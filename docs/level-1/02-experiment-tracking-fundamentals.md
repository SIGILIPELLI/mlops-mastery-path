---
description: "A real MLflow runs/search query found 3 real runs under one experiment, including a genuine FAILED run from an earlier error -- tracked, not discarded."
---

# Experiment Tracking Fundamentals: Runs, Params, Metrics

## What was run

Real queries against the real local MLflow server's own API — `/experiments/search` and `/runs/search` — to see
exactly how MLflow organizes real tracked work, including a genuine `FAILED` run left over from
[Lesson 1](01-what-mlops-solves.md)'s real skops error, still fully present in the real tracking history.

## The real hierarchy, queried directly

```bash
curl -s ".../api/2.0/mlflow/experiments/search" -d '{"max_results": 10}'
```
```
1  breast-cancer-classifier
0  Default
```

```bash
curl -s ".../api/2.0/mlflow/runs/search" -d '{"experiment_ids": ["1"]}'
```
```
logreg-test  02679eb1  FINISHED
rf-baseline  423446ec  FINISHED
rf-baseline  154c541e  FAILED
```

That third row is real and worth noticing: `154c541e` is the exact run from [Lesson 1](01-what-mlops-solves.md)
that crashed on the real `skops` trust error, before the fix was applied. MLflow didn't discard it or hide it —
it's recorded, honestly, as a real `FAILED` run, permanently part of this experiment's real history.

## The real params and metrics, for one specific run

```bash
curl -s ".../api/2.0/mlflow/runs/get?run_id=423446ecdca9494aaa4ce2cce765a6f3"
```
```
params:  n_estimators=100, max_depth=5, random_state=42
metrics: accuracy=0.9649, f1_score=0.9722, precision=0.9589, recall=0.9859
```

## How It Actually Works

MLflow's tracking model has three real, nested levels: an **experiment** is a named collection of related work
(`breast-cancer-classifier` here); a **run** is one real execution — one real call to `model.fit()`, one real set
of resulting metrics — nested inside an experiment; and within each run, **params** and **metrics** are two
deliberately different kinds of data. Params are logged once, at the start, and represent *inputs* — the things
you chose before training (`n_estimators=100`). Metrics are logged as *outputs* — the things you measured as a
result (`accuracy=0.9649`) — and, as later lessons show, can be logged multiple times within one run to capture
how a value changes over training, which a param (fixed, chosen in advance) never does.

The real `FAILED` run in this lesson's data demonstrates something genuinely important about how tracking should
work: a run's status is recorded *honestly*, not filtered to only show successes. This matters because a record
of what *didn't* work is itself real, useful information — which hyperparameter combinations crashed, which
environment configurations failed, exactly the kind of signal that gets silently lost if a team only remembers
and tracks its successful notebook runs. MLflow records every real run it's told about, start to finish, success
or failure, which is precisely what makes it trustworthy as a historical record rather than a curated highlight
reel.

## Common mistakes

- Treating a failed run as something to delete or ignore rather than real, informative tracking data — this
  lesson's own real `FAILED` row shows exactly why keeping it matters: it's honest evidence of what was actually
  tried, including what broke.
- Logging a value that changes during training (like an evolving loss curve) as a single param instead of a
  metric — params are for one-time, fixed inputs; a value that updates over time belongs as a metric, logged at
  each real step.
- Querying runs by memory or manual notes ("I think the good one was yesterday afternoon") instead of the real
  tracking server's own queryable API — exactly the kind of real, structured query this lesson used directly
  instead of guessing.

## Exercise

Run `train_basic.py` three more times with three different real `max_depth` values (3, 5, 10), then query
`/runs/search` for the `breast-cancer-classifier` experiment and report, from the real returned data, the
`run_id`, `max_depth` param, and `accuracy` metric for all three — confirming you can reconstruct exactly which
real hyperparameter choice produced which real result, purely from the tracking server's own records.
