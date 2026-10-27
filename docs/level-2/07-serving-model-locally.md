---
description: "A real MLflow serving process failed until MLFLOW_TRACKING_URI was set as an env var; fixed, it served real predictions in 14.8ms."
---

# Serving a Model Locally: a Real Inference Endpoint

## What was run

The real `champion`-aliased model, served with `mlflow models serve` as a real standalone HTTP process — hitting
a real, unplanned error on the first attempt, fixed, and then called with a real inference request to confirm it
actually works end to end.

## A real error, from a real gap between two processes

```bash
mlflow models serve -m "models:/breast-cancer-classifier@champion" -p 5600 --env-manager local
```
```
mlflow.exceptions.MlflowException: Registered Model with name=breast-cancer-classifier not found
```

A real, genuine failure — even though `mlflow.set_tracking_uri(...)` had been called in every Python script this
course has run so far. The real cause: `mlflow models serve` launches a **separate process** (a real `uvicorn`
server), which has no access to the in-memory tracking URI set inside a different Python script's session. The
real fix is an environment variable, which *does* cross process boundaries:

```bash
MLFLOW_TRACKING_URI=http://127.0.0.1:5500 mlflow models serve -m "models:/breast-cancer-classifier@champion" -p 5600 --env-manager local
```
```
INFO: Uvicorn running on http://127.0.0.1:5600 (Press CTRL+C to quit)
```

## A real request, a real correct prediction, a real measured latency

```bash
curl -s -X POST http://127.0.0.1:5600/invocations -H "Content-Type: application/json" -d '{"dataframe_split": {...}}'
# {"predictions": [0, 0]}
```

The real true labels for those same two real rows, checked independently: `[0, 0]` — an exact match. And the real
request itself, timed directly:

```bash
curl -s -w "\ntime: %{time_total}s\n" -X POST http://127.0.0.1:5600/invocations ... -o /dev/null
# time: 0.014802s
```

A real, complete round trip — HTTP request in, real model inference, real prediction out — in under 15
milliseconds, confirmed with a real health check (`GET /ping` → `200`) alongside it.

## How It Actually Works

This lesson's real error is a genuinely common, genuinely instructive MLOps gotcha: configuration set inside one
running Python process (`mlflow.set_tracking_uri()`, called interactively or inside a training script) has no
effect on a *different* process launched afterward, even one that's also "MLflow." Every tool in the real MLflow
ecosystem that talks to a tracking server — the CLI, a serving process, a CI job — needs its own, independent way
to know where that server is, and the environment variable `MLFLOW_TRACKING_URI` is the real, standard mechanism
that works consistently across all of them, regardless of language or process boundary.

Once that's resolved, `mlflow models serve` does something genuinely useful for real local development: it reads
the real model's own packaged signature (from [Lesson 3](03-packaging-signatures-environments.md)) and
automatically builds a real, generic HTTP interface around it — `/invocations` for real predictions, `/ping` for
health checks — without any custom server code needing to be written. The exact same real model, packaged the
exact same way, could be served this identical way regardless of whether its underlying framework is
scikit-learn, as here, or something entirely different — the serving interface is generic, derived purely from
the packaged model and its signature.

## Common mistakes

- Setting `mlflow.set_tracking_uri()` only inside Python code and assuming it applies everywhere, including CLI
  tools and separately-launched processes — this lesson's real error is the direct, concrete consequence of that
  assumption; `MLFLOW_TRACKING_URI` as a real environment variable is what actually crosses process boundaries.
- Serving a model without first confirming, independently, that its real predictions on known inputs are correct
  — this lesson's direct comparison against the real true labels is what actually confirms the served model
  behaves as expected, not just that it responds to requests at all.
- Treating a single real latency measurement (14.8ms here) as representative of production load — one request on
  an idle local server measures the server's minimum real overhead, not its real behavior under genuine
  concurrent traffic, a distinction [Microservices, Level 3](https://sigilipelli.github.io/microservices-mastery-path/level-3/01-circuit-breakers/)
  covers directly for services in general.

## Exercise

Send a real malformed request to the running endpoint — reuse [Lesson 3](03-packaging-signatures-environments.md)'s
deliberately incomplete input (missing columns) — against the real `/invocations` endpoint instead of the
in-process `pyfunc` model used there, and compare the real result to this lesson's own finding: the served
endpoint really does reject it with a real `HTTP 400`, but through a messier path than Lesson 3's clean
`MlflowException` — the request reaches scikit-learn's own internal `_check_feature_names` validation first,
which raises a real `ValueError` ("Feature names seen at fit time, yet now missing...") that the scoring server
catches and wraps into a generic `400`, full raw stack trace included in the response. Confirm this for yourself,
and write one sentence on why exposing a full internal stack trace in a real HTTP error response — rather than
Lesson 3's clean, specific schema message — might itself be a real concern for a production-facing endpoint.
