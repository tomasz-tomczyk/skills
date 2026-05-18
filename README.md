# Skills

Agent skills I've built and use. Compatible with [Claude Code](https://claude.com/product/claude-code), Codex, Cursor, and other [skills.sh](https://skills.sh)-compatible agents.

## Available Skills

| Skill | Description |
| --- | --- |
| [crit](skills/crit) | Review code, plans, diffs, or a running web app with crit inline comments. Runs the interactive review loop, then reads and addresses structured feedback. Also covers programmatic commenting and GitHub PR sync. |
| [design-explore](skills/design-explore) | Generate multiple design prototypes for a UI feature in parallel, each inspired by a different company's design language. Pick one, adapt it. |
| [mom-test](skills/mom-test) | Generate Mom Test-style interview questions for validating feature requests and product ideas. Asks about past behavior, not hypothetical futures. |
| [startup-email](skills/startup-email) | Write or rewrite transactional and growth emails (invitations, onboarding, referrals) using YC/Airbnb email copy principles. Personal subject lines, single CTA, social proof. |
| [dev-copy](skills/dev-copy) | Audit and improve marketing copy aimed at engineers — READMEs, landing pages, feature pages, tweets. Based on Seibel's YC pitch framework adapted for dev tools. Clarity over sizzle. |

---

## mom-test

