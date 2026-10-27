---
description: "The real champion server returned a real 200 OK for all 114 requests at ~14ms latency, whether real accuracy was 0.9649 or had collapsed to 0.6228."
---

# Why Monitoring a Model Differs From a Service

## What was run

The exact same real `champion` server from [Level 3](../level-3/07-canary-shadow-deployment.md), hit with two
real batches of requests — one the real, unmodified test set, one deliberately corrupted (every feature zeroed
out) — checking both the service's real HTTP health signals and the model's real predictive accuracy for each.

## A real service that looks identical, healthy both times

```python
resp = requests.post(CHAMPION, json={"values": row.tolist()})
statuses.append(resp.status_code)
```
```
=== NORMAL data ===
service health: 114/114 returned 200, avg latency=13.94ms
real model accuracy on this real data: 0.9649

=== CORRUPTED (all-zero) input ===
service health: 114/114 returned 200, avg latency=13.66ms
real model accuracy on this corrupted input: 0.6228
```

Every real service-level signal a standard uptime check or APM tool would collect — HTTP status code, response
latency — came back identical in both runs: `200`, about `14ms`. The real model's actual quality, measured the
only way that's possible (checking predictions against real labels), collapsed from `0.9649` to `0.6228` in the
second run. Nothing in the service's own observable behavior hinted at this.

## How It Actually Works

A service being "up" means it accepts requests and returns responses within its expected shape and latency —
exactly what this lesson's corrupted-input run still did, perfectly. A model being "wrong" is a completely
separate real failure mode that produces no error, no exception, no unusual status code: `model.predict()` on
all-zero input is a perfectly valid call that returns a perfectly valid `0` or `1`, just a badly *wrong* one,
because the real input no longer resembles anything the model was actually trained on.

This is the real reason model monitoring is its own discipline, not a byproduct of standard service monitoring:
every tool built for "is my API alive" — uptime checks, latency dashboards, status-code alerts, all genuinely
useful and all passing cleanly in this lesson's corrupted run — is structurally blind to a model that's
confidently producing wrong answers. Catching that requires either real ground truth (which usually arrives
late, if ever) or the real, specific statistical checks the rest of this level builds:
[data drift](02-data-drift.md) and [concept drift](03-concept-drift.md) detection, and
[monitoring without ground truth](04-monitoring-without-ground-truth.md) for when labels simply aren't available
in time.

## Common mistakes

- Treating "the dashboard is all green" as evidence the model is still working well — this lesson's real
  corrupted-input run would show entirely green service dashboards while quietly returning wrong answers on
  every request.
- Building model monitoring as an afterthought bolted onto existing service monitoring, assuming the same alerts
  cover both — they genuinely don't, as this lesson's identical-latency, identical-status-code, wildly-different
  accuracy result demonstrates directly.
- Only discovering a real model-quality problem when a human notices something's off downstream — the entire
  point of [Level 4](index.md) is building the automated, statistical equivalent of this lesson's manual
  accuracy check, so a real degradation like this corrupted-input scenario gets caught the way a real service
  outage would be: automatically, and fast.

## Exercise

Run this lesson's exact real comparison again, but instead of zeroing every feature, corrupt only a single real
column (for example, multiply `mean radius` by 100 across every request) and measure how much real accuracy
degradation that smaller, more realistic corruption produces compared to this lesson's total-zero case — while
confirming the service's real HTTP health signal stays exactly as uninformative either way.
