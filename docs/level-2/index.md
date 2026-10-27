---
description: "MLOps Level 2: the model registry, stages, packaging with signatures, model lineage, approval workflows, and serving a real model locally."
---

# Level 2 · Model Registry & Packaging

Level 2 moves from "a tracked experiment" to "a deployable artifact": a real model registry that versions models
the way git versions code, a real promotion workflow that gates what actually reaches production, and a real,
locally-served inference endpoint that proves a registered model can actually answer a request.

!!! note "What was run, and what wasn't"
    This level uses a real local MLflow Model Registry, registering real models from Level 1's real tracked runs,
    promoting them through real stage transitions, and serving one with a real local inference server. A full
    managed model-serving platform at production scale was not run as part of this course; the underlying
    serving mechanism is shown directly and honestly instead.

**Prerequisites:** Level 1.

## Modules

1. **[The Model Registry: Versioning Models Like Code](01-model-registry-fundamentals.md)**: a real model
   registered, with a real, incrementing version number.
2. **[Model Stages: Staging, Production, Archived](02-model-stages.md)**: a real model moved through real stage
   transitions, confirmed at each step.
3. **[Packaging a Model: Signatures & Environments](03-packaging-signatures-environments.md)**: a real model
   signature that catches a real malformed request before it reaches the model.
4. **[Model Lineage: Tracing a Model Back to Its Run](04-model-lineage.md)**: a real registered model traced back
   to the exact real run, data, and code that produced it.
5. **[Comparing Model Versions: Is the New One Better?](05-comparing-model-versions.md)**: a real side-by-side
   comparison deciding whether a new version actually deserves to replace the old one.
6. **[Model Approval Workflows: Gating Production](06-model-approval-workflows.md)**: a real gate that blocks an
   unapproved model from reaching production.
7. **[Serving a Model Locally: a Real Inference Endpoint](07-serving-model-locally.md)**: a real HTTP endpoint,
   backed by a real registered model, answering a real request.
8. **[Input/Output Schemas: Catching Bad Requests Early](08-input-output-schemas.md)**: a real malformed request
   rejected before it ever reaches the model.
9. **[Rolling Back a Bad Model Version](09-rolling-back-model-version.md)**: a real rollback, executed and
   verified.
10. **[Project: Register, Promote & Serve a Real Model](10-project-register-promote-serve.md)**: the full real
    lifecycle, end to end.

## After Level 2

Level 3 automates everything built by hand in Levels 1–2 into a real pipeline: training, testing, and deploying a
model through a real CI/CD process, not a sequence of manual steps.
