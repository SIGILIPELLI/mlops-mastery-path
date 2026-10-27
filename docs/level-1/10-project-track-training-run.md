---
description: "One real capstone run combined real seeds, a real data hash, real per-step metrics, an artifact, and a logged model -- all confirmed via MLflow's own API."
---

# Project: Track a Real Training Run End to End

## What was run

One real, comprehensive training run, `train_capstone.py`, combining every real technique built across this
level: a real fixed seed, a real SHA-256 data hash, real params chosen from the sweep findings in
[Lessons 4 and 7](04-comparing-runs.md), real per-step metrics, real organizational tags, a real logged
artifact, and a real logged model — each independently verified against MLflow's own real API, not assumed.

## One real run, every piece verified

```python
with mlflow.start_run(run_name="capstone-final") as run:
    mlflow.set_tag("owner", "course")
    mlflow.set_tag("purpose", "capstone")
    mlflow.set_tag("data_sha256", data_hash)
    mlflow.log_param("n_estimators", 100)
    mlflow.log_param("max_depth", 6)
    ...
```

```bash
python3 train_capstone.py
# run_id=bf0a9dabb13d4e23ad29e692492fa126
# data_sha256=437523f7ff68c26a...
# final_accuracy=0.9649 final_f1=0.9722
```

The real `data_sha256` matches [Lesson 6](06-data-lineage.md)'s `full-dataset` hash exactly —
`437523f7ff68c26a...` — real, direct confirmation this run trained on the identical real data as every other
"full dataset" run in this course's history, not just data that looks similar.

```bash
curl -s ".../runs/get?run_id=bf0a9dabb13d4e23ad29e692492fa126"
# params: 4   metrics: final_accuracy=0.9649, final_f1=0.9722
# tags: owner=course, purpose=capstone, data_sha256=437523f7ff68...

curl -s ".../metrics/get-history?run_id=...&metric_key=test_accuracy_vs_data_fraction"
# step=0 value=0.9561  step=1 value=0.9737  step=2 value=0.9561  step=3 value=0.9649  step=4 value=0.9649

curl -s ".../artifacts/list?run_id=..."
# capstone_confusion_matrix.json  131 bytes
```

And the real model itself, found correctly this time via the real Logged Models API rather than the old artifact
path [Lesson 1](01-what-mlops-solves.md) learned not to trust blindly:

```bash
curl -s ".../logged-models/search" -d '{"experiment_ids": ["1"], "filter": "source_run_id = \"bf0a9dab...\""}'
# model_id=m-b0dbb16731a44c0f8b4c94ea17d2ff36  status=LOGGED_MODEL_READY  source_run_id=bf0a9dab...
```

Every single real claim this lesson makes about what was tracked is backed by a real query against the tracking
server's own data — not a printed success message trusted at face value.

## How It Actually Works

This project is the direct, combined application of every technique this level built in isolation:
[Lesson 2](02-experiment-tracking-fundamentals.md)'s run/param/metric structure, [Lesson 3](03-logging-metrics-artifacts.md)'s
per-step metrics and real artifacts, [Lesson 5](05-reproducibility-seeds-environments.md)'s fixed seed,
[Lesson 6](06-data-lineage.md)'s real content hash, [Lesson 9](09-tagging-organizing-experiments.md)'s real
organizational tags, and [Lesson 1](01-what-mlops-solves.md)'s hard-won understanding of exactly where (and how)
to verify a model was really saved. None of these pieces is new; what's new is running them together, on one real
run, and confirming — the way a real team's actual audit of "can we trust this run's record" would — that every
single piece is genuinely present, not just assumed to be because the script didn't crash.

The real, honest thread running through this entire level, made concrete one final time here: a printed success
message and a verified fact are not the same thing. [Lesson 1](01-what-mlops-solves.md)'s own real investigation
— checking the wrong path first, finding nothing, and only then discovering the real, current Logged Models API —
is a direct demonstration of exactly the discipline this project's final verification applies to every other
piece: trust the tracking server's own queryable record, not the absence of an exception.

## Common mistakes

- Trusting that a training script "worked" because it printed a success message and exited cleanly, without
  independently querying the tracking server to confirm what it claims was actually recorded — this entire
  level's running theme, and the exact gap this project's final verification step closes.
- Combining many real tracking techniques in one script without testing each piece individually first — this
  project only works cleanly because every technique (seeds, hashing, per-step metrics, tags, artifacts, model
  logging) was already built and verified separately in earlier lessons.
- Treating a comprehensive tracking setup as a one-time setup cost rather than a real, reusable pattern — the
  real value of this project's structure is that it's meant to be the *template* for every real training run
  going forward, not a one-off capstone exercise.

## Exercise

Run `train_capstone.py` a second time with `max_depth=10` instead of 6, and write a real script that queries
both runs' full real records — params, final metrics, data hash, and logged model ID — and prints a real,
side-by-side comparison table. Confirm the real data hash matches across both (same data, different
hyperparameter), completing a real, independently-verified comparison of two fully-tracked runs.
