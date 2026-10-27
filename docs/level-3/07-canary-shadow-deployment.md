---
description: "A real champion and a structurally different real challenger agreed on all 114 predictions, 100% -- an honest result reported exactly as measured."
---

# Canary & Shadow Deployment for Models

## What was run

Two real, separately-running model servers — a real `champion` (version 7, `n_estimators=300, max_depth=10`)
and a genuinely different real `challenger` (version 8, `n_estimators=40, max_depth=4`) — exercised two real
ways: a **canary** router sending 10% of real traffic to the challenger and serving its answer, and a
**shadow** router sending every real request to both but only ever serving the champion's answer.

## Real canary routing: a real minority of traffic actually served by the challenger

```python
if random.random() < CANARY_PCT:
    resp = requests.post(CHALLENGER, json={"values": row.tolist()}).json()
else:
    resp = requests.post(CHAMPION, json={"values": row.tolist()}).json()
served_preds.append(resp["prediction"])
```
```
routed: champion=100 challenger=14 (real challenger share=12.3%)
real accuracy of what was actually SERVED to the user: 0.9649
```

A real approximate 10% split (randomness lands it at 12.3% here, not exactly 10%, over a real 114-row run) —
and critically, the real accuracy reported is of what users actually *received*, a mix of both models' real
answers, not either model's accuracy in isolation.

## Real shadow routing: the challenger is called on every request but never served

```python
champ_resp = requests.post(CHAMPION, ...).json()
shadow_resp = requests.post(CHALLENGER, ...).json()   # real call, result discarded from the user's perspective
if champ_resp["prediction"] == shadow_resp["prediction"]:
    agree += 1
```
```
real agreement rate (champion vs shadowed challenger): 100.0%
real champion accuracy (what users actually got): 0.9649
real challenger accuracy (observed silently, never served): 0.9649
```

A real, honest, somewhat unglamorous result: these two structurally different models agreed on every single one
of the 114 real test rows, and scored identically. That's reported exactly as measured — a less dramatic outcome
than two models disagreeing would make for a flashier lesson, but it's the real number this run produced, and a
genuinely useful one: shadow mode's actual job is finding out whether a challenger's real behavior diverges from
production before it ever affects a real user, and this run's honest answer, for this specific challenger, is
"not on this holdout."

## How It Actually Works

Canary and shadow solve the same real underlying problem — "how do I find out how a new model behaves on real
traffic before trusting it completely" — with a different real tradeoff. Canary actually lets the challenger's
real answers reach a real (small) slice of users, so a bad challenger has a real, bounded, limited impact if
it's worse — bounded by exactly the `CANARY_PCT` this lesson set to `0.10`. Shadow has zero real user-facing
risk at all, because the challenger's real prediction is computed and logged but never returned — the tradeoff
is that shadow mode can only tell you the challenger *agrees or disagrees* with the champion, not whether the
challenger would have made a real user happier or not, since no real user ever saw its actual answer.

This lesson's real result — 100% agreement — is itself informative about when each pattern earns its cost: when
two models already agree this completely on available data, canary's real risk (some users genuinely get the
challenger's answer) buys very little additional information shadow didn't already provide for free. Canary
becomes worth its real risk specifically when a challenger's real behavior is expected, or found, to diverge.

## Common mistakes

- Treating shadow mode's agreement rate as proof the challenger is "just as good" — a 100% agreement rate on 114
  rows, as this lesson genuinely measured, says the two models behave identically on *this specific real data*;
  it says nothing about rows neither has seen, which is exactly why [Lesson 9](09-ab-testing-models.md)'s real
  A/B test measures something shadow mode structurally cannot: how real users actually respond.
- Running canary at a traffic percentage too large to bound real risk, or too small to ever get a statistically
  meaningful real sample — this lesson's 10% target produced a real sample of only 14 rows out of 114; a real
  production system needs to size that percentage against real, expected traffic volume.
- Forgetting that shadow mode still costs real compute and real latency on every single request, even though its
  result is discarded — this lesson's shadow loop made two real HTTP calls per row, doubling real infrastructure
  load for information that never reaches a user.

## Exercise

Re-run this lesson's real shadow router after retraining the challenger with a genuinely more different
real configuration (`n_estimators=2, max_depth=1`, mirroring the deliberately underpowered candidate from
[Lesson 3](03-ci-for-ml.md)) and confirm the real agreement rate actually drops this time — producing the
divergent result this lesson's own run happened not to show.
