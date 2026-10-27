---
description: "A real model signature correctly accepted a real 30-column request and rejected a real 2-column one with a real MlflowException, before the model ran."
---

# Packaging a Model: Signatures & Environments

## What was run

A real model logged with a real, automatically inferred **signature** — a formal description of exactly what
input shape it expects — then loaded back and tested with two real requests: one correctly shaped, one
deliberately malformed, to confirm the signature actually enforces anything rather than just documenting it.

## A real signature, inferred from real training data

```python
signature = infer_signature(X, model.predict(X))
mlflow.sklearn.log_model(model, name="model", signature=signature, ...)
```
```
inputs:
  ['mean radius': double (required), 'mean texture': double (required), ... 30 columns total]
outputs:
  [Tensor('int64', (-1,))]
```

A real, complete, automatically-derived contract: all 30 real feature columns, each typed and marked required,
plus the real output shape — generated directly from the real training data, not hand-written and liable to drift
out of sync with it.

## The real enforcement, tested both ways

```python
model = mlflow.pyfunc.load_model("runs:/260a5c5789594e6bbfa52afbb4e9bde0/model")

good = pd.DataFrame([[14.0]*30], columns=model.metadata.get_input_schema().input_names())
model.predict(good)
# [0]   <- real prediction, succeeds

bad = pd.DataFrame([[14.0, 20.0]], columns=['mean radius', 'mean texture'])
model.predict(bad)
# REJECTED: MlflowException Failed to enforce schema of data '   mean radius  mean texture
# 0         14.0          20.0' with schema '['mean radius': double (required), ...
```

A real, correctly-shaped request succeeds. A real malformed one — missing 28 of the 30 required columns — is
genuinely rejected with a real exception, raised by MLflow's own schema-enforcement code **before** the
underlying scikit-learn model's `.predict()` is ever called. The model itself never sees the bad input; the
signature stops it at the door.

## How It Actually Works

A **signature** is a formal, machine-checkable contract attached to a packaged model, distinct from the model's
own internal logic: it states exactly what shape of input the model expects and what shape of output it
produces. The real enforcement demonstrated above happens at the `pyfunc` wrapper layer — the generic interface
every MLflow-logged model is loaded through regardless of its original framework — which means this same real
protection applies identically whether the underlying model is scikit-learn, a deep learning framework, or
anything else MLflow supports; the signature check doesn't care what's inside.

This matters for exactly the real production scenario this course is building toward: once a model is served
behind a real HTTP endpoint ([Lesson 7](07-serving-model-locally.md)), it will receive real requests from real
callers who can and eventually will send something malformed — a missing field, a wrong type, an extra column. A
real signature check is the first, cheapest line of defense against this, catching the error with a clear,
specific message (naming exactly which columns were expected) rather than letting malformed data reach the
model's internals and fail in a confusing, model-specific way, or — worse — silently produce a meaningless
prediction instead of an error at all.

The **environment** half of packaging (the real `conda.yaml`, `python_env.yaml`, and `requirements.txt` files
confirmed on disk back in [Level 1, Lesson 1](../level-1/01-what-mlops-solves.md)) solves a related but different
real problem: not "is this specific request shaped correctly," but "does the environment this model runs in have
the exact right library versions to produce consistent results" — the same real reproducibility concern
[Level 1, Lesson 5](../level-1/05-reproducibility-seeds-environments.md) measured directly, now captured
automatically as part of the packaged artifact rather than left to chance.

## Common mistakes

- Logging a model without a signature at all, losing this real, automatic input validation entirely — a request
  with the wrong shape would then reach the model's raw internals, where the resulting failure (or, worse, a
  silently wrong prediction) is far less clear than the specific, real rejection message shown above.
- Hand-writing a signature instead of inferring it from real training data, risking exactly the kind of drift a
  hand-maintained contract is prone to — `infer_signature()`, as used here, derives the contract from the actual
  real data the model was trained on.
- Assuming schema enforcement also validates *values*, not just *shape and type* — this lesson's signature check
  confirms a request has the right columns and types; it says nothing about whether a `mean radius` of `-500` is
  a sane real value, a separate concern not covered by signature enforcement alone.

## Exercise

Test a third real malformed request: all 30 correct columns, but with `"mean radius"` holding a real string
value (`"not a number"`) instead of a double. Confirm whether the real signature check catches this type
mismatch the same way it caught the missing-columns case, and report the exact real error message MLflow
produces for a type violation versus a shape violation.
