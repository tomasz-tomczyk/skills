---
name: dev-copy
description: Audit and improve marketing copy aimed at engineers — READMEs, landing pages, feature pages, tweets, launch posts. Based on Michael Seibel's YC pitch framework adapted for developer tools. Use when asked to "review copy", "audit the homepage", "improve the README", "check the marketing", or when writing new marketing content for a dev tool.
---

# Dev Copy — Engineer-Facing Marketing

Audit or write marketing copy for developer tools using these principles. Engineers have finely tuned BS detectors — the goal is clarity and insight, not sizzle.

## Core Rule

**You stand out by being concise and easy to understand.** Not by energy, pizzazz, or shark-tankiness. Clear and specific beats clever and vague every time.

## The Checklist

Run every piece of copy through these checks. Report what passes and what doesn't.

### 1. Two sentences + specific example

The reader must know what the product does after reading two sentences. Then a vivid, specific example that makes them think "I had that exact problem yesterday."

**Good:** "Teleport lets you SSH into any server in your infrastructure without managing keys. Imagine you're paged at 2am, you need to get into the prod database box, but your SSH key expired last week and nobody's around to rotate it."

**Bad:** "A next-generation identity-aware infrastructure access platform."

Rules:
- 80% accurate, 100% clear — not the other way around
- The example needs specific details: a person, a situation, a number, an outcome
- Vague examples ("if you have a project and need to do X...") don't stick
- Never leave the first screen / first paragraph without the reader knowing what it does

### 2. Insight over features

Feature lists describe WHAT. Insights explain WHY — and position you as an expert.

**Good:** "Most database queries aren't slow because of the query. They're slow because the ORM generated a join you didn't ask for."

**Bad:** "Query optimizer. Connection pooling. Automatic indexing."

Rules:
- A unique insight is something <50% of your audience would agree with before you explain it
- Use numbers and facts to make insights concrete
- Tell the story of how you learned the insight (when possible)
- You only earn the right to share insights after the reader knows what you do

### 3. Order by impressiveness

Put the strongest material right after the reader knows what you do. You earn every 10 seconds of a developer's attention — if the last 10 seconds were weak, they're gone.

Rules:
- "What you do" goes first, CTA goes last, everything in between is ordered by strength
- If traction is your best asset, put it right after the opener
- If unique insights are strongest, lead with those
- Don't follow a rigid template if it buries your best material

### 4. Specific example in every section

Every section should have a concrete scenario, not just a description.

**Good feature blurb:** "Your CI pipeline just failed on a flaky test for the third time this week. Buildkite reruns only the failed step — you don't rebuild the whole thing."

**Bad feature blurb:** "Selective step retries. Parallel execution. Artifact caching."

The pattern: start with "You just..." or "Imagine you..." to put the reader in a moment they recognize.

### 5. Credibility without life stories

State credentials flatly in one sentence. Show you personally experience the problem.

**Good:** "Former SRE at Stripe. I run this on every deploy."

**Bad:** A three-paragraph founder origin story about discovering the problem space.

Rules:
- If you have an impressive credential, say it — don't bury it (the "Mars Rover" rule: one YC founder built software for the Mars rover and never mentioned it)
- Dogfooding signals ("I use it daily") are the strongest credibility for dev tools
- No life stories unless explicitly asked
- "Experienced the problem" > "saw the opportunity"

### 6. Traction = velocity, not vanity

Communicate speed and momentum, always with timeframes.

**Good:** "Built and shipped in 3 months. 500 stars in the first week."

**Bad:** "500 stars." (no timeframe — could be 3 years)

Rules:
- Always include how long things took
- Release cadence is a traction signal (shipping weekly > big launch once)
- Fake traction (advisor surveys, waitlist numbers) is worse than no traction — skip the section entirely
- Pre-launch traction is fine: "built the prototype in 2 weeks, 100 beta users in the first month"

### 7. The ask must be explicit and low-friction

Every piece of content needs a clear CTA. Make it trivially easy to try.

**Good:** End with the install command and a concrete first use: `npx create-my-tool` then "run `my-tool init` in any project."

**Bad:** "Check out our GitHub for more information."

Rules:
- The CTA should feel like the default action, not a commitment
- "30-second install. No account, no config, no dependencies." — each word removes an objection
- Don't end with "learn more" when you could end with the install command

### 8. Conversation, not book report

Copy should feel like talking to a smart colleague, not reading a press release.

Rules:
- Draw the reader in with questions they'd actually ask ("Why not just use grep? Because grep doesn't understand scope.")
- FAQ sections that address real objections are high-value
- Let users/testimonials say things you can't say about yourself
- Match tone to the audience — engineers respect directness, distrust polish

### 9. Self-contained pages

Every page must work for someone arriving from search with zero context.

Rules:
- Don't reference other pages/features as if the reader has seen them ("Same as the Pro plan" assumes they've read the pricing page)
- Each page should re-state what the product does in one line before diving into specifics
- Section headers should be meaningful to a cold reader ("How it works" > "The loop")
- Meta titles and descriptions must match the current page content — stale meta tags are the equivalent of an investor not knowing what your company does after slide one

### 10. No misleading claims

Technical accuracy matters. Engineers verify claims and remember when you lied.

Rules:
- Don't say "zero runtime overhead" if there's a 2ms per-request cost
- Don't say "no dependencies" if there's a runtime requirement you're not counting
- Qualify honestly: "local by default — cloud sync is opt-in" is more compelling than a vague "privacy-first"
- Under-promise concrete things, over-deliver on the experience

## How to Use This Skill

### Auditing existing copy

Read each page/section and check it against the 10 points above. Report findings as:

| Section | Passes | Issue | Suggestion |
|---|---|---|---|
| Hero | 1, 7, 8 | Missing specific example (#4) | Add a scenario after the tagline |

### Writing new copy

1. Start with two sentences + specific example
2. List 2-3 unique insights (non-obvious things about the problem/solution)
3. Order sections by impressiveness
4. Add a scenario to each section
5. End with explicit, low-friction CTA
6. Read it cold — does a stranger know what this does after 10 seconds?

## When This Skill Is Wrong

- Internal docs, READMEs for internal tools — skip the marketing framing
- API reference / technical docs — accuracy over clarity tradeoffs reverse
- Copy aimed at non-technical buyers (VPs, procurement) — different rules apply
- The user explicitly wants a feature list, not insight-driven copy
