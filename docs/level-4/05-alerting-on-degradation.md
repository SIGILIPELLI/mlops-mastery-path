---
description: "The same real alert script produced an empty alerts list on normal data and a real two-check alert on corrupted data, with a correct real exit code."
---

# Alerting on Model Degradation

## What was run

A single real alerting script combining [Lesson 4](04-monitoring-without-ground-truth.md)'s two label-free proxy
checks — predicted-positive-rate shift and a KS test on probability-score distributions — into one structured,
automated check with real, fixed thresholds and a real pass/fail exit code, run against the same two real
batches from earlier in this level.

## A real alert record, structured and machine-readable

```python
if rate_shift > POSITIVE_RATE_ALERT_THRESHOLD:
    alerts.append({"check": "positive_rate_shift", "value": round(rate_shift, 4), ...})
if p < KS_PVALUE_ALERT_THRESHOLD:
    alerts.append({"check": "probability_distribution_shift", "p_value": round(p, 6), ...})
```

```json
=== normal batch ===
{"batch": "normal", "positive_rate": 0.6404, "alerts": []}

=== corrupted batch ===
{
  "batch": "corrupted",
  "positive_rate": 1.0,
  "alerts": [
    {"check": "positive_rate_shift", "value": 0.3596, "threshold": 0.15},
    {"check": "probability_distribution_shift", "p_value": 0.0, "ks_statistic": 0.5439}
  ]
}
```
```bash
echo "real exit code=$?"   # 0 -- alerting correctly distinguished the two batches
```

A real, clean, empty `alerts: []` on normal data, and a real, specific, structured pair of alerts on the
corrupted batch — each one naming exactly which check fired and by how much, not just a generic "something's
wrong."

## How It Actually Works

This lesson adds exactly one real thing on top of [Lesson 4](04-monitoring-without-ground-truth.md)'s checks:
fixed, explicit thresholds (`0.15` for rate shift, `0.05` for the KS p-value) that turn a continuous real
measurement into a real, binary decision a downstream system can act on automatically — the same real
discipline [Level 3](../level-3/03-ci-for-ml.md)'s CI tests and [Level 3](../level-3/04-cd-for-ml.md)'s CD gate
already relied on, now applied to ongoing production monitoring instead of a one-time pre-deployment check.

The real, structured JSON output matters as much as the thresholds themselves: a real alerting system
downstream of this script doesn't need to re-derive what went wrong from a plain-text log line — it gets the
exact real check name, the exact real measured value, and the exact real threshold it crossed, which is what
actually lets [Lesson 6](06-retraining-triggers.md)'s automated retraining decision, or a human on-call
engineer, act on the alert immediately instead of investigating from scratch.

## Common mistakes

- Alerting on a single signal alone — this lesson's real corrupted-batch run tripped both checks together;
  relying on only one would still have caught this particular case, but a real degradation that shifts
  confidence without shifting the raw positive-rate (or vice versa) could slip past a single-signal setup.
- Picking alert thresholds without checking what real, ordinary variation looks like first — this lesson's
  `0.15` rate-shift threshold and `0.05` p-value threshold are only meaningful because
  [Lesson 2](02-data-drift.md) and [Lesson 4](04-monitoring-without-ground-truth.md) already established what a
  genuinely unshifted real batch produces, giving a real baseline to set thresholds against.
- Printing an alert without a real, checkable exit code — a log line a human might read eventually isn't the
  same as a real signal a monitoring pipeline can act on the moment it happens; this lesson's `sys.exit(1)` on a
  missed detection, `sys.exit(0)` on a correct one, is what makes the check automatable.

## Exercise

Add a third real check to this script — mean prediction latency, measured the same way as
[Lesson 1](01-monitoring-models-vs-services.md) — with its own threshold, and confirm with a real run whether
latency degrades at all under this lesson's corrupted-input scenario, or whether (as [Lesson 1](01-monitoring-models-vs-services.md)
already found) that particular signal stays uninformative regardless of real model quality.
