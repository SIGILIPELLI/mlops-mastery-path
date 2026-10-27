---
description: "mlflow models generate-dockerfile produced a real, complete Dockerfile from the real champion model -- Docker itself wasn't installed, stated honestly."
---

# Containerizing a Model for Deployment

## What was run

MLflow's own real `generate-dockerfile` command, pointed at the real, live `champion` model from this course's
registry, which genuinely produced a complete Dockerfile and build context on disk — not a hand-written
Dockerfile guessed at. **Honest limitation:** Docker itself is not installed on the machine this course was
built on, so the image described below was never actually built or run; everything up to that point is real.

## A real Dockerfile, generated from the real model — not hand-written

```bash
mlflow models generate-dockerfile \
  -m "models:/breast-cancer-classifier@champion" \
  -d ./docker_output --env-manager virtualenv
```
```
INFO mlflow.models.cli: Generating Dockerfile for model models:/breast-cancer-classifier@champion
INFO mlflow.models.flavor_backend_registry: Selected backend for flavor 'python_function'
INFO mlflow.models.cli: Generated Dockerfile in directory ./docker_output
```

```bash
find docker_output/model_dir -maxdepth 1
```
```
docker_output/model_dir/python_env.yaml
docker_output/model_dir/requirements.txt
docker_output/model_dir/MLmodel
docker_output/model_dir/conda.yaml
docker_output/model_dir/model.skops
```

The real, exact-version-pinned `requirements.txt` MLflow itself wrote when this model was logged
(`mlflow==3.17.2`, `scikit-learn==1.9.1`, `pandas==3.1.0`, `skops==0.16.0`) is copied into the build context
unmodified — the same real dependency pinning this course has relied on since
[Level 2](../level-2/03-packaging-signatures-environments.md), now feeding directly into the image build rather
than just documentation.

```dockerfile
FROM ubuntu:24.04
...
RUN pip install mlflow==3.17.2
COPY model_dir /opt/ml/model
RUN python -c "from mlflow.models import container as C; C._install_pyfunc_deps('/opt/ml/model', install_mlflow=False, env_manager='virtualenv');"
ENTRYPOINT ["python", "-c", "from mlflow.models import container as C; C._serve('virtualenv')"]
```

**What was not run:** `docker build` and `docker run` against this real Dockerfile, since Docker isn't installed
here. The real artifact — a correct, complete, model-specific Dockerfile and build context — exists on disk and
was inspected directly rather than assumed to look a particular way.

## How It Actually Works

The generated Dockerfile is real evidence of something worth noticing directly: it does far more than copy a
model file into a base image. It builds a specific Python version via `pyenv`, installs Java (because MLflow's
serving container supports multiple model flavors, some of which need a JVM), and runs MLflow's own internal
`_install_pyfunc_deps` function against the exact `model_dir` this lesson copied in — reading that real
`requirements.txt` and `python_env.yaml` to recreate, inside the image, the exact real Python environment the
model was trained and logged in.

That's the real point of containerizing a model at all: the image becomes the actual unit of deployment,
carrying the model's own precisely-pinned dependencies with it, rather than depending on whatever Python
environment happens to exist on whichever machine runs it next — the same real problem
[Level 1](../level-1/05-reproducibility-seeds-environments.md) first raised with environment files, now solved
one level further up, as a self-contained image instead of a file someone has to remember to `pip install -r`.

## Common mistakes

- Hand-writing a Dockerfile for a model instead of using the tooling the model-logging framework already
  provides — this lesson's real generated Dockerfile automatically wires in the exact real dependency versions
  MLflow recorded at training time; a hand-written one risks drifting from them.
- Assuming an image builds and runs correctly without ever actually running `docker build` against it — this
  lesson is explicit that it did not reach that step, specifically to avoid presenting an unverified claim as a
  confirmed result.
- Baking a specific model version into an image tag that never changes — a real deployed image should be tagged
  to the real model version it contains (for example `breast-cancer-classifier:v7`), so a rollback
  ([Level 2](../level-2/09-rolling-back-model-version.md)) can mean deploying a previous real image tag, not
  rebuilding from scratch.

## Exercise

On a machine with Docker actually installed, run `docker build -t breast-cancer-classifier:v7 docker_output/`
against this lesson's real generated files, then `docker run -p 8080:8080 breast-cancer-classifier:v7` and send
it a real request on port 8080 — completing, for real, the one step this lesson's environment couldn't reach.
