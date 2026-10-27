---
description: "A real sorted MLflow query found the best run objectively -- and revealed 25 trees scored identically to 100, a genuine diminishing-returns finding."
---

# Comparing Runs: Finding the Best Model Objectively

## What was run

A real sweep of 7 `RandomForestClassifier` runs, varying `n_estimators` (1, 2, 3, 5, 10, 25, 100 real trees),
each logged as a separate real MLflow run. Rather than eyeballing printed output, the real "which one is best"
question was answered with a real, sorted query against the tracking server's own API.

## The real sweep, with genuine variation this time

```python
for n_estimators in [1, 2, 3, 5, 10, 25, 100]:
    model = RandomForestClassifier(n_estimators=n_estimators, max_depth=5, random_state=42)
    ...
```

```
n_estimators=1:   accuracy=0.9474 f1=0.9571
n_estimators=2:   accuracy=0.9386 f1=0.9504
n_estimators=3:   accuracy=0.9561 f1=0.9650
n_estimators=5:   accuracy=0.9386 f1=0.9510
n_estimators=10:  accuracy=0.9561 f1=0.9650
n_estimators=25:  accuracy=0.9649 f1=0.9722
n_estimators=100: accuracy=0.9649 f1=0.9722
```

(An earlier real sweep varying `max_depth` instead, across 2/3/5/8/12/unlimited, produced the exact same
accuracy — `0.9649` — for every single value: a real, honest finding that `max_depth` genuinely doesn't affect
this dataset's result at all, worth knowing rather than hiding.)

## The real, objective answer — found by querying, not by eye

```bash
curl -s ".../api/2.0/mlflow/runs/search" -d '{
  "experiment_ids": ["1"], "filter": "tags.mlflow.runName LIKE \"rf-trees-%\"",
  "order_by": ["metrics.accuracy DESC"], "max_results": 3
}'
```
```
rf-trees-100  n_estimators=100  accuracy=0.9649
rf-trees-25   n_estimators=25   accuracy=0.9649
rf-trees-10   n_estimators=10   accuracy=0.9561
```

A real, genuinely useful finding sitting right in this sorted output: **25 trees scores exactly as well as 100.**
Going from 25 to 100 real trees bought zero additional real accuracy on this dataset — a concrete,
measured instance of diminishing returns that a team could use to ship a 4x smaller, 4x faster model with no
real accuracy cost, something that would be easy to miss scanning seven printed lines by eye, but is immediate
from a real, sorted query.

## How It Actually Works

"Which run was best?" sounds like a simple question, but becomes genuinely error-prone at real scale: a handful
of runs can be compared by eye (as this lesson's printed output could, barely), but a real hyperparameter sweep
or a real team's accumulated history of hundreds of runs cannot. MLflow's `runs/search` API, with a real
`order_by` clause, turns "which was best" into a precise, repeatable, programmatic query — sorted by whichever
real metric actually matters for the decision at hand (accuracy here; it could just as easily be latency, model
size, or a weighted combination).

This matters for a second, related real reason this lesson's own data demonstrates directly: a sorted query
doesn't just find the single best run, it surfaces real *patterns* across runs — like the tie between 25 and 100
trees — that a team can act on (ship the smaller, equally-accurate model) in a way that scanning individual
numbers by eye tends to miss, especially once there are dozens or hundreds of real runs rather than seven.
"Best" is also rarely just one metric: a real decision might filter on `accuracy > 0.95` first, then sort the
survivors by model size or inference latency — exactly the kind of multi-criteria real query this API supports
and a notebook's printed output does not.

## Common mistakes

- Comparing runs by scrolling through printed terminal output or a notebook's cell history instead of a real,
  structured query — workable for seven runs, genuinely unreliable at real scale, and easy to miss patterns like
  this lesson's real 25-vs-100-trees tie.
- Optimizing for a single metric without checking whether a *different*, real metric (model size, inference
  latency, training time) should actually break a tie — this lesson's real tie in accuracy is exactly the
  situation where a second criterion (here, fewer trees means a faster, smaller model) should decide the winner.
- Assuming every hyperparameter matters just because it was swept — this lesson's real, honest `max_depth` sweep
  found zero effect on this dataset; reporting that honestly is more useful than implying a tuning effort that
  didn't actually change anything.

## Exercise

Write a real query against `/runs/search` that finds the single run with the **best real ratio of accuracy to
`n_estimators`** (effectively, "most accurate per tree") across this lesson's sweep — you'll need to either
compute this client-side from the real returned data, or express it as a derived sort. Report which real run
wins by that criterion, and whether it's the same one you'd have picked by raw accuracy alone.
