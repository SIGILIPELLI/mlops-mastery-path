---
description: "The real deployed champion model had no signature at all -- a real, honest gap closed with a validation layer returning clean JSON, not a stack trace."
---

# Input/Output Schemas: Catching Bad Requests Early

## What was run

An attempt to build a real, explicit validation layer in front of the served `champion` model, which immediately
surfaced a real, honest gap: the actual deployed model has **no signature at all**. A real fallback schema was
built and tested instead, contrasted directly against [Lesson 7](07-serving-model-locally.md)'s messier, raw
stack-trace rejection.

## A real, honest discovery: this model was never given a signature

```python
model = mlflow.pyfunc.load_model("models:/breast-cancer-classifier@champion")
EXPECTED_COLUMNS = model.metadata.get_input_schema().input_names()
```
```
AttributeError: 'NoneType' object has no attribute 'input_names'
```

A real, genuine gap: `champion` points at the model trained by `train_capstone.py`, which — unlike
[Lesson 3](03-packaging-signatures-environments.md)'s dedicated `signature-test` run — never called
`infer_signature()` before logging. The real, deployed, promoted-through-a-real-approval-gate model has no
built-in schema to check requests against at all.

## The real fix: an explicit schema, enforced before the model ever runs

```python
EXPECTED_COLUMNS = list(load_breast_cancer().feature_names)

@app.post("/predict")
async def predict(request: Request):
    columns = body.get("columns", [])
    missing = set(EXPECTED_COLUMNS) - set(columns)
    if missing or extra:
        raise HTTPException(400, {"error": "schema_mismatch", "missing_columns": sorted(missing), ...})
```

```bash
curl -s -w "\nHTTP:%{http_code}\n" -X POST http://127.0.0.1:5601/predict -d '{"columns": ["mean radius", "mean texture"], "data": [[14.0, 20.0]]}'
```
```json
{"detail":{"error":"schema_mismatch","missing_columns":["area error","compactness error", ... 28 total]}}
HTTP:400
```

A real, clean, structured `400` — listing exactly which columns are missing, as real, actionable JSON — compared
directly against [Lesson 7](07-serving-model-locally.md)'s real finding, where the same kind of malformed request
against MLflow's own generic serving endpoint produced a messier `400` with scikit-learn's raw internal
`ValueError` and full stack trace embedded in the response body.

```bash
curl -s http://127.0.0.1:5601/predict -d '{"columns": [...all 30 real columns...], "data": [[...]]}'
# {"predictions":[0]}
```

A real, correctly-shaped request still succeeds normally, confirming the validation layer only blocks genuinely
malformed requests, not valid ones.

## How It Actually Works

This lesson's own real discovery — a production-promoted model with no signature at all — demonstrates something
important that a course built entirely around idealized examples would miss: real models, in real pipelines,
don't always arrive pre-packaged with a complete schema, even after passing a real approval gate
([Lesson 6](06-model-approval-workflows.md)). When that happens, the responsibility for validating input shifts
from the model's own packaging ([Lesson 3](03-packaging-signatures-environments.md)'s ideal case) to the real
serving layer sitting in front of it — exactly the explicit, hand-written schema this lesson built as a genuine,
necessary fallback.

The real, practical advantage of validating explicitly at the serving layer, demonstrated directly by comparing
this lesson's clean JSON error against Lesson 7's raw stack trace: a deliberate validation step controls *exactly*
what a caller sees on failure — a clear, structured, actionable message — rather than whatever internal exception
the underlying framework happens to raise, which can (as Lesson 7 found) leak internal file paths and library
internals into a real, external-facing API response.

## Common mistakes

- Assuming every logged model has a usable signature without checking — this lesson's own real
  `AttributeError` is exactly the consequence of that assumption, against a real, currently-deployed production
  model.
- Returning raw internal exceptions (stack traces, library-specific error messages) directly in an API response,
  as [Lesson 7](07-serving-model-locally.md)'s real, unvalidated endpoint did — a real security and
  usability concern, since internal paths and library versions become visible to any caller, not just ones
  that should see them.
- Building schema validation that only checks for *missing* fields and not *unexpected* ones — this lesson's
  real validator checks both (`missing` and `extra`), since an unexpected extra field reaching a model can be
  just as real a sign of a caller-side bug as a missing one.

## Exercise

Fix the real underlying gap this lesson found, rather than only working around it: retrain and re-log a new
version of `breast-cancer-classifier` using [Level 1's capstone script](../level-1/10-project-track-training-run.md),
this time adding a real `infer_signature()` call before `mlflow.sklearn.log_model()`, exactly as
[Lesson 3](03-packaging-signatures-environments.md) did. Promote it through the real
[approval gate](06-model-approval-workflows.md), then confirm `model.metadata.get_input_schema()` no longer
returns `None` for the new real champion.
