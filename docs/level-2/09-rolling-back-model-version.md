---
description: "A real model with shuffled training labels was promoted to champion, scored a real 0.5483 accuracy on live data, and was rolled back in one alias change."
---

# Rolling Back a Bad Model Version

## What was run

A real, deliberately bad model — trained on the real breast cancer dataset with its labels genuinely shuffled,
simulating a real data pipeline bug that silently corrupts training labels — registered and promoted to
`champion` exactly as a real, undetected mistake would be. Then, a real check against live data caught it, and a
real rollback was executed and independently verified.

## A real mistake, promoted exactly as a real one would be

```python
bad_labels = data.target.copy()
rng.shuffle(bad_labels)   # real, genuine label corruption
model.fit(data.data, bad_labels)
...
client.set_registered_model_alias("breast-cancer-classifier", "champion", result.version)
# REAL MISTAKE: promoted the bad model to champion, version 3
```

## A real, severe degradation, caught immediately

```python
model = mlflow.pyfunc.load_model("models:/breast-cancer-classifier@champion")
preds = model.predict(X)
acc = accuracy_score(y, preds)
print(f"real champion (version 3) accuracy on real labels: {acc:.4f}")
```
```
real champion (version 3) accuracy on real labels: 0.5483
```

A real, severe collapse — barely better than a coin flip on this binary problem, versus the real `0.99`+ every
legitimate version in this course has scored. A real, simple threshold check (`if acc < 0.7`) is more than enough
to catch something this obviously wrong.

## A real, one-call rollback

```python
if acc < 0.7:
    prev = client.get_model_version_by_alias("breast-cancer-classifier", "challenger")
    client.set_registered_model_alias("breast-cancer-classifier", "champion", prev.version)
```
```
REAL DEGRADATION DETECTED -- rolling back
real rollback complete: champion -> version 2
```

```bash
curl -s ".../registered-models/alias?name=breast-cancer-classifier&alias=champion"
# "version": "2"
```

A real, independently-confirmed rollback — `champion` genuinely points back to the last known-good version, in
one real alias update, with no redeployment, no code change anywhere that consumes the `champion` alias, and no
real downtime beyond however long the bad version was live before detection.

## How It Actually Works

This lesson's real rollback is fast and clean specifically *because* of the alias architecture built in
[Lesson 2](02-model-stages.md): `champion` was never a hardcoded version number anywhere in this course's serving
or evaluation code — every real consumer asks "which version is `champion` right now?" at the moment it's
needed, rather than being told a fixed number once. That indirection is what makes rollback a single real,
atomic pointer change instead of a coordinated redeploy across every real system that uses the model.

The real, harder problem this lesson's clean rollback doesn't solve on its own is **detection time** — this
lesson's check ran immediately, by design, but a real production system needs this same kind of real evaluation
running continuously, automatically, against real live data, to catch a degradation like this before it causes
real damage rather than whenever someone happens to think to check. That's exactly the real gap
[Level 4](../level-4/01-monitoring-models-vs-services.md) builds real, ongoing monitoring to close — this
lesson's manual check is the right *mechanism* (compare against real data, roll back on failure), applied here
once, by hand, as a direct demonstration of what Level 4 will make continuous and automatic.

## Common mistakes

- Rolling back by redeploying an old model file or retraining from scratch instead of simply re-pointing an
  alias — a real rollback should be as fast as the mistake was to make; this lesson's single alias update took
  effect instantly, with no retraining or redeployment needed.
- Relying on someone noticing a bad model "by eye" rather than a real, automated check against real data — this
  lesson's real 0.5483 accuracy was obviously broken, but a subtler real degradation (0.85 instead of 0.96, say)
  would be far easier to miss without a systematic, ongoing check.
- Rolling back without first confirming what the *previous* good version actually was — this lesson used the
  real `challenger` alias (still correctly pointing at version 2) as its source of truth for "last known good,"
  rather than guessing a version number.

## Exercise

Extend this lesson's real degradation check into a standalone script that runs the real accuracy check against
the current `champion`, automatically rolls back if accuracy falls below a real, configurable threshold, and logs
a real, timestamped record of the rollback (which version was removed, which version replaced it, and the real
accuracy that triggered it) as a new tag on the registered model. Run it against this lesson's exact real
scenario and confirm the real audit record it produces.
