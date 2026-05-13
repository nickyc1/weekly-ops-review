# Anomaly Detection Rules

What counts as an anomaly worth flagging in the weekly review.

## The principle

A weekly review surfaces ~5-15 flags in a typical week. Below 5 means you're missing signal. Above 15 means everything looks like an anomaly and the flags lose meaning.

The rules below produce a calibrated number across most accounts. Tune to your account in `paid-ads-context.md`.

## Account-level anomalies

### Spend

| Trigger | Flag |
|---|---|
| Total spend within ±10% of 4-week average | OK |
| Total spend ±10-30% of 4-week average | ⚠️ |
| Total spend >30% above 4-week average | 🚨 |
| Total spend >30% below 4-week average | ⚠️ (could be budget-limited or campaigns paused) |

### Profit-to-spend (real)

| Trigger | Flag |
|---|---|
| P:S within target band | OK |
| P:S above target band ceiling | 📈 (good problem — investigate scaling) |
| P:S 0.1x below target band floor | ⚠️ |
| P:S 0.3x+ below target band floor | 🚨 |
| P:S below 1.0x for 3+ consecutive days | 🚨 (account-level circuit breaker) |

### Refund rate

| Trigger | Flag |
|---|---|
| Within band from `paid-ads-context.md` section 3 | OK |
| Above the upper band by 5pp+ | ⚠️ |
| Above the upper band by 10pp+ | 🚨 (product issue or audience drift — escalate) |

### New-buyer percentage

| Trigger | Flag |
|---|---|
| Within ±5pp of 4-week average | OK |
| Down 10pp+ from 4-week average | ⚠️ (channel skewing toward repeat — cannibalization risk) |
| Down 20pp+ from 4-week average | 🚨 |

## Campaign-level anomalies

### Per-campaign profit-to-spend

| Trigger | Flag |
|---|---|
| Campaign P:S below 1.0x for 7+ days | ⚠️ |
| Campaign P:S below 1.0x for 14+ days | 🚨 (recommend pause) |
| Campaign P:S above 2.5x for 7+ days | 📈 (recommend scaling, with incrementality caveat) |
| Campaign P:S swung 0.5x+ in either direction week-over-week | ⚠️ (investigate cause) |

### CPC anomalies

| Trigger | Flag |
|---|---|
| Average CPC within ±15% of 7-day rolling | OK |
| Average CPC 30%+ higher than 7-day rolling | ⚠️ (competitor entered, match type widened, or quality score dropped) |
| Average CPC exceeds the max profitable CPC for the campaign's deal tier | 🚨 |

Max profitable CPC math is documented in `google-ads-manager`.

### Conversion anomalies

| Trigger | Flag |
|---|---|
| Campaign spent $500+ with zero conversions in a day | 🚨 |
| Campaign conversion rate dropped 30%+ week-over-week | ⚠️ |
| Account-wide conversions = 0 for 6+ hours during business hours | 🚨 (likely tracking broken — halt bid changes) |

### Quality Score

| Trigger | Flag |
|---|---|
| Account-wide weighted Quality Score dropped 1+ point week-over-week | ⚠️ |
| Specific keyword Quality Score dropped from 7+ to 5- | ⚠️ |

## Search-term anomalies

### High-spend / low-conversion terms

| Trigger | Flag |
|---|---|
| Single search term spent $50+ with zero conversions in the window | ⚠️ (add as negative) |
| Single search term spent $200+ with zero conversions | 🚨 (add as negative + alert) |
| Single search term spent $500+ with one conversion at >2x CPA target | 🚨 (review the term) |

### High-CTR / low-impression terms

| Trigger | Flag |
|---|---|
| Term has CTR >15% but <100 impressions over 7 days | 📈 (consider adding as exact match) |
| Term shows up in 3+ campaigns at >10% CTR | 📈 (high signal, audit campaign structure for cannibalization) |

## Trend anomalies

These compare against longer windows (4-week or 12-week rolling).

### Multi-week declines

| Trigger | Flag |
|---|---|
| Metric down 5%+ for 2 consecutive weeks | 📉 |
| Metric down 5%+ for 3+ consecutive weeks | 🚨 (sustained decline — investigate cause) |

Apply to: account spend, account profit, P:S ratio, new-buyer %, refund rate, top-3 campaign profit.

### Multi-week growth

| Trigger | Flag |
|---|---|
| Metric up 5%+ for 2 consecutive weeks | 📈 |
| Metric up 10%+ for 3+ consecutive weeks | 📈 (potential trend — consider doubling down) |

## Geographic anomalies

For accounts running international:

| Trigger | Flag |
|---|---|
| New region appears in top-10 by spend | ⚠️ (intentional or PMax expanded — audit) |
| Region's CVR drops 25%+ week-over-week | ⚠️ |
| Region's share of spend up 20%+ week-over-week | ⚠️ (smart bidding shifted — verify intent) |

## Creative anomalies

| Trigger | Flag |
|---|---|
| Single ad creative consumes >40% of campaign spend | ⚠️ (single-point-of-failure risk) |
| Ad creative CTR declining 15%+ week-over-week | 📉 (creative fatigue — refresh) |
| Ad creative present but disapproved | 🚨 |

## How to apply these rules

The weekly review's "Account health" table shows the calculated values. Cells that hit any threshold above get the appropriate flag.

The "Channel snapshot" section explicitly calls out flagged campaigns with the cause and recommendation.

Anything flagged 🚨 also generates a separate immediate alert outside the weekly cadence — typically a Slack message to the operator the moment the trigger fires.

## What's NOT an anomaly

- Single-day fluctuations within 15% of the rolling average — that's noise, not signal
- Seasonal patterns the operator already knows about (Black Friday, Q1 budget reset, etc.) — don't flag these unless they're outside the expected seasonal band
- Newly-launched campaigns in their first 7 days — they're in learning phase; the rules don't apply yet

## Tuning to your account

The defaults above produce ~5-15 flags per week in a typical $30K-$200K/month account. If yours produces more or fewer, tune the thresholds in `paid-ads-context.md`.

Specifically: higher-volume accounts can tolerate tighter thresholds (small deviations matter more in absolute terms). Smaller accounts need looser thresholds (everything is noisy at low volume).
