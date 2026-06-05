---
name: oban-jobs
description: Battle-tested gotchas and debugging recipes for Oban background jobs in Elixir (including Oban Pro). Use when writing or debugging Oban workers, queues, batches, or cron jobs; when a custom Oban instance name raises "No Oban instance named ...", when jobs run forever or get discarded, when a queue won't pause/resume or resets on deploy, when REINDEX/long DB ops deadlock under concurrency, when a batch callback never fires, or when inspecting job/queue state in a running node.
---

# Oban Jobs — gotchas & debugging

Hard-won lessons. Each is a real failure mode, not theory. Verify against the running node before acting.

## 1. Custom instance name resolves wrong inside a worker

`use Oban.Worker` and `use Oban.Pro.Workers.Batch` inject `alias Oban.{Job, Worker}`. So if your Oban instance's **first module segment is `Job` or `Worker`** (e.g. an instance named `Worker.Oban`, common when the OTP release is `worker`), a bare reference resolves to the **non-existent `Oban.Worker.Oban`** → `No Oban instance named Oban.Worker.Oban`.

```elixir
use Oban.Pro.Workers.Batch, queue: :reindex

# WRONG inside the module — `Worker.` collides with the injected `alias Oban.Worker`
Oban.pause_queue(Worker.Oban, queue: :reindex)

# RIGHT — the `Elixir.` prefix bypasses the injected alias
@oban_name Elixir.Worker.Oban
Oban.pause_queue(@oban_name, queue: :reindex)
```
Instance names that don't start with `Job`/`Worker` (e.g. `MyApp.Oban`) are unaffected.

## 2. Jobs have NO timeout by default

`Oban.Worker.timeout/1` defaults to `:infinity`. Oban will **never** kill a long-running `perform/process` — it waits forever. Bound long operations yourself:

```elixir
# Oban won't stop this; the query/connection timeout will.
SQL.query!(Repo, "REINDEX INDEX CONCURRENTLY #{idx}", [], timeout: to_timeout(hour: 1))
```
Add `def timeout(_job), do: to_timeout(minute: 30)` only if you want Oban to enforce a ceiling (e.g. free a queue slot from a stuck job).

## 3. Queue pause state is ephemeral with static queues

With a static `queues: [...]` config, `paused: true` is only the **boot-time** state. Runtime `pause_queue`/`resume_queue` is in-memory per-node and **does NOT survive a restart/deploy** — the queue boots back to the config default. A manual resume gets silently undone by the next deploy.

Persistence requires **Oban Pro DynamicQueues**, which stores state in `oban_queues`. It's **all-or-nothing**: it replaces the static `:queues` list (mixing static + dynamic is unsupported), so you can't make just one queue persistent.

## 4. Serialize operations that deadlock in parallel

`global_limit` (cluster-wide) / `local_limit` (per-node) cap concurrency. Operations that contend on the same locks deadlock when run in parallel — e.g. multiple `REINDEX INDEX CONCURRENTLY` on related tables. Force serialization:

```elixir
reindex: [global_limit: 1, paused: true]
```

## 5. Batch callbacks only fire when the whole batch is terminal

`handle_completed` fires when **all** jobs complete; `handle_exhausted` fires when all jobs are terminal but not all completed. Neither fires while any job is still `available`/`scheduled`. A circuit-breaker (`if failures >= N, do: pause_queue`) placed in those callbacks will **not** trip mid-run — if you see a pause with jobs still `available`, the breaker is NOT the cause (look at the boot default / a deploy instead).

## 6. Inspecting state in a running node

```elixir
# Live producer state: paused?, started_at, global/local limits, running jobs
Oban.check_queue(MyApp.Oban, queue: :reindex)

# Jobs by worker + state
import Ecto.Query
Repo.all(from j in Oban.Job,
  where: j.worker == "MyApp.MyWorker",
  select: %{id: j.id, state: j.state, args: j.args, attempt: j.attempt})
```

**Reading a discard:** several jobs `discarded` at the **same timestamp** with **empty `errors`**, plus a producer `started_at` **after** that timestamp = a graceful pod shutdown orphaned the in-flight jobs. With `max_attempts: 1` an orphaned attempt goes straight to `discarded` (no error recorded) — it's not a code failure. A single `discarded` with a real error (e.g. `40P01 deadlock_detected`) is the genuine failure.

## 7. Enqueuing & running

```elixir
# Arg order: INSTANCE first, then changeset
Oban.insert(MyApp.Oban, MyWorker.new(%{foo: 1}))

# Run inline NOW — bypasses the queue AND the unique constraint (great for manual/dev testing)
MyWorker.perform(%Oban.Job{args: %{}})
```
`unique: [period: ...]` dedupes re-inserts within the window (returns the existing job, `conflict: true`) — re-inserting won't run it again; delete the existing job or run inline to force. Narrow it with `keys: [:user_id]` / `states: [...]`.

## 8. Args are JSON — string keys, IDs not structs

Args round-trip through JSON, so `perform`/`process` receives **string keys** and only JSON-safe values:

```elixir
# Enqueue with atoms is fine, but match string keys:
def perform(%Oban.Job{args: %{"user_id" => id}}), do: ...   # NOT %{user_id: id}
```
Store **references, not data**: `%{user_id: 1}`, never `%{user: %User{}}` (a struct won't serialize, and stale data rots between enqueue and run). Don't put large blobs in args — store a path/ID and re-fetch.

## 9. Return value decides retry vs. stop

| Return | Resulting state |
|---|---|
| `:ok` / `{:ok, _}` | `completed` |
| `{:error, reason}` / raise | `retryable` (backoff, until `max_attempts` → `discarded`) |
| `{:cancel, reason}` | `cancelled` (permanent, no retry) |
| `{:snooze, seconds}` | `scheduled` (re-run later) |

Use `{:cancel, _}` for unrecoverable cases (entity deleted) so you don't burn retries. Customise backoff with `def backoff(%Job{attempt: a})`.

⚠️ **Oban Pro Smart Engine: `{:snooze, _}` rolls back the `attempt` counter.** Guarding snoozes on `attempt` loops forever. Track in meta instead:

```elixir
# WRONG — infinite snooze loop under Smart Engine
def process(%Job{attempt: a}) when a <= 3, do: {:snooze, 5}

# RIGHT
snoozed = Map.get(job.meta, "snoozed", 0)
if snoozed < 3, do: {:snooze, 5}, else: {:cancel, "max snoozes"}
```

## 10. Jobs retry by default → must be idempotent

`max_attempts > 1` means any job can run more than once (retry, or a rescued orphan). Make `perform` safe to repeat — guard side effects with an idempotency key (esp. payments/external calls):

```elixir
case Payments.find_by_idempotency_key(key) do
  {:ok, existing} -> {:ok, existing}
  :not_found -> Payments.charge(user_id, amount, idempotency_key: key)
end
```

The **Lifeline** plugin (`rescue_after:`) is what reclaims jobs orphaned by a crashed/restarted node (see #6) — it moves stuck `executing` jobs back to `available` if attempts remain, else `discarded`. That's why a `max_attempts: 1` orphan lands straight in `discarded`.

---

*Companion reference:* for the full pattern catalogue (worker options, queue/pool sizing, cron, Smart Engine limits, Workflows/Batches/Chunks, testing with `perform_job`/`assert_enqueued`/`drain_jobs`, `args_schema`), see [oliver-kriska/claude-elixir-phoenix → oban](https://github.com/oliver-kriska/claude-elixir-phoenix/tree/main/plugins/elixir-phoenix/skills/oban). This skill is the gotchas; that one is the reference.
