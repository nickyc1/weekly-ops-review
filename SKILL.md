---
name: weekly-ops-review
description: Generate the weekly paid-ads operating review — the one-page document that captures health, decisions, and asks for the marketing team. Pulls from google-ads-manager, paid-channel-recap, attribution-modeling, and the profit table. Use when asked to "run the weekly review", "generate the Monday report", "weekly ops report", "what happened last week", or any weekly recurring growth/marketing standup.
metadata:
  version: 1.0.0
---

# Weekly Ops Review

The Monday-morning ritual. One page. Health, decisions, asks.

This skill is the conductor — it doesn't generate new analysis, it pulls the analyses from `google-ads-manager`, `paid-channel-recap`, `attribution-modeling`, and the underlying `campaign_performance` table, then synthesizes them into a single review document.

## When to use

- Monday or Tuesday mornings, before the weekly marketing standup
- Before a leadership 1:1 where you're presenting paid-ads health
- After a major launch (compressed format — daily for first week)
- Anytime someone says "wait, how are ads doing?"

## What it produces

A markdown document with five sections:

1. **TL;DR** — three lines. The number, the action, the question.
2. **Account health** — week-over-week metrics on the dimensions that matter
3. **Channel snapshot** — performance per channel with the tier label
4. **Open proposals** — what's awaiting approval from last week's run
5. **Asks for the team** — specific things only humans can answer

Output target: one page printed. ~600-800 words. Anything longer gets skimmed and ignored.

## The five sections

### 1. TL;DR

Three lines maximum. The skill picks from these patterns:

- **Status line.** "Account profit-to-spend is [X.X]x — [in band / above target / below floor]."
- **Action line.** "[Specific change happening this week] / [no change, holding]."
- **Question line.** "One thing I need from you: [specific question]."

If there's nothing to ask, skip the question line. Don't pad.

### 2. Account health

A small table — current week vs prior week vs 4-week rolling.

| Metric | This week | Last week | 4-week avg | Δ |
|---|---|---|---|---|
| Spend | $___ | $___ | $___ | __% |
| Profit (real) | $___ | $___ | $___ | __% |
| Profit-to-spend | __x | __x | __x | ±__x |
| Orders | ___ | ___ | ___ | __% |
| New-buyer % | __% | __% | __% | ±__pp |
| Refund rate | __% | __% | __% | ±__pp |

The skill flags any cell that's outside its expected band with a ⚠️.

### 3. Channel snapshot

For each active channel, a one-line health entry:

```
[Channel name] — $X spent, Y.Yx P:S, [PUSH/SCALE/TEST/PAUSE] tier. [One-line observation.]
```

Tier definitions (from `google-ads-manager`):

- **PUSH** — P:S above 2.0x. Budget increase proposed (with incrementality caveat).
- **SCALE** — P:S 1.5x-2.0x. Hold or modest shift toward.
- **TEST** — P:S 1.0x-1.5x. Investigate.
- **PAUSE** — P:S below 1.0x for 2+ consecutive weeks. Recommend pause.

The "one-line observation" is what humans actually use. Make it specific. "Brand search P:S dropped 0.3x because impression share lost to competitor [name] in IS auctions" beats "performance declined."

### 4. Open proposals

Anything from last week's change-proposal output that hasn't been actioned. Three states:

- **Pending your approval:** [link to proposal] — sitting in your queue since [date]
- **Applied:** [proposal] applied [date]. Outcome to date: [observation]
- **Rejected:** [proposal] you said no on [date]. Anything change?

The point of this section: don't lose changes in the workflow. The week-over-week loop only closes if approved changes actually happen.

### 5. Asks for the team

Specific things only humans can answer:

- "[Person] — can I get an updated COGS number for [SKU]? Last data point is from [date]."
- "[Person] — is the Q2 launch still on track for [date]? Affects the budget reservation."
- "[Anyone] — saw a 30% spike in branded search volume from [region]. Anyone know what's happening there?"

If there are no asks, skip the section. Don't invent them.

## The data pipeline this skill consumes

