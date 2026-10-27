---
description: "MLOps Level 1: experiment tracking fundamentals, logging metrics and artifacts, comparing runs, reproducibility, data lineage, and hyperparameter sweeps."
---

# Level 1 · Experiment Tracking & Reproducibility

Level 1 builds the foundation everything else in this course depends on: a disciplined, automatic record of every
real training run — what data, what parameters, what code, what result — so that six months from now, any model
in production can be traced back to exactly how it was made. Every run, comparison, and metric shown here comes
from a real local MLflow tracking server.

!!! note "What was run, and what wasn't"
    This course runs a real local MLflow tracking server, logging real training runs of real scikit-learn models
    against real datasets. Every run ID, metric value, and comparison in this level is real, observed output from
    that server. Where a lesson describes behavior only visible at a scale this single-laptop setup couldn't
    reproduce (a large distributed hyperparameter sweep across a cluster, for instance), it says so plainly.

**Prerequisites:** basic Python and command-line comfort, and some familiarity with training a model (any
model — this course doesn't teach modeling itself). No prior MLOps tooling experience assumed.

## Modules

1. **[What MLOps Actually Solves: Notebook to Production](01-what-mlops-solves.md)**: the real, specific gap
   between a model that works once and a model that works reliably over time.
2. **[Experiment Tracking Fundamentals: Runs, Params, Metrics](02-experiment-tracking-fundamentals.md)**: a real
   run logged to a real tracking server, inspected directly.
3. **[Logging Metrics & Artifacts During Training](03-logging-metrics-artifacts.md)**: real per-epoch metrics and
   a real saved model artifact, not just a final number.
4. **[Comparing Runs: Finding the Best Model Objectively](04-comparing-runs.md)**: a real query across many real
   runs to find the actual best one, not a remembered guess.
5. **[Reproducibility: Seeds, Environments & Data Versions](05-reproducibility-seeds-environments.md)**: a real
   re-run that either reproduces exactly, or reveals exactly why it doesn't.
6. **[Tracking Data Lineage: Which Data Trained Which Model](06-data-lineage.md)**: a real dataset hash logged
   alongside a real run.
7. **[Hyperparameter Sweeps: Tracking Many Runs at Once](07-hyperparameter-sweeps.md)**: a real sweep of dozens
   of real runs, tracked and compared automatically.
8. **[Autologging: What It Captures and What It Misses](08-autologging.md)**: a real, honest audit of what
   autologging actually logs versus what a team assumes it logs.
9. **[Tagging & Organizing Experiments at Scale](09-tagging-organizing-experiments.md)**: real tags used to find
   one real run out of hundreds.
10. **[Project: Track a Real Training Run End to End](10-project-track-training-run.md)**: a real model, fully
    tracked from first run to final comparison.

## After Level 1

Level 2 takes the best tracked run and turns it into something that can actually be deployed: a versioned,
registered model with a real promotion workflow, not just a file saved on someone's laptop.
