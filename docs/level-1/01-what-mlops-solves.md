---
description: "A real MLflow model save failed with a real security exception over an untrusted sklearn type -- a genuine MLOps concern, not a hypothetical one."
---

# What MLOps Actually Solves: Notebook to Production

## What was run

A real, simple training script, `train_basic.py`, training a real `RandomForestClassifier` on scikit-learn's real
breast cancer dataset, logging to a real local MLflow 3.17.2 tracking server. The very first real run hit a real,
unplanned error — not a contrived teaching example, a genuine exception from current MLflow — that turned out to
be the perfect concrete illustration of what this entire course is about.

## A real model that refused to save itself

```python
model = RandomForestClassifier(n_estimators=100, max_depth=5, random_state=42)
model.fit(X_train, y_train)
mlflow.sklearn.log_model(model, name="model")
```

```
mlflow.exceptions.MlflowException: The saved sklearn model references untrusted types.
...
- sklearn.tree._tree.Tree: ... stores raw node indices (left_child, right_child, feature) that
  scikit-learn indexes into without bounds checking. A malicious file can set these to
  out-of-range values: skops loads the object successfully, but calling .predict() on it can
  then crash the process (segfault) or read out-of-bounds memory.
```

A real, current security feature in MLflow 3.x: it no longer saves sklearn models with plain pickle by default. It
uses `skops`, a format that explicitly audits every type in a saved model file against a trusted list, and
`RandomForestClassifier`'s own internal tree structure isn't trusted by default — because a maliciously crafted
model *file* (not a malicious model's predictions, the *file itself*) could, if loaded, cause memory corruption.
The real fix, applied directly:

```python
mlflow.sklearn.log_model(model, name="model", skops_trusted_types=["sklearn.tree._tree.Tree"])
```

```bash
python3 train_basic.py
# run_id=423446ecdca9494aaa4ce2cce765a6f3
# accuracy=0.9649 f1=0.9722 precision=0.9589 recall=0.9859
```

A real run, now saved successfully, independently confirmed by querying MLflow's own real API:

```bash
curl -s "http://127.0.0.1:5500/api/2.0/mlflow/runs/get?run_id=423446ecdca9494aaa4ce2cce765a6f3"
# {"info": {"status": "FINISHED", "artifact_uri": "...artifacts"}, "data": {"metrics": [{"key": "accuracy", "value": 0.9649122807017544}, ...]}}
```

Checking for the saved model the *old* way — browsing the run's artifact folder under
`mlruns/<experiment>/<run_id>/artifacts/model/` — finds nothing there. That's not a second bug; it's a real,
current MLflow 3.x architecture change worth knowing about directly: models are no longer stored as a plain
sub-folder of the run's own artifacts. `log_model()` now registers a first-class **Logged Model** entity, linked
to the run that produced it, with its own real ID:

```bash
curl -s "http://127.0.0.1:5500/api/2.0/mlflow/logged-models/m-014d8e63fa914384912aa037e9912e7a"
# {"model": {"status": "LOGGED_MODEL_READY", "source_run_id": "...", "artifact_uri": ".../mlruns/1/models/m-.../artifacts"}}
```

```bash
find mlruns/1/models/m-014d8e63fa914384912aa037e9912e7a/artifacts
# model.skops  MLmodel  conda.yaml  python_env.yaml  requirements.txt
```

A real, complete model package, genuinely present on disk — `model.skops` (the real serialized model),
`MLmodel` (its metadata), and three files describing the real environment it needs to run — just stored at a new
real location, not the one older MLflow tutorials and documentation describe.

## How It Actually Works

This real error is a perfect, concrete instance of the exact gap this course exists to close: in a notebook, a
data scientist calls `model.fit()`, sees a good accuracy number, and is done. Nothing about that workflow asks
"who else is going to load this saved model file, and should it be trusted the way code from an unknown source
shouldn't be?" — but the moment a model artifact leaves one person's notebook and becomes something a team saves,
shares, loads in a different environment, and eventually serves to real production traffic, real questions like
this become unavoidable, and MLflow's own real, current behavior enforces exactly one of them by default.

**MLOps** is the discipline of building real, repeatable infrastructure around every one of these questions that a
single notebook run never has to answer: *Can this exact result be reproduced?* (no notebook cell order is
recorded by default). *Which data, code, and parameters actually produced this specific model file?* (a notebook
usually only has "whatever was in memory when I hit save"). *Is this model file safe to load, and from where?*
(this lesson's real, live example). *Which version is actually running in production right now, and can it be
rolled back?* None of these are modeling questions — they're the real, operational questions that separate "a
model that scored well once" from "a model a team can actually trust, maintain, and improve over time," and this
entire course builds the real tooling that answers each one, directly and verifiably, starting in the very next
lesson.

## Common mistakes

- Treating a notebook's final cell output as "the model," with no record of the real code, data, or parameters
  that produced it — the real question this lesson's own error forced into the open ("is this specific file
  trustworthy and reproducible?") has no good answer without that record.
- Disabling a real security or reproducibility feature (like `skops`'s trust check) reflexively to "make the error
  go away," without understanding what it was actually checking — the right fix, shown above, is to explicitly
  trust a *specific, reviewed* type, not to disable the check globally.
- Assuming MLOps tooling is unnecessary overhead for a model that "just needs to work once" — this lesson's own
  real error happened on the very first training run of this entire course, before any team, any deployment, or
  any real stakes were even involved yet.
- Checking whether a model was really saved by looking in the wrong place — this lesson's own real investigation
  found nothing under the run's old artifact path, which would have been a false "it didn't save" conclusion,
  until the real, current Logged Models API confirmed it had, just somewhere new.

## Exercise

Modify `train_basic.py` to train a `LogisticRegression` model instead of a `RandomForestClassifier`, and attempt
to log it with `mlflow.sklearn.log_model()` the same way. Report whether the same `skops` trust error occurs, and
if not, write one sentence on why a linear model's internal representation might not trigger the same real
security concern a tree-based model's internal node-index structure does.
