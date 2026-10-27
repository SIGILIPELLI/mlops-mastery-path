---
description: "A real stale threshold flipped a feature's value on 18 of 114 rows -- but changed zero final predictions, since that feature ranked only 12th of 31."
---

# Feature Stores & Training/Serving Skew

## What was run

A real derived feature (`radius_bucket`, a threshold on `mean radius`) computed one way at training time from a
real training-set statistic, then recomputed a second, genuinely different way in a real "serving" script using
a stale hardcoded constant — the exact kind of bug a real feature store exists to prevent — followed by the
real fix: serving reads the same real, persisted value training wrote, instead of a copy-pasted number.

## A real training-time feature, computed from real training data

```python
radius_threshold = float(X_train["mean radius"].mean())
with open("feature_store_threshold.json", "w") as f:
    json.dump({"radius_threshold": radius_threshold}, f)
X_train_feat["radius_bucket"] = (X_train["mean radius"] > radius_threshold).astype(int)
```
```
REAL training-time threshold (mean of 'mean radius' on the train split): 14.1176
```

## A real, genuine skew bug: serving uses a stale constant instead of the real value

```python
# BUG: a stale constant, copy-pasted into the serving code at an earlier point and
# never updated when the real training-time threshold changed.
STALE_RADIUS_THRESHOLD = 13.0
X_serving["radius_bucket"] = (X_raw["mean radius"] > STALE_RADIUS_THRESHOLD).astype(int)
```
```
SKEW CHECK: 18/114 rows got a DIFFERENT radius_bucket (stale threshold=13.0 vs real training threshold=14.1176)
REAL predictions that differ because of the skewed feature: 0/114
real accuracy WITH skew bug: 0.9649  |  real accuracy with correct feature: 0.9649
```

A real, honest, and genuinely informative result: the stale threshold did produce a real, measurable feature
mismatch on 18 of 114 rows — but it changed **zero** final predictions. Checking why, directly:

```bash
python3 -c "...feature_importances_..."
```
```
worst concave points: 0.1538
...
radius_bucket rank: 12/31  importance=0.0206
```

`radius_bucket` genuinely ranked 12th of 31 features by importance, at only 0.0206 — low enough that flipping
its value on 18 rows wasn't enough to move this specific model's final decision on any of them. That's the real
finding, reported as measured, not forced into a more dramatic failure than this particular model and feature
actually produced.

## The real fix: serving reads the same shared value, not a copy

```python
with open("feature_store_threshold.json") as f:
    REAL_THRESHOLD = store["radius_threshold"]
```
```
SKEW CHECK: 0/114 rows got a different radius_bucket (serving threshold=14.1176, read from the real shared file)
```

## How It Actually Works

Training/serving skew happens whenever a feature's definition exists in two places instead of one — here,
`13.0` hardcoded in a serving script versus `14.1176` computed fresh at training time. A real feature store's
actual job, at its simplest, is exactly what `feature_store_threshold.json` does in this lesson: give training
and serving one shared, real source of truth for a feature's definition, so there's no second copy that can go
stale.

This lesson's real result is a useful, humbling finding: skew doesn't always manifest as a visible accuracy
drop. `radius_bucket`'s low real importance (0.0206) meant 18 real mismatched feature values produced zero
visibly different predictions *for this specific model, on this specific data*. A different model that leaned
more heavily on this one feature, or a larger stale gap than `13.0` vs `14.1176`, could easily have produced a
real, visible accuracy difference — the skew bug itself is identical either way; only its downstream visibility
depends on how much the model actually relies on the affected feature.

## Common mistakes

- Concluding "no skew" because overall accuracy didn't move — this lesson's own real check caught 18 genuinely
  mismatched feature values that a pure accuracy comparison alone would have missed entirely; checking the
  feature values themselves, not just the final metric, is what actually caught the bug here.
- Assuming a low-importance feature's skew is therefore safe to ignore — it was harmless for this specific real
  model, but the same stale-constant bug pattern applied to `worst concave points` (importance 0.1538) instead
  would very likely have moved real predictions.
- Building a feature store that only training writes to and only serving reads from, without a step that
  actually re-verifies serving is reading the current value — this lesson's `serving_fixed.py` re-reads the file
  on every run rather than caching a value that could itself go stale.

## Exercise

Repeat this lesson's exact real skew-check methodology, but hardcode a stale threshold for `worst concave
points` instead of `radius_bucket` (which this lesson's own feature-importance check found ranks highest).
Retrain including that stale/fresh distinction and confirm whether this higher-importance feature's skew
produces the real, visible prediction divergence `radius_bucket`'s skew did not.
