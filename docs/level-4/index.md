---
description: "MLOps Level 4: data and concept drift, monitoring without ground truth, retraining triggers, governance, cost/latency tradeoffs, and a capstone."
---

# Level 4 · Monitoring, Drift & Operating ML at Scale

Level 4 is about the question a working deployment pipeline doesn't answer on its own: is the model still
actually good, right now, with today's real data — not the data it was trained on? This level builds real drift
detection, real monitoring that doesn't require waiting for ground-truth labels, and a real, deliberate decision
process for when to retrain.

!!! note "What was run, and what wasn't"
    This level computes real statistical drift measures against real, deliberately-shifted data, and builds real
    monitoring and alerting logic around a real served model. A full, large-scale production monitoring platform
    was not run as part of this course; the real statistical and architectural mechanisms underneath one are
    built and verified directly instead.

**Prerequisites:** Levels 1–3.

## Modules

1. **[Why Monitoring a Model Differs From a Service](01-monitoring-models-vs-services.md)**: a real case where a
   model can be "up" and fully healthy by every infrastructure metric while being badly wrong.
2. **[Data Drift: When the Input Distribution Changes](02-data-drift.md)**: real, measured drift in real input
   data, detected statistically.
3. **[Concept Drift: When the Relationship Itself Changes](03-concept-drift.md)**: a real case where the inputs
   look the same but what they predict has genuinely changed.
4. **[Model Performance Monitoring Without Ground Truth](04-monitoring-without-ground-truth.md)**: real proxy
   signals used to catch real degradation before true labels ever arrive.
5. **[Alerting on Model Degradation](05-alerting-on-degradation.md)**: a real alert that fires against a real,
   injected model degradation.
6. **[Retraining Triggers: When and How to Retrain](06-retraining-triggers.md)**: a real, principled decision
   process, not a fixed retraining schedule.
7. **[Model Governance & Auditability](07-model-governance-auditability.md)**: a real, complete audit trail for
   one real model's entire lifecycle.
8. **[Cost & Latency Tradeoffs in Model Serving](08-cost-latency-tradeoffs.md)**: a real, measured tradeoff
   between model size, latency, and serving cost.
9. **[Common MLOps Anti-Patterns](09-mlops-antipatterns.md)**: real, measured costs of getting this wrong,
   tallied from across the whole course.
10. **[Capstone: A Complete MLOps Loop for a Real Model](10-capstone-complete-mlops-loop.md)**: tracking,
    registry, pipeline, and monitoring, for one real model, end to end.

## After Level 4

You've tracked a real model, registered and served it, automated its deployment, and built real monitoring to
catch it degrading — the same loop a real production ML team runs continuously, just at a scale you can finish on
a laptop.