Generate [Mom Test](https://www.momtestbook.com/)-style interview questions for validating feature requests, user feedback, and product ideas. Based on Rob Fitzpatrick's principle: ask about past behavior, not hypothetical futures.

### Install

```shell
npx skills add tomasz-tomczyk/skills --skill mom-test -g -y
```

Or browse on [skills.sh](https://skills.sh/tomasz-tomczyk/skills/mom-test).

### What it does

Give it a feature request (GitHub issue, Linear ticket, Slack message, or plain description) and it generates targeted questions across six categories:

1. **Current workflow** — how they do things today
2. **Pain** — how much the workaround actually costs them
3. **Stakes** — whether it matters enough to change behavior
4. **Solution assumptions** — whether the proposed solution matches the real need
5. **Alternatives rejected** — what they've tried and abandoned
6. **Sharing vs. personal** — whether the artifact is for them or shared with others

Each question asks about specific past events, not imagined futures. Questions that can't be lied to.

### When it fits

- You received a feature request and want to validate it before committing
- You're preparing for a user interview or discovery call
- You want to stress-test whether a problem is real or hypothetical

### When it's the wrong tool

- You have quantitative data (analytics, usage logs) — look at that first
- The requester is reporting a bug — just fix it
- You're interviewing yourself

---

## design-explore

Generate multiple competing design prototypes for a UI feature, each inspired by a different company's design language. Each prototype is built in parallel by a separate agent, lives as a standalone HTML file with a dark/light toggle, then gets adapted to your real codebase once you pick a winner.

### Install

```shell
npx skills add tomasz-tomczyk/skills --skill design-explore -g -y
```

Or browse on [skills.sh](https://skills.sh/tomasz-tomczyk/skills/design-explore).

### What it does

Give it a component (a comment thread, a file tree, a theme picker) and a list of companies (defaults: GitHub, Linear, Raycast, Vercel, Monzo). It will:

1. Read your current implementation, extract the theme variables
2. Build a base HTML file mirroring the component in dark + light modes
3. Spawn one agent per company, **all in parallel**, each told to research that company's design language and apply it to your component
4. Hand you a folder of standalone HTMLs to compare side by side
5. Adapt the winner to your real codebase using your actual theme variables

### When it fits

- The codebase has a real theme system (CSS variables, light/dark) and standalone HTML can mirror it
- The feature is a self-contained component (panel, toggle, picker, thread, card) — not a whole page
- You want creative range, not a precise restyle

### When it's the wrong tool

- You already have exact specs (hex codes, fonts, spacing) — that's an implementation request, just implement it
- The component lives in a heavy framework binding it to a design system that resists standalone reproduction
- The feature is a full page — too much surface for parallel exploration

### Tips for better results

- Give specific companies. "Stripe, Figma, Notion, Arc" produces wildly different output than the defaults.
- The more context the agents have about the feature's purpose and users, the bolder the designs. Volunteer it up front.
- Open the base HTML before agents dispatch to confirm it actually matches the current implementation. Saves a re-run later.

---

## startup-email

Write or rewrite transactional and growth emails (invitations, onboarding, referrals, re-engagement) using startup email copy principles from YC/Airbnb. Based on Gustaf Alstromer's YC growth talk and the Airbnb referral email — one of the highest-converting referral emails ever measured.

### Install

```shell
npx skills add tomasz-tomczyk/skills --skill startup-email -g -y
```

Or browse on [skills.sh](https://skills.sh/tomasz-tomczyk/skills/startup-email).

### What it does

Give it an email to write or an existing email to rewrite. It applies the Airbnb referral pattern:

1. **Subject line** — `{Person} invited you to {Product}` (personal, not branded)
2. **From name** — `{Person} via {Product}` (social, not corporate)
3. **Headline** — Bold, first-name, value-first
4. **Body** — One sentence on what the product does + bold deadline for urgency
5. **Single CTA** — One button, one action
6. **Social proof** — Inviter's avatar + name + tenure ("On Product since May 2025")

Includes a checklist, HTML email essentials (preheader, viewport, inline styles), and clear do/don't tables.

### When it fits

- Writing invitation, referral, or onboarding emails
- Rewriting an existing transactional email for better conversion
- Reviewing email copy before shipping

### When it's the wrong tool

- Marketing newsletters (different compliance and tone rules)
- Password reset / verification emails (pure utility)
- You have A/B test data that says otherwise — data beats principles

---

## crit

Review code changes, plans, live pages, or local HTML files with [`crit`](https://github.com/tomasz-tomczyk/crit) — a browser-based inline-comment review tool. The skill runs the full review loop: launch crit, wait for the human to leave GitHub-style comments, read the structured feedback, address each comment, and start the next round.

### Install

```shell
npx skills add tomasz-tomczyk/skills --skill crit -g -y
```

Or browse on [skills.sh](https://skills.sh/tomasz-tomczyk/skills/crit).

Requires the `crit` binary on PATH — see the [crit README](https://github.com/tomasz-tomczyk/crit) for install instructions.

### What it does

1. Launches `crit` against a plan, a diff, a GitHub PR, a running dev server, or a local HTML file — auto-detecting the mode
2. Blocks until the human clicks "Finish Review" in the browser
3. Reads the structured review JSON and identifies unresolved comments
4. Revises the referenced files and replies inline with what changed
5. Signals round-complete and loops until the review is approved

Also covers authoring comments programmatically (`crit comment`, including bulk `--json`), sharing reviews (`crit share`), and syncing to/from GitHub PRs (`crit pull` / `crit push`).

### When it fits

- You wrote a plan or made code changes and want structured human feedback before proceeding
- A workflow requires a review pass before accepting non-trivial changes
- You're leaving inline comments on code, or syncing reviews with GitHub PRs

### When it's the wrong tool

- You just need a quick yes/no — ask the user directly
- The change is trivial enough that a formal review pass is overhead

---

## dev-copy

Audit and improve marketing copy aimed at engineers — READMEs, landing pages, feature pages, tweets, launch posts. Based on Michael Seibel's YC pitch framework adapted for developer tools: clarity over sizzle, insights over feature lists, specific examples over vague descriptions.

### Install

```shell
npx skills add tomasz-tomczyk/skills --skill dev-copy -g -y
```

Or browse on [skills.sh](https://skills.sh/tomasz-tomczyk/skills/dev-copy).

### What it does

Give it a README, homepage, or any engineer-facing copy and it audits against 10 principles:

1. **Two sentences + specific example** — does the reader know what this does in 10 seconds?
2. **Insight over features** — does it teach something non-obvious, or just list capabilities?
3. **Order by impressiveness** — is the strongest material up front?
4. **Specific example in every section** — scenarios, not descriptions
5. **Credibility without life stories** — flat credentials, dogfooding signals
6. **Traction = velocity** — momentum with timeframes, not vanity metrics
7. **Explicit low-friction CTA** — install command, not "learn more"
8. **Conversation, not book report** — directness, real objections addressed
9. **Self-contained pages** — works for someone arriving from search with zero context
10. **No misleading claims** — technical accuracy matters, engineers verify

Reports findings in a table with pass/fail per section and concrete suggestions.

### When it fits

- Writing or rewriting a README for an open source dev tool
- Auditing a landing page or homepage before launch
- Reviewing feature pages, integration pages, or mode pages
- Drafting tweets, launch posts, or HN comments

### When it's the wrong tool

- Internal docs or READMEs for internal tools
- API reference / technical docs (accuracy over clarity tradeoffs reverse)
- Copy aimed at non-technical buyers (VPs, procurement)
- The user explicitly wants a feature list, not insight-driven copy
