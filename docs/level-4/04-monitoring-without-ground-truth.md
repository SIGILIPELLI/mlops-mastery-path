---
description: "With zero real labels used, the predicted-positive rate jumped from 0.6404 to 1.0000 and a KS test on probability scores flagged p=0.0000 -- label-free."
---

# Model Performance Monitoring Without Ground Truth

## What was run

The exact same real corrupted-input scenario from [Lesson 1](01-monitoring-models-vs-services.md) — the
champion model fed all-zero input — re-examined using only signals a real production system actually has
*immediately*: the model's own real predictions and real `predict_proba` confidence scores, with no true label
used anywhere in the check.

## Real proxy signals, computed with zero labels

```python
ref_preds = model.predict(X_test)
ref_positive_rate = ref_preds.mean()
ref_proba = model.predict_proba(X_test)[:, 1]
```
```
REFERENCE (normal data): predicted-positive rate=0.6404, mean confidence=0.9468
NEW BATCH (corrupted input): predicted-positive rate=1.0000, mean confidence=0.8533

real predicted-positive-rate shift: 0.3596
real KS test on the probability-score DISTRIBUTIONS: KS=0.5439 p=0.0000 -> PROXY ALERT
```

Two real, independent, label-free signals both fired cleanly: the real predicted-positive rate jumped by
`0.3596` (every corrupted row got classified positive, versus a real, varied `0.6404` mix normally), and a real
KS test comparing the full probability-score distributions — not needing to know which predictions were
*correct*, only that their statistical shape changed — came back at `p=0.0000`.

## How It Actually Works

Both of this lesson's real checks exploit the same real fact: a model's own output carries information about
its input even before anyone knows whether that output was correct. A model forced to score input that looks
nothing like what it trained on tends to produce a real, measurably different *pattern* of outputs — every
prediction collapsing to the same class, confidence scores clustering somewhere unusual — well before a real
label ever confirms whether any individual prediction was right or wrong.

This is exactly the real gap [Lesson 3](03-concept-drift.md) identified: concept drift is invisible to
input-distribution checks and often has no real label available in time either. These proxy signals don't
require a real label at all, which is precisely why they matter — they're the real, practical tool for exactly
the gap between "a prediction happened" and "we eventually found out if it was right," which in most real
systems is measured in days or weeks, not milliseconds.

## Common mistakes

- Waiting for real labeled feedback before monitoring anything — in most real production systems, true labels
  (did the loan actually default, was the diagnosis confirmed, did the user actually convert) arrive far too
  late to catch the kind of problem this lesson's proxy signals caught instantly.
- Monitoring only the predicted class and ignoring the real confidence/probability distribution — this lesson's
  KS test on raw probability scores is more sensitive than the simpler positive-rate check, since a model can
  shift its real confidence distribution meaningfully even while its hard class predictions still look
  superficially balanced.
- Treating a proxy-signal alert as proof of a specific real problem rather than a prompt to investigate — this
  lesson's alert correctly says "prediction behavior shifted," not "input is corrupted"; confirming the actual
  cause still needs the kind of direct inspection [Lesson 1](01-monitoring-models-vs-services.md) and
  [Lesson 2](02-data-drift.md) did.

## Exercise

Run this lesson's exact real proxy-monitoring script against [Lesson 2](02-data-drift.md)'s genuinely shifted
large-tumor batch instead of this lesson's all-zero corruption, and compare which of the two real proxy signals
(positive-rate shift vs. the KS test on probability scores) reacts more strongly to a real, legitimate
population shift than to outright corrupted input.
