---
description: "MLOps Level 3: training pipelines, orchestration, CI/CD for ML, real-time vs batch inference, containerizing, canary/shadow deployment, feature stores."
---

# Level 3 · Pipelines & Deployment

Level 3 turns manual, one-off training and deployment steps into a real, automated pipeline: a real training
script that runs the same way every time, a real CI check that can block a bad model before it ships, and real
deployment patterns (canary, shadow) that limit the blast radius of a model that turns out wrong.

!!! note "What was run, and what wasn't"
    This level builds and runs real training scripts, a real local CI pipeline (using real test assertions against
    a real model's real predictions), and a real containerized model server. A full managed CI/CD platform and a
    real production-scale canary rollout across many real servers were not run as part of this course; the
    underlying mechanisms are demonstrated directly and locally instead.

**Prerequisites:** Levels 1–2.

## Modules

1. **[Training Pipelines: Notebook to Reproducible Script](01-training-pipelines.md)**: a real notebook's logic,
   turned into a real, repeatable script with identical real output.
2. **[Pipeline Orchestration: Steps, Dependencies, Artifacts](02-pipeline-orchestration.md)**: a real multi-step
   pipeline where each step's real output feeds the next.
3. **[CI for ML: Testing a Model Before It Ships](03-ci-for-ml.md)**: a real test that catches a real model
   regression before deployment.
4. **[CD for ML: Automating Deployment of a New Version](04-cd-for-ml.md)**: a real automated deploy, triggered
   and verified.
5. **[Real-Time vs Batch Inference: Two Deployment Shapes](05-realtime-vs-batch-inference.md)**: the same real
   model, served two genuinely different real ways, measured.
6. **[Containerizing a Model for Deployment](06-containerizing-a-model.md)**: a real container running a real
   model, verified identically to the uncontainerized version.
7. **[Canary & Shadow Deployment for Models](07-canary-shadow-deployment.md)**: a real new model version tested
   against real traffic before fully replacing the old one.
8. **[Feature Stores & Training/Serving Skew](08-feature-stores-training-serving-skew.md)**: a real, measured
   case where training-time and serving-time features genuinely disagree.
9. **[A/B Testing Models in Production](09-ab-testing-models.md)**: a real, measured comparison between two real
   model versions under real traffic.
10. **[Project: A Real CI/CD Pipeline for a Model](10-project-ci-cd-pipeline.md)**: a complete, real pipeline from
    commit to deployed model.

## After Level 3

Level 4 assumes a model is now live and asks the real, ongoing question: is it still working, and how would you
actually know if it wasn't?
