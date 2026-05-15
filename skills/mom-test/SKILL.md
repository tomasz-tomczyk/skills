---
name: mom-test
description: Use when validating a feature request, user feedback, or product idea. Generates Mom Test-style questions that ask about past behavior instead of hypothetical futures. Use when asked to "validate this idea", "mom test questions", "interview questions for this feature", or given a feature request to evaluate.
---

# Mom Test Questions

Generate questions that follow Rob Fitzpatrick's *The Mom Test* rules: ask about the person's life and past behavior, not your idea. Good questions can't be lied to.

## The Three Rules

1. Talk about **their life**, not your idea
2. Ask about **specifics in the past**, not hypotheticals about the future
3. Talk less, listen more

## Inputs

When invoked, you need:

- **The idea/feature/request**: a GitHub issue, Linear ticket, Slack message, or plain description (REQUIRED)
- **Your hidden question**: what the user actually wants to validate — ask if not obvious from context (OPTIONAL but valuable)

## Question Categories

Generate 2-3 questions per category. Not all categories apply to every situation — skip irrelevant ones.

### Current workflow
How they do things today. Reveals whether the problem is real and frequent.

- "Walk me through the last time you [did the thing the feature addresses]. What happened?"
- "How often does [situation] come up?"
- "What did you do when [the gap] happened?"

**Pattern:** "Walk me through..." and "The last time you..." force specifics.

### Pain
How much the current situation actually costs them. Reveals severity.

- "How much time did you spend [workaround] last time?"
- "Have you tried any workarounds?"
- "What happened when [downstream person] couldn't [thing the feature would enable]?"

**Pattern:** Ask about consequences they've already experienced, not ones they imagine.

### Stakes
Whether this matters enough to change behavior. Separates "nice to have" from "need to have."

- "If [tool] never supports [feature], what would you change about your workflow?"
- "How many of your recent [sessions/projects/sprints] hit this?"
- "Who else on your team runs into this?"

**Pattern:** "If we never built this..." reveals true priority. Low-stakes answers: "I'd just keep doing what I do."

### Solution assumptions
Whether the requester's proposed solution matches the actual need. People jump to solutions — dig for the underlying problem.

- "When you say [their proposed solution], would you still want to [deeper interaction], or is [simpler thing] enough?"
- "What kind of [input] are we talking about — [simple case], [complex case], or [edge case]?"

**Pattern:** Offer a deliberately reduced version. If they accept it, the real need is smaller than the request.

### Alternatives already rejected
What they've tried and abandoned. Reveals hidden constraints.

- "Have you looked at other tools for [this]? What was wrong with them?"
- "You mentioned [alternative approach] — what made you move away from that?"

**Pattern:** Past tool-switching decisions reveal real priorities better than stated preferences.

### Sharing vs. personal
Whether the artifact is for them alone or shared with others. Changes the feature's scope entirely.

- "Who else sees [the artifact] besides you?"
- "Walk me through what happens to [the artifact] after you're done with it — does it go anywhere?"
- "If I looked at your last [project] that had [the artifact], would [other person] have opened it?"

**Pattern:** Ask where the artifact goes, not whether they share it. The third form grounds it in a specific past instance.

## Writing Good Questions

| Do | Don't |
|---|---|
| Ask about past behavior | Ask about future intentions |
| Use "the last time you..." | Use "would you ever..." |
| Ask who else is affected | Ask if others might be interested |
| Offer a reduced version | Ask if they want the full version |
| Ask what they did when X happened | Ask what they'd do if X happened |
| Ground in a specific instance | Accept vague generalities |

## Output Format

Group questions by category with a brief note on what each group validates. End with a one-line note identifying the single most important thing to validate (the "if this is false, nothing else matters" question).

## When This Skill Is Wrong

- You have quantitative data (analytics, usage logs) — look at that first
- The requester is reporting a bug, not requesting a feature — just fix it
- You're interviewing yourself — Mom Test is for external validation
