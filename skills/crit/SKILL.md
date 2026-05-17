---
name: crit
description: Review code changes, a plan, a live page (running dev server), or a local HTML file with crit inline comments. Use when asked to review code, a plan, a diff, or a running web app, when you want structured human feedback on your work, or when authoring crit comments programmatically. Covers the interactive review loop plus crit comment, crit share, and GitHub PR sync.
---

# Review with Crit

Review and revise code changes, plans, live pages (running dev servers, staging URLs), or local HTML files using [`crit`](https://github.com/tomasz-tomczyk/crit) for inline comment review.

`crit` opens a browser-based review surface where a human leaves GitHub-style inline comments, then writes them to a structured JSON file you read and act on.

## Prerequisite

The `crit` binary must be on PATH. If it is missing, point the user at the install instructions in the [crit README](https://github.com/tomasz-tomczyk/crit). GitHub PR sync additionally needs the `gh` CLI installed and authenticated.

## Step 1: Pass arguments to `crit`

The CLI auto-detects the review mode from its arguments. **Do not ask the user which mode to use.** Pass arguments through:

```
crit <arguments>               # file, dir, URL, .html — CLI auto-detects mode
crit --pr <num|url>            # GitHub PR (range mode)
crit --range <base>..<head>    # commit range (range mode)
crit                           # no args → branch diff
```

If no arguments, check conversation context:

1. A plan file was written earlier in this conversation → `crit <plan-file>`
2. Otherwise → bare `crit` (branch diff)

## Step 2: Launch crit and block until review completes

**CRITICAL — you MUST run this step. Do NOT skip it. Do NOT proceed without it.**

Run `crit` in the foreground and block until it exits:

```bash
crit <plan-file>   # specific file
crit               # git mode
```

If a crit server is already running from earlier in this conversation, `crit` automatically connects to it. Starting from scratch, it spawns the daemon, opens the browser, and blocks until the user clicks "Finish Review".

`crit` prints the review URL on startup (e.g. `Started crit daemon at http://localhost:<port>`). Relay it verbatim:

> **"Crit is open at http://localhost:<port>. Leave inline comments, then click Finish Review."**

**Do NOT proceed until `crit` completes.** Do NOT ask the user to type anything. Do NOT read the review file early. Wait for the foreground command to finish — that is how you know the human is done reviewing.

## Step 3: Read the review output

When `crit` completes, its stdout includes the path to the review file (e.g. "Review comments are in /path/to/review.json"). Read it.

The file contains structured JSON. Three comment scopes:

- `review_comments` (top-level array, `r_`-prefixed IDs) — general feedback
- File comments (per-file `comments` array, `start_line: 0`) — about the file as a whole
- Line comments (per-file `comments` array, with `start_line`/`end_line`) — about specific lines

Identify all comments where `resolved` is `false` or **missing** — both mean unresolved. Only `true` means resolved.

When a comment has these fields:

- `quote`: the specific text the reviewer selected — focus your changes on the quoted text rather than the entire line range
- `anchor`: full text of the commented lines when placed — use it to locate the current position; line numbers may be stale after edits
- `drifted: true`: original content was removed or heavily rewritten — line numbers are approximate at best
- `replies`: check before acting — if you already replied, the reviewer may be following up rather than requesting a new change

### Review file format

```json
{
  "review_comments": [
    {
      "id": "r_f1e2d3",
      "body": "Overall the architecture looks good",
      "scope": "review",
      "author": "User Name",
      "resolved": false,
      "replies": [
        { "id": "rp_b4a5c6", "body": "Thanks, addressed it", "author": "Agent" }
      ]
    }
  ],
  "files": {
    "path/to/file.go": {
      "comments": [
        {
          "id": "c_a1b2c3",
          "start_line": 5,
          "end_line": 10,
          "body": "Comment text",
          "quote": "the specific words selected",
          "anchor": "The sessions table needs a complete rewrite...",
          "author": "User Name",
          "resolved": false,
          "replies": []
        }
      ]
    }
  }
}
```

## Step 4: Address each review comment

For each unresolved comment:

1. Understand what the comment asks for
2. If it contains a suggestion block, apply that specific change
3. Revise the referenced file (plan or code file from the diff)
4. Reply with what you did: `crit comment --reply-to <id> --author '<your name>' '<what you did>'` (reply bodies support markdown)
5. **Do not pass `--resolve`.** Resolving is the reviewer's call. Only add `--resolve` if the user explicitly asks.

Editing the plan or code file triggers Crit's live reload — the user sees changes in the browser immediately.

### Replying to multiple comments

Use `--json` for a single bulk call instead of one invocation per comment:

```bash
echo '[
  {"reply_to": "c_a1b2c3", "body": "Fixed"},
  {"reply_to": "c_d4e5f6", "body": "Refactored as suggested"}
]' | crit comment --json --author '<your name>'
```

**If there are zero review comments**: inform the user no changes were requested and stop.

## Step 5: Signal completion and start the next round

**CRITICAL — you MUST run this step. Do NOT skip it. Do NOT proceed without it.**

When Step 2's `crit` command exits with feedback, it prints `Next round: crit <args>` to stdout. Run that command verbatim — the daemon is keyed by args, so mismatched args spawn a new daemon instead of reconnecting.

On subsequent calls, `crit` automatically signals round-complete first, then blocks until the next "Finish Review" click.

Tell the user: **"Changes applied. Review the diff in your browser and click Finish Review when ready."**

**Do NOT proceed until `crit` completes.** When it does, return to Step 3. If the user finishes with zero comments, the review is approved — stop the loop and proceed.

## Authoring comments programmatically

Beyond replies, you can author original comments — useful when reviewing someone else's work or in multi-agent workflows.

```bash
crit comment --author '<your name>' '<body>'                       # review-level (general feedback)
crit comment --author '<your name>' <path> '<body>'                # file-level (whole file)
crit comment --author '<your name>' <path>:<line> '<body>'         # single line
crit comment --author '<your name>' <path>:<start>-<end> '<body>'  # line range
crit comment --reply-to <id> --author '<your name>' '<body>'       # reply
```

Hard rules:

- **Pass `--author '<your name>'`** so comments are attributed correctly (use the name of the agent or tool you are). If omitted, crit falls back to the configured VCS user name.
- **Always single-quote the body** — double quotes break on backticks and shell metacharacters.
- **Line numbers reference the file on disk** (1-indexed), not diff line numbers.
- **Only pass `--resolve` when the user explicitly asks.** Never resolve proactively.

### Bulk commenting (3+ comments)

Use `--json` for atomicity (single write, no partial state) and speed. JSON comes from stdin or `--file <path>`:

```bash
echo '[
  {"body": "overall feedback", "scope": "review"},
  {"path": "session.go", "body": "restructure", "scope": "file"},
  {"file": "src/auth.go", "line": 42, "body": "Missing null check"},
  {"file": "src/auth.go", "line": "50-55", "body": "Extract to helper"},
  {"reply_to": "c_a1b2c3", "body": "Fixed — added null check"}
]' | crit comment --json --author '<your name>'
```

For multi-paragraph bodies, prefer `--file` — a literal newline inside a `"body"` string breaks JSON parsing. Write the JSON to a temp file, then `crit comment --json --file /tmp/crit-bulk.json --author '<your name>'`.

Per-entry fields: `file`/`path` (relative path; `path` alone → file-level), `line` (`42` or `"45-47"`), `body` (always required), `author` (per-entry override), `scope` (`review`/`file` — usually inferred), `reply_to` (comment ID), `resolve` (only when the user asks).

If `crit comment` errors with "comment found in multiple files", disambiguate with `--path <file>` (or set `file` on the JSON entry). Review-level IDs (`r_…`) are globally unique and never need this.

## Sharing

If the user asks for a URL, a shareable link, or to share the review:

```bash
crit share <file> [file...]   # Upload and print URL
crit share --qr <file>        # Also print QR code (terminal only)
crit unpublish                # Remove the shared review
```

- **Always relay the full output** — copy the URL (and QR if used) directly into your response. Don't make the user dig through tool output.
- **`--qr` is terminal-only** — skip it in mobile apps or web chat UIs, where Unicode block characters won't render.
- If a review file exists, comments for the shared files are included automatically.
- Unpublish uses the persisted delete token in the review file — no extra args needed.

## GitHub PR integration

```bash
crit pull [pr-number]                                    # Fetch PR review comments into the review file
crit push [--dry-run] [--event <type>] [-m <msg>] [pr]   # Post review comments as a GitHub PR review
```

Requires the `gh` CLI installed and authenticated. PR number is auto-detected from the current branch. `--event` values: `comment` (default), `approve`, `request-changes`. `-m` adds a review-level body message.
