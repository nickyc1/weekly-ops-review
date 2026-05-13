# Output Design Principles

Why the weekly review is one page. Why these five sections. Why this format.

## The one-page rule

Reports get longer the less they're read.

A 5-page review signals: "I want you to see all the work I did." Nobody reads past page 1.

A 1-page review signals: "I made the cuts. Here's what matters." Everyone reads it.

The right length is the length that gets read. For a weekly operating review, that's one printed page — roughly 600-800 words of markdown.

## The TL;DR is the report

If only the TL;DR got read, would the reader still know what to do? If yes, the rest is supporting context. If no, the TL;DR is broken.

Three lines maximum:

1. **The number.** "Account profit-to-spend is 1.7x — in the target band."
2. **The action.** "Reallocating $200/day from Brand to Non-Brand Search."
3. **The question.** "What's driving the Germany CVR spike? Worth investigating."

Some weeks won't have a question. Skip the line. Don't pad.

## Why exactly these five sections

Each section earns its place by answering a specific operator question.

| Section | Question it answers |
|---|---|
| TL;DR | What do I do this week? |
| Account health | Is anything on fire? |
| Channel snapshot | Where is the money working / not working? |
| Open proposals | What's still in the queue? |
| Asks for the team | What's blocked on someone else? |

Sections that get added beyond these are usually the operator showing work. Cut them.

## Specificity beats coverage

A specific observation in one channel beats a general overview of all channels.

**Bad:**
> "Search campaigns underperformed this week."

**Good:**
> "Non-Brand Search — AI Tools P:S dropped from 1.9x to 1.1x. Spend held flat but CPC rose 28% due to a new competitor (Anthropic) entering 'AI agent' queries. Recommend negative-keyword 'anthropic' and 'claude' or reset the bid strategy."

The bad version makes the operator do the digging. The good version gives them the bid recommendation.

## Tone

Neutral. Factual. No celebration. No drama.

The review is operational. Wins go in the channel snapshot ("[Channel] hit 2.4x P:S, recommending +15% budget"). Celebration belongs in a different forum.

If you find yourself writing "great week!" or "tough week," delete those words. The numbers say what kind of week it was.

## Flagging anomalies

Every cell that's outside its expected band gets flagged.

Use a small set of flags consistently:

- ⚠️ — outside expected band, needs attention
- 🚨 — circuit breaker triggered or exceeded threshold
- 📉 — declining trend over 2+ weeks
- 📈 — rising trend over 2+ weeks
- 🎯 — back on target after being off

Don't decorate with flags. Use them only on cells that meet the criteria. A clean week should have few flags. A messy week makes the flags visible immediately.

## What "expected band" means

Pulled from `paid-ads-context.md` section 3:

```
Profit-to-spend target band:        1.5x - 2.0x
Account daily budget cap:           $2,000
Single-campaign zero-conv cap:      $500
Acceptable refund rate band:        10% - 25%
```

Any metric outside its band gets flagged. The operator sets the bands once in context; the review enforces them weekly.

## Why no "what worked this week / what didn't"

It reads like a school book report. Nobody operates on "what worked / what didn't" framing — they operate on "what's the next move."

Replace it with **Action** (what's changing) and **Pending** (what's awaiting decision). Both are forward-looking and decision-oriented.

## Audience: one person

The weekly review is for the marketing operator running the account. Singular.

If you need to communicate to stakeholders (partners, leadership, board), use `paid-channel-recap` — different format, different redaction policy.

Don't mix. A document trying to serve both audiences serves neither.

## Voice

Same as the rest of the agent stack — direct, no fluff, no AI tells. The output passes through any voice-profile if one is configured.

Specifically:

- No em dashes for dramatic effect
- No "It's not X, it's Y"
- No "Here's the thing..."
- No "great work" / "rough week" framing
- Use real numbers, real campaign names, real observations

## Output ergonomics

A few specifics that make the review actually usable:

- **Date headers** clearly — "Week of May 5, 2026" — so historical reviews can be diffed
- **Link to the source data** — the BigQuery query, the dashboard URL, wherever the operator can verify
- **Don't bury the action** — proposed changes should be visible without scrolling
- **One artifact per week** — a separate Notion page per week beats appending to a long doc
- **Save the prior weeks** — historical reviews are how you spot multi-week trends

## When to deviate from the format

The one-page rule has two exceptions:

**Tentpole weeks** (launches, major sales, holidays) — the review extends with a "Tentpole context" section and explicit pre-and-post metrics. Add it, don't replace.

**Crisis weeks** (3+ circuit breakers fired, profit-to-spend below 1.0x for 3+ days) — the review becomes a 1-page incident report. Different format. Different urgency.

For 90% of weeks, the standard format holds.

## Compressed Slack version

For teams that won't open a Notion page, a compressed Slack version is acceptable. Constraints:

- 1500 chars max
- TL;DR + Account health table + worst-performing channel only
- Link to the full review for those who want more

The compressed version is a teaser. The full version is the operating doc.