| Source | What it provides |
|---|---|
| `attribution-modeling` profit table | Real profit-to-spend per campaign per day |
| `google-ads-manager` | Last week's circuit-breaker triggers, change proposals, search-term mining results |
| `paid-channel-recap` (if active campaign window) | Per-channel performance rolled up |
| `paid-ads-context.md` section 3 | Target P:S band, daily budget cap, refund rate band |
| `paid-ads-context.md` section 7 | Cadence rules (when this report fires) |

## Cadence

By default, runs once a week. Cron expression in `paid-ads-context.md` section 7.

Suggested setup:

- **Monday 9am account-time:** the skill fires. Output goes to a Notion page (or Slack channel) named "Weekly Ops Review."
- **Monday 10am:** operator reads, asks the team for clarifications.
- **Monday 11am:** operator runs `google-ads-manager` change-proposal step for the week.
- **Monday-Tuesday:** approvals happen.
- **Tuesday afternoon:** changes applied.

Compressed alternative — for high-spend or high-velocity accounts:

- Daily mini-review (5 metrics, no channel breakdown) at 9am
- Full weekly review on Monday only

## What this skill does NOT do

- It does NOT make budget decisions on its own. It surfaces them.
- It does NOT auto-apply changes. That's `google-ads-manager`'s job after human approval.
- It does NOT replace deeper analysis. Surface anomalies; the operator decides whether to dig in.
- It does NOT write a "weekly newsletter" style report. The output is operational, not communications.

## Audience

The output is for ONE person: the marketing operator running the account. Not for stakeholders, not for partners, not for a wider audience.

If you need a stakeholder-facing version: use `paid-channel-recap` instead, which has the redaction policy baked in.

## Output format options

- **Markdown (default)** — for Notion, internal wiki, or piped into Slack
- **HTML dashboard** — for a more visual version; uses the same template style as `paid-channel-recap`
- **Slack message** — compressed to fit in a 1500-char message; TL;DR + bullets only
- **Voice memo summary** — 90-second audio summary for the operator's commute

Set the output format in the skill invocation:

```
Run weekly-ops-review. Output: markdown.
```

```
Run weekly-ops-review. Output: slack-compressed. Post to #marketing-ops.
```

## Anti-patterns

| Anti-pattern | Why | Fix |
|---|---|---|
| Long-form weekly review | Nobody reads past page 1 | One page. Cut. |
| Generic observations ("performance was mixed") | Tells the operator nothing actionable | Force specificity: campaign name, exact number, suspected cause |
| No flags / no callouts | The whole point is to surface anomalies | Flag any metric outside its band |
| All metrics, no opinion | Data without interpretation isn't a review | Every section has at least one "here's what to do about it" line |
| Includes celebration / praise | The review is operational; celebration belongs in a different forum | Keep tone neutral and factual |
| Misses last week's open proposals | Decisions get lost | Section 4 is non-negotiable |

## Related skills

| Skill | Relationship |
|---|---|
| [`paid-ads-context`](https://github.com/nickyc1/paid-ads-context) | Provides targets (section 3) and cadence (section 7) |
| [`attribution-modeling`](https://github.com/nickyc1/attribution-modeling) | Provides the profit table this skill reads |
| [`google-ads-manager`](https://github.com/nickyc1/google-ads-manager) | Provides circuit-breaker results, change proposals, search-term mining |
| [`paid-channel-recap`](https://github.com/nickyc1/paid-channel-recap) | Different audience — stakeholders. Different format. Sister skill. |
| [`ad-creative`](https://github.com/nickyc1/ad-creative) | Creative winners from last week feed forward as input here |
| [`customer-research`](https://github.com/nickyc1/customer-research) | If the review surfaces a question like "why did this audience tank," may trigger a research round |
| [`n8n-recipes`](https://github.com/nickyc1/n8n-recipes) | The cron + Slack delivery pattern lives there |

## References

- [`references/output-design-principles.md`](references/output-design-principles.md) — why one page, why these five sections
- [`references/anomaly-detection-rules.md`](references/anomaly-detection-rules.md) — what counts as an anomaly worth flagging
- [`templates/weekly-review.md`](templates/weekly-review.md) — the markdown output template
- [`templates/slack-compressed.md`](templates/slack-compressed.md) — the compressed Slack version
