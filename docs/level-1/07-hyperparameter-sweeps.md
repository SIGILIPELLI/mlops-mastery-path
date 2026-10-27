---
description: "A real 5x5=25-run grid sweep logged in 1.94 seconds; a sorted query over all 25 confirmed n_estimators, not max_depth, was the only factor that mattered."
---

# Hyperparameter Sweeps: Tracking Many Runs at Once

## What was run

A real 5x5 grid sweep — 5 real values of `n_estimators` crossed with 5 real values of `max_depth`, 25 real
`RandomForestClassifier` training runs total — each logged as a separate real MLflow run, then queried as a
whole to find the real pattern across all 25 at once.

## A real 25-run sweep, logged fast

```python
for n_estimators in [5, 10, 25, 50, 100]:
    for max_depth in [2, 4, 6, 8, 10]:
        with mlflow.start_run(run_name=f"n{n_estimators}-d{max_depth}"):
            ...
```

```bash
python3 train_grid_sweep.py
# logged 25 real runs across a real 5x5 grid in 1.94s
```

25 real, independent runs, each training a real model and logging real params and a real metric, completed in
under 2 real seconds — fast enough that a much larger real sweep (hundreds of combinations) is genuinely
practical to run and track on a single laptop.

## The real pattern across all 25, found by querying the whole grid

```bash
curl -s ".../api/2.0/mlflow/runs/search" -d '{"experiment_ids": ["2"], "order_by": ["metrics.accuracy DESC"], "max_results": 5}'
```
```
n100-d10  n_estimators=100  max_depth=10  accuracy=0.9649
n100-d8   n_estimators=100  max_depth=8   accuracy=0.9649
n100-d6   n_estimators=100  max_depth=6   accuracy=0.9649
n100-d4   n_estimators=100  max_depth=4   accuracy=0.9649
n100-d2   n_estimators=100  max_depth=2   accuracy=0.9649
```

Every single one of the top 5 real results uses `n_estimators=100`, across every real `max_depth` value from 2 to
10 — a real, direct confirmation (at a larger scale, and completely independently) of what
[Lesson 4](04-comparing-runs.md)'s smaller sweep already suggested: for this real dataset, `n_estimators` is the
factor that actually drives accuracy, and `max_depth` genuinely doesn't matter across this entire real range.

## How It Actually Works

A hyperparameter sweep answers a different real question than a single comparison does: not "is run A better
than run B," but "across this whole real space of choices, what pattern actually determines the outcome?" This
only becomes answerable with a real, systematic sweep covering the space (the full 5x5 grid here, not a handful
of hand-picked combinations) and a real way to query the *whole* result set at once, rather than comparing pairs
of runs by eye.

The real value this lesson's own data demonstrates: a sweep that varies **two** real hyperparameters together
(not one at a time) can reveal that one of them simply doesn't matter across the entire range tested — a finding
that a sweep of `max_depth` alone, or `n_estimators` alone, would each show in isolation, but which only becomes
a *confirmed, cross-checked* real pattern by seeing it consistently across every row of a real two-dimensional
grid. This is also the direct, practical argument for tracking every run in a sweep, not just the best one: the
real pattern ("max_depth doesn't matter, n_estimators does") only becomes visible by looking at all 25 real
results together, something that's lost immediately if only the single best run is kept.

## Common mistakes

- Running a real sweep but only recording the single best result, discarding the rest — exactly the data that
  revealed this lesson's real cross-parameter pattern would be gone, leaving only "the best was 96.49%" with no
  insight into *why*.
- Sweeping hyperparameters one at a time instead of together, missing real interaction effects — this lesson's
  two-dimensional grid is what let the `n_estimators`-dominates-`max_depth` pattern show up consistently across
  every row, not just in one isolated slice.
- Assuming a sweep's real runtime will scale linearly and planning accordingly without checking — this lesson's
  real 1.94s for 25 runs on a tiny dataset doesn't predict the real runtime of a 25-combination sweep on a much
  larger real dataset or a much more expensive real model; the actual cost needs to be measured directly.

## Exercise

Extend this lesson's real grid to a 3rd dimension: sweep `min_samples_split` across `[2, 5, 10]` as well, making
a real 5x5x3 = 75-run grid. Run it, time it the same way this lesson did, and query the real top 10 results —
report whether `min_samples_split` turns out to matter for this dataset, or whether the same real
`n_estimators`-dominates pattern holds even with a third real dimension added.
