---
description: "Registering a real model produced a real MLflow warning confirming Level 1's own Logged Models finding, then a real version 1, status READY, verified."
---

# The Model Registry: Versioning Models Like Code

## What was run

The real model from [Level 1's capstone run](../level-1/10-project-track-training-run.md) registered for the
first time with `mlflow.register_model()`, creating a real, versioned entry in MLflow's real Model Registry —
confirmed afterward by querying the registry's own API directly, not by trusting the script's printed output.

## A real registration, and a real confirmation of last level's finding

```python
RUN_ID = "bf0a9dabb13d4e23ad29e692492fa126"
MODEL_URI = f"runs:/{RUN_ID}/model"
result = mlflow.register_model(MODEL_URI, "breast-cancer-classifier")
```

```
Successfully registered model 'breast-cancer-classifier'.
WARNING mlflow.tracking._model_registry.fluent: Run with id bf0a9dab... has no artifacts at artifact
path 'model', registering model based on models:/m-b0dbb16731a44c0f8b4c94ea17d2ff36 instead
Created version '1' of model 'breast-cancer-classifier'.
registered: name=breast-cancer-classifier version=1 source=models:/m-b0dbb16731a44c0f8b4c94ea17d2ff36
```

That real warning is MLflow's own code independently confirming
[Level 1, Lesson 1](../level-1/01-what-mlops-solves.md)'s own real finding from a different angle: the old-style
`runs:/<run_id>/model` path genuinely has no artifacts there anymore in MLflow 3.x — the registry itself has to
redirect to the real, new Logged Models URI (`models:/m-b0dbb16731a44c0f8b4c94ea17d2ff36`) to find the actual
model. This isn't a workaround or an error; it's MLflow's own registration logic handling its own real
architecture change transparently.

## The real registration, confirmed independently

```bash
curl -s ".../registered-models/get?name=breast-cancer-classifier"
```
```json
{"registered_model": {"name": "breast-cancer-classifier", "latest_versions": [
  {"version": "1", "current_stage": "None", "status": "READY",
   "source": "models:/m-b0dbb16731a44c0f8b4c94ea17d2ff36", "run_id": "bf0a9dab..."}
]}}
```

A real, queryable registry entry: version `1`, status `READY`, with an unbroken real chain back to the exact
source run that produced it — not a file copied somewhere and renamed, but a tracked, verifiable lineage.

## How It Actually Works

The **Model Registry** adds a layer on top of experiment tracking that solves a different real problem: tracking
answers "what happened during training" (every run from Level 1); the registry answers "which of these trained
models is actually a candidate for production, and what version is it?" A **registered model** (like
`breast-cancer-classifier` here) is a named, stable identity that can accumulate real **versions** over time —
version 1 today, version 2 next month after retraining — the same way a package name on a real package registry
accumulates releases, with each version traceable back to its exact real source (here, directly back to the
specific run and its exact data hash, params, and metrics from Level 1).

This is a genuinely different concern from a raw MLflow run, which is cheap, numerous, and often exploratory —
[Level 1](../level-1/07-hyperparameter-sweeps.md) logged 25 real runs in under 2 seconds, and most of them were
never meant to go anywhere near production. **Registering** a model is a deliberate, real act of promotion: out
of potentially hundreds of real tracked runs, this specific one is being marked as a real candidate worth naming,
versioning, and — in the next lesson — moving through a real stage lifecycle toward production.

## Common mistakes

- Treating every tracked run as automatically "the model" without a real, deliberate registration step — tracking
  and registration answer different real questions, and conflating them means production deployment decisions
  get made from the same noisy pool as every exploratory experiment.
- Registering a model under a vague or inconsistent name across different real training scripts, fragmenting what
  should be one real, continuous version history into several disconnected ones.
- Assuming the `source` field in a registered model's real metadata is cosmetic — as this lesson's own real
  investigation showed, it's the actual, functioning link the registry uses to locate the real model files, not
  just a label.

## Exercise

Register a second real model version: retrain with `max_depth=10` (reusing
[Level 1's capstone script](../level-1/10-project-track-training-run.md) with that change), then register the
resulting new run's model under the *same* real name, `breast-cancer-classifier`. Query
`.../registered-models/get` again and confirm it now shows a real `version: 2` alongside version 1, each with its
own distinct, correct `run_id` and `source` — a real, growing version history for one named model.
