---
name: startup-email
description: Write or rewrite transactional and growth emails (invitations, onboarding, referrals, re-engagement) using startup email copy principles from YC/Airbnb. Use when asked to "write an email", "rewrite this email", "invitation email", "onboarding email", or when reviewing email copy for conversion.
---

# Startup Email Copy

Write transactional and growth emails that convert. Based on patterns from YC-backed companies: Airbnb's referral email (Gustaf Alstromer), Dropbox's referral program (Drew Houston), and invitation emails from Stripe, Linear, and Brex.

## Core Principle

Every email competes with 50 others in an inbox. You get the subject line and one preview sentence to earn the open. You get the headline and one paragraph to earn the click. Everything else is noise.

## The Airbnb Referral Email Pattern

The highest-converting referral email ever measured at Airbnb followed this exact structure:

1. **Subject line** — `{Person} invited you to {Product}` (personal, from a human, not a brand)
2. **From name** — `{Person} via {Product}` (feels like a forwarded message, not marketing)
3. **Headline** — Bold, personal, value-first: `{Person} sent you $40 for your first trip!`
4. **Body** — One sentence explaining what the product does + a bold deadline for urgency
5. **Single CTA** — One button, one action. No secondary links competing for attention.
6. **Social proof footer** — The inviter's photo, name, and tenure (`On Airbnb since 2009`). Builds trust — a real person you know vouches for this.

## The Dropbox Lesson: Tie to Something Concrete

Dropbox referrals drove 35% of daily signups at peak. The key insight: emails tied to a specific shared folder ("Alex wants to share 'Q3 Designs' with you") converted far better than abstract product invitations. When Dropbox A/B tested reward amounts, doubling the free storage didn't double conversions — the social trigger (friend's name + shared content) mattered more than the incentive.

**Apply this:** If the invitation has context (a workspace, a project, a review), name it. `{Name} invited you to join {Org}` beats `{Name} invited you to {Product}`.

## The Brex/Linear Lesson: Less Copy Converts More

Brex A/B tested their invite emails and found that **removing all feature descriptions** and keeping only the social invitation increased click-through rates. Linear follows the same pattern — one line of context, one button, nothing else.

For developer tools especially: under 50 words in the body. Developers scan, they don't read marketing.

## Rules

### Subject Line

| Do | Don't |
|---|---|
| Lead with a person's name | Lead with your product name |
| State the action: "invited you", "shared with you" | Use passive voice: "You've been invited" |
| Keep under 40 characters — 6 words or fewer (mobile truncation at ~50, but shorter wins) | Add brackets, emojis, or ALL CAPS |
| Make it feel like a notification, not marketing | Use "Re:" or "Fwd:" tricks |

**Pattern:** `{Name} {action} you to {thing}` — this reads as a personal notification in every inbox.

### From Name

Use `{Person} via {Product}`, not `{Product}` alone. The "via" pattern:
- Gets past spam filters better (person name signals relationship)
- Stands out in inbox (most emails come from brand names)
- Creates social obligation (someone you know sent this, not a robot)

### Headline

The headline is the subject line expanded, not repeated. It should:
- Use the inviter's **first name** (familiar, not formal)
- State the value or action clearly
- End with energy (exclamation point is fine here — it's an invitation)

**Pattern:** `{First name} invited you to join {Org}!`

### Body Copy

One paragraph. Two sentences maximum. It must:

1. **Explain what the product does** — assume the reader has never heard of it. One sentence.
2. **Create urgency with a bold deadline** — `Just accept by **May 22, 2026** to join as a member.`

Don't:
- List features (this is not a landing page)
- Explain how things work (they'll find out after clicking)
- Add multiple paragraphs (every line is a chance to lose them)
- Use jargon the reader might not know

The body copy should make sense to someone who has never visited your site. The inviter is vouching for your product to their colleague — write copy worthy of that trust.

### CTA Button

One button. One action. Nothing else.

- Text: action verb, not destination. `Accept invitation` not `Go to dashboard`
- Visually prominent: high contrast, generous padding (14px 28px minimum)
- No secondary links above the fold — every extra link steals clicks from the primary CTA

### Social Proof Section

Below a divider, show the inviter as a real person:

- **Avatar** (or initial circle fallback)
- **Full name**
- **Tenure line**: `On {Product} since {month year}` — mirrors Airbnb's pattern, builds trust

This section answers the unconscious question: "Is this legit?" A real photo and a tenure date say yes.

### Footer

Minimal. One line: "If you didn't expect this email, you can safely ignore it."

Don't:
- Repeat the expiry (it's in the body)
- Add unsubscribe links to transactional emails (not required by CAN-SPAM/GDPR for one-time relationship-initiated messages, and confuses recipients)
- Add your company address (required for marketing emails, not transactional)

## Preheader Text

The hidden preview text that appears after the subject in inbox list views. Without it, email clients pull the first visible text (often your logo text or "View in browser").

Add a hidden div as the first element in `<body>`:
```html
<div style="display: none; max-height: 0; overflow: hidden;">
  {First name} invited you to join {Org} — accept by {date}
</div>
```

Keep it under 100 characters. It should reinforce the subject line, not repeat it.

## HTML Email Essentials

Not about copy, but these prevent your good copy from rendering as garbage:

- `lang="en"` on `<html>` — accessibility
- `<meta name="viewport" content="width=device-width, initial-scale=1.0">` — mobile rendering
- `<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">` — character encoding
- `role="presentation"` on layout tables — screen readers
- All styles inline — Gmail strips `<style>` blocks
- Always send both `text_body` and `html_body` — plaintext fallback for terminal clients and accessibility tools
- Table-based layout (not divs) — Outlook still doesn't support modern CSS layout

## Anti-Patterns That Kill Emails

**Spam triggers** — avoid these in subject and body:
- Words: "free", "act now", "limited time", "congratulations", "click here"
- URL shorteners (bit.ly) in transactional email are a strong spam signal
- ALL CAPS, excessive punctuation (!!!), emoji clusters
- Sending from `no-reply@` with no SPF/DKIM/DMARC

**Design killers:**
- Image-only emails render blank (40-60% of clients block images by default)
- Buttons under 44x44px are untappable on mobile
- Multiple CTAs halve click-through (Hick's Law) — one email, one action

**Trust destroyers:**
- Misleading subject lines (fake "Re:" or "Fwd:") — permanently lose trust
- Adding promotional upsells to transactional emails trains users to ignore all your emails
- More than 1 reminder for a single action is the #1 reported unsubscribe reason in B2B SaaS

**Paul Graham's test:** if you wouldn't send it to yourself, don't send it.

## Checklist

When writing or reviewing an email, verify:

- [ ] Subject line starts with a person's name, not the product
- [ ] From name uses `{Person} via {Product}` pattern
- [ ] Headline uses first name and states the action
- [ ] Body is ≤ 2 sentences: what the product does + bold deadline
- [ ] Single CTA button with action verb
- [ ] Social proof section: avatar + name + tenure
- [ ] Preheader text set (hidden div)
- [ ] Both text and HTML bodies provided
- [ ] HTML has lang, viewport meta, inline styles
- [ ] No feature lists, no multiple paragraphs, no secondary links above the fold

## When This Skill Is Wrong

- **Marketing/newsletter emails** — different rules (unsubscribe required, different tone)
- **Password reset / verification emails** — pure utility, no selling needed
- **You have A/B test data** — data beats principles, follow your numbers
- **The recipient already uses your product** — skip the "what we do" sentence, get to the point
