# weekly-ops-review

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://makeapullrequest.com)

A [Claude Code](https://claude.com/claude-code) skill that generates the weekly paid-ads operating review — the one-page document that captures health, decisions, and asks for the marketing team.

## Why this exists

Most weekly reports get longer the less they're read. A 5-page review signals "I want you to see my work." Nobody reads past page 1. A 1-page review signals "I made the cuts. Here's what matters." Everyone reads it.

This skill generates the one-page review. Mondays. Same format every week. Compounds.

## What it produces

A markdown document with five sections:

1. **TL;DR** — three lines. The number, the action, the question.
2. **Account health** — week-over-week metrics on the dimensions that matter
3. **Channel snapshot** — performance per channel with the tier label (PUSH / SCALE / TEST / PAUSE)
4. **Open proposals** — what's awaiting approval from last week's run
5. **Asks for the team** — specific things only humans can answer

Total: ~600-800 words. One printed page. Anything longer gets skimmed and ignored.

## Requirements

- [Claude Code](https://claude.com/claude-code)
- Recommended setup:
  - [`paid-ads-context`](https://github.com/nickyc1/paid-ads-context) configured (provides targets, cadence)
  - [`attribution-modeling`](https://github.com/nickyc1/attribution-modeling) pipeline producing the `campaign_performance` table
  - [`google-ads-manager`](https://github.com/nickyc1/google-ads-manager) running weekly reviews and producing change proposals

The skill is the conductor — it doesn't generate analysis from scratch, it pulls existing analyses and synthesizes them.

## Install

```bash
git clone https://github.com/nickyc1/weekly-ops-review.git ~/.claude/skills/weekly-ops-review
```

Restart Claude Code. The skill is available.

## Usage

In Claude Code:

```
Run the weekly ops review for last week.
```

```
Run weekly-ops-review. Output: slack-compressed. Post to #marketing-ops.
```

```
Generate Monday's review, but include a trends section comparing last 4 weeks.
```

## The opinion baked in

- **One page or it doesn't get read.** Discipline of cutting matters more than depth.
- **TL;DR is the report.** If the rest got cut, the operator should still know what to do.
- **Specificity > coverage.** A specific observation in one channel beats a general overview of all channels.
- **Neutral tone, no celebration.** Operational document. Wins are in the channel snapshot; celebration belongs elsewhere.
- **Flag anomalies, don't decorate.** Use flags only on cells that exceed thresholds. Clean weeks have few flags. Messy weeks make flags visible immediately.
- **Audience is ONE person.** The operator running the account. Not for stakeholders — that's `paid-channel-recap`.

## How Monday morning runs

Suggested operator cadence (also lives in `paid-ads-context.md` section 7):

| Time | Action |
|---|---|
| Mon 9am | Skill fires. Output to Notion page or Slack channel. |
| Mon 10am | Operator reads, asks team for clarifications. |
| Mon 11am | Operator runs `google-ads-manager` change-proposal step. |
| Mon-Tue | Approvals happen. |
| Tue PM | Changes applied. |

## Output formats

- **Markdown** (default) — for Notion, internal wiki, or piped into Slack
- **HTML dashboard** — for a more visual version
- **Slack compressed** — 1500-char message, TL;DR + bullets only

## Related skills

| Skill | Relationship |
|---|---|
| [`paid-ads-context`](https://github.com/nickyc1/paid-ads-context) | Provides targets (section 3) and cadence (section 7) |
| [`attribution-modeling`](https://github.com/nickyc1/attribution-modeling) | Provides the profit table this skill reads |
| [`google-ads-manager`](https://github.com/nickyc1/google-ads-manager) | Provides circuit-breaker results, change proposals, search-term mining |
| [`paid-channel-recap`](https://github.com/nickyc1/paid-channel-recap) | Sister skill — same data, stakeholder audience, redacted output |
| [`ad-creative`](https://github.com/nickyc1/ad-creative) | Creative winners surface in the channel snapshot |
| [`customer-research`](https://github.com/nickyc1/customer-research) | The review may trigger a research round when a question surfaces |
| [`n8n-recipes`](https://github.com/nickyc1/n8n-recipes) | The cron + Slack delivery pattern |

## Repo structure

```
weekly-ops-review/
├── SKILL.md
├── references/
│   ├── output-design-principles.md     # why one page, why five sections
│   └── anomaly-detection-rules.md      # what counts as worth flagging
├── templates/
│   ├── weekly-review.md                # markdown output template
│   └── slack-compressed.md             # compressed Slack version
└── README.md
```

## License

MIT — see [LICENSE](LICENSE).

Built by [Nick Christensen](https://github.com/nickyc1).
