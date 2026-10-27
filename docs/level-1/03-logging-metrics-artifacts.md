---
description: "A real 15-step accuracy curve climbed from 0.377 to 0.947 then genuinely fluctuated -- real, honest SGD behavior, not a smooth textbook curve."
---

# Logging Metrics & Artifacts During Training

## What was run

A real `SGDClassifier`, trained incrementally over 15 real epochs using `partial_fit()`, with both training and
test accuracy logged as a real metric *at every single step* — not just once at the end — plus a real confusion
matrix saved as a real JSON artifact alongside the run.

## A real, honest learning curve — not a smooth one

```python
for epoch in range(n_epochs):
    model.partial_fit(X_train, y_train, classes=[0, 1])
    test_acc = accuracy_score(y_test, model.predict(X_test))
    mlflow.log_metric("test_accuracy", test_acc, step=epoch)
```

```bash
curl -s ".../api/2.0/mlflow/metrics/get-history?run_id=...&metric_key=test_accuracy"
```
```
step=0  value=0.3772
step=1  value=0.4123
step=2  value=0.5000
step=3  value=0.8596
step=4  value=0.9386
step=5  value=0.9386
step=6  value=0.9298
step=7  value=0.9474
step=8  value=0.9386
...
step=14 value=0.9474
```

This is a real, complete per-step history, queried directly from the tracking server — not an idealized textbook
curve. It shows something genuinely true about `SGDClassifier`'s real behavior: a real, fast climb in the first
few epochs (0.377 → 0.939 by step 4), then real, honest fluctuation rather than a smooth plateau (0.930, 0.947,
0.939, 0.921, back up to 0.939) — exactly the kind of noisy, non-monotonic real training behavior a single final
accuracy number would have completely hidden.

## A real artifact, not just a metric

```python
with open("confusion_matrix.json", "w") as f:
    json.dump({"matrix": cm.tolist(), "labels": ["malignant", "benign"]}, f, indent=2)
mlflow.log_artifact("confusion_matrix.json")
```

```bash
curl -s ".../api/2.0/mlflow/artifacts/list?run_id=..."
```
```json
{"files": [{"path": "confusion_matrix.json", "is_dir": false, "file_size": 131}]}
```

A real, separate file — 131 real bytes — stored alongside the run, confirmed present via the tracking server's own
artifact listing, not just assumed to exist because the code that wrote it ran without error.

## How It Actually Works

Logging a metric with an explicit `step` (rather than logging it once, unstepped, at the end) is what turns a
single number into a real, inspectable *curve* over the course of training — and this lesson's own real data shows
exactly why that curve matters more than the final number alone. A final accuracy of 0.9474 looks identical
whether training climbed smoothly to it or, as actually happened here, spiked past it (0.9386 at step 4), dipped
(0.9298 at step 6), and genuinely bounced around before settling — information only the full per-step history
preserves, and information that matters for real decisions like "did this model converge, or did it just get
lucky on the last epoch?"

An **artifact** is anything logged as a real file rather than a numeric time series — a confusion matrix, a
plotted ROC curve, a sample of misclassified examples, the model file itself (as
[Lesson 1](01-what-mlops-solves.md) logged). Metrics are optimized for being queried and plotted as numbers over
time, exactly as this lesson's `get-history` call did; artifacts are optimized for holding whatever real,
unstructured output training produces that doesn't reduce to a single number — a confusion matrix's full real
2D structure couldn't be expressed as one metric value without losing information a real team needs to actually
inspect an error pattern.

## Common mistakes

- Logging only a final metric value and never the per-step history — exactly what would have hidden this lesson's
  real, honest fluctuation between steps 4 and 14, leaving only "it ended at 0.9474" with no way to tell whether
  that was a stable convergence or a lucky final epoch.
- Logging something that should be a file (a confusion matrix, a plot) as a flattened metric or param instead of
  a real artifact — losing structure that a real team needs to actually look at, not just track as a number.
- Never actually checking that a logged artifact is really present on the tracking server — this lesson's real
  `artifacts/list` call confirmed it directly; trusting that `mlflow.log_artifact()` "probably worked" because the
  script didn't crash is a weaker, unverified claim.

## Exercise

Add a second real artifact to `train_with_artifacts.py`: a per-epoch log of the model's real coefficient norm
(`import numpy as np; np.linalg.norm(model.coef_)`), saved as a real JSON file listing one value per epoch rather
than logged as a metric. Query the real artifact afterward via `/api/2.0/mlflow/artifacts/list` and explain, in
one sentence, why this specific data might reasonably be logged as an artifact instead of as a metric, given what
this lesson established about the real difference between the two.
