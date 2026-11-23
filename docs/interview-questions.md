---
title: "MLOps Interview Questions and Answers (180 Questions)"
description: "180 MLOps interview questions with answers and explanations, from beginner to advanced, organised by level. Free, from the MLOps Mastery Path."
---

# MLOps Interview Questions and Answers

**180 questions** with the correct answer and a short explanation, organised from beginner (Level 1) to advanced (Level 4). Every question comes from a lesson in the free MLOps Mastery Path, so you can open the lesson to go deeper.

!!! tip "Practise, don't just read"
    Each lesson also ends with an interactive quiz, and the [final quiz](quiz.md) tests all four levels. Try to answer each question below before reading the answer.

## Level 1 · Experiment Tracking & Reproducibility

### [What MLOps Actually Solves: Notebook to Production](level-1/01-what-mlops-solves.md)

**Q1.** What problem does MLOps solve?

**Answer:** Reliably taking models from experiments to production and keeping them healthy

Operational discipline for ML.

**Q2.** Why do ML projects fail to reach production?

**Answer:** Irreproducible experiments, unmanaged data/models and no deployment or monitoring path

Hidden technical debt.

**Q3.** How does ML differ from traditional software to operate?

**Answer:** Behavior depends on data and degrades as data changes, not just on code

Data is part of the system.

**Q4.** What are the lifecycle stages?

**Answer:** Data, experiment, train, validate, deploy, monitor and retrain

A loop.

### [Experiment Tracking Fundamentals: Runs, Params, Metrics](level-1/02-experiment-tracking-fundamentals.md)

**Q5.** What does experiment tracking record?

**Answer:** Parameters, metrics, artifacts, code version and data version per run

So results are reproducible.

**Q6.** What's an MLflow run?

**Answer:** One execution of training code with its logged information

Grouped into experiments.

**Q7.** Why track runs rather than keeping notebooks?

**Answer:** Comparisons and reproducibility need structured records

Notebook state is lost.

**Q8.** What should always be logged?

**Answer:** Hyperparameters and evaluation metrics

Core run data.

### [Logging Metrics &amp; Artifacts During Training](level-1/03-logging-metrics-artifacts.md)

**Q9.** What are artifacts?

**Answer:** Files produced by a run such as models, plots and reports

log_artifact.

**Q10.** What's the difference between a param and a metric?

**Answer:** Params are inputs (fixed); metrics are outputs (can be logged over time)

Step-wise metrics.

**Q11.** Why log metrics per epoch?

**Answer:** To see convergence and overfitting

Curves.

**Q12.** Where store large artifacts?

**Answer:** Object storage referenced by the tracking server

Artifact store.

### [Comparing Runs: Finding the Best Model Objectively](level-1/04-comparing-runs.md)

**Q13.** How compare runs fairly?

**Answer:** Same data split, metrics and conditions, focusing on the target metric

Controlled.

**Q14.** What does a parallel coordinates plot show?

**Answer:** Relationships between hyperparameters and metrics across runs

Sweeps.

**Q15.** Why not pick the best run by test score repeatedly?

**Answer:** You overfit the test set; keep a final holdout

Selection bias.

**Q16.** What helps compare noisy runs?

**Answer:** Repeated runs with different seeds

Variance.

### [Reproducibility: Seeds, Environments &amp; Data Versions](level-1/05-reproducibility-seeds-environments.md)

**Q17.** Two runs with the same seed produce what with the same code and data?

```python
import random
random.seed(7);a=[random.random() for _ in range(3)]
random.seed(7);b=[random.random() for _ in range(3)]
print(a==b)
```

**Answer:** True

Seeds make randomness repeatable. Verified by running it.

**Q18.** What else besides seeds affects reproducibility?

**Answer:** Library versions, hardware/nondeterminism, data versions and configuration

Pin environments.

**Q19.** How capture the environment?

**Answer:** requirements/conda lock files or container images logged with the run

Same environment.

**Q20.** Why log the git commit?

**Answer:** To rebuild the exact code used

Provenance.

### [Tracking Data Lineage: Which Data Trained Which Model](level-1/06-data-lineage.md)

**Q21.** What is data lineage?

**Answer:** Tracking where data came from and how it was transformed into a model's inputs

Auditability.

**Q22.** Why version datasets?

**Answer:** To reproduce training and explain model behavior

DVC/lakeFS/hashes.

**Q23.** What's a cheap lineage start?

**Answer:** Log dataset path/hash and row counts with every run

Minimum.

**Q24.** What does lineage help during incidents?

**Answer:** Finding which data and code produced a bad model

Root cause.

### [Hyperparameter Sweeps: Tracking Many Runs at Once](level-1/07-hyperparameter-sweeps.md)

**Q25.** What is a hyperparameter sweep?

**Answer:** Running training across parameter combinations to find good settings

Grid/random/Bayesian.

**Q26.** Why prefer random over grid search?

**Answer:** It explores more distinct values per important parameter

Bergstra &amp; Bengio.

**Q27.** What must a sweep respect?

**Answer:** A separate validation set and a compute budget

Avoid leakage.

**Q28.** How organize sweep runs?

**Answer:** Nested/parent-child runs with tags

Comparison.

### [Autologging: What It Captures and What It Misses](level-1/08-autologging.md)

**Q29.** What does autologging do?

**Answer:** Automatically records params, metrics and models for supported libraries

mlflow.autolog().

**Q30.** What's a risk of relying only on autolog?

**Answer:** Missing custom metrics, data versions or business context

Add explicit logs.

**Q31.** When enable it?

**Answer:** At the start of the script before training

Hooks into libraries.

**Q32.** What do you still log manually?

**Answer:** Dataset version, tags and domain-specific metrics

Context.

### [Tagging &amp; Organizing Experiments at Scale](level-1/09-tagging-organizing-experiments.md)

**Q33.** Why tag runs?

**Answer:** Filtering and grouping by team, dataset, purpose or status

Organization.

**Q34.** What naming convention helps experiments?

**Answer:** Consistent names by project/problem and tags for variants

Findability.

**Q35.** What's an experiment vs a run?

**Answer:** An experiment groups related runs

Hierarchy.

**Q36.** What's a good tag?

**Answer:** owner, dataset_version, objective and git_sha

Queryable.

### [Project: Track a Real Training Run End to End](level-1/10-project-track-training-run.md)

**Q37.** What does the tracking project combine?

**Answer:** Seeds, environment capture, params/metrics/artifacts and tags in one training run

Level 1 project.

**Q38.** How prove reproducibility?

**Answer:** Re-run from the logged information and compare metrics

Verify.

**Q39.** What's the deliverable?

**Answer:** A run someone else can reproduce and explain

Provenance.

**Q40.** What should the run link to?

**Answer:** Code commit, data version and environment

Lineage.

## Level 2 · Model Registry & Packaging

### [The Model Registry: Versioning Models Like Code](level-2/01-model-registry-fundamentals.md)

**Q41.** What is a model registry?

**Answer:** A central store of versioned models with metadata and lifecycle management

Governed models.

**Q42.** What does registering link?

**Answer:** A model version to the run/code/data that produced it

Lineage.

**Q43.** Why not pass pickles around by email?

**Answer:** No versioning, provenance or controlled promotion

Registry.

**Q44.** What's a registered model version?

**Answer:** An immutable numbered snapshot of a model

v1, v2.

### [Model Stages: Staging, Production, Archived](level-2/02-model-stages.md)

**Q45.** What do stages/aliases represent?

**Answer:** Lifecycle states such as staging and production

Promotion.

**Q46.** Why use aliases (champion) over stage names in newer MLflow?

**Answer:** Flexible, explicit pointers decoupled from fixed stages

Deployments reference aliases.

**Q47.** What should consumers reference?

**Answer:** An alias/stage, not a hard-coded version number

Easy rollback.

**Q48.** What happens on promotion?

**Answer:** The pointer moves to a new validated version

Controlled.

### [Packaging a Model: Signatures &amp; Environments](level-2/03-packaging-signatures-environments.md)

**Q49.** What is a model signature?

**Answer:** The expected input and output schema of a model

Validation.

**Q50.** Why capture the environment with the model?

**Answer:** Serving must recreate dependencies to behave the same

conda.yaml.

**Q51.** What's a model flavor?

**Answer:** A standard packaging format for a framework (sklearn, pyfunc)

Portability.

**Q52.** What does pyfunc provide?

**Answer:** A generic Python function interface to any logged model

Uniform predict().

### [Model Lineage: Tracing a Model Back to Its Run](level-2/04-model-lineage.md)

**Q53.** What does model lineage show?

**Answer:** The run, data, code and parameters behind a registered version

Traceability.

**Q54.** Why needed for audits?

**Answer:** To explain how a production model was built

Governance.

**Q55.** What does linking register-time tags add?

**Answer:** Evaluation reports and approvals

Evidence.

**Q56.** What happens if lineage is missing?

**Answer:** You can't reproduce or explain a production model

Risk.

### [Comparing Model Versions: Is the New One Actually Better?](level-2/05-comparing-model-versions.md)

**Q57.** How compare two model versions properly?

**Answer:** Same evaluation data and metrics, including slices and latency

Fair.

**Q58.** What do slice metrics reveal?

**Answer:** Performance on subgroups hidden by averages

Fairness and robustness.

**Q59.** What non-accuracy criteria matter?

**Answer:** Latency, memory, cost and stability

Production fit.

**Q60.** What's a regression test for models?

**Answer:** A fixed set of cases the new model must still pass

Golden set.

### [Model Approval Workflows: Gating Production](level-2/06-model-approval-workflows.md)

**Q61.** What does an approval workflow add?

**Answer:** Human/automated gates before promoting to production

Controls.

**Q62.** What can automated gates check?

**Answer:** Metric thresholds, schema compatibility and tests

Policy as code.

**Q63.** Why require reviewer sign-off?

**Answer:** Accountability for models affecting users

Governance.

**Q64.** What should be recorded?

**Answer:** Who approved, when and based on what evidence

Audit.

### [Serving a Model Locally: a Real Inference Endpoint](level-2/07-serving-model-locally.md)

**Q65.** What does serving a model locally let you do?

**Answer:** Test the packaged model behind an HTTP API before deployment

mlflow models serve.

**Q66.** What endpoint style is common?

**Answer:** POST /invocations with JSON/CSV input

Standard.

**Q67.** Why test serving early?

**Answer:** Packaging and dependency problems appear before production

Shift left.

**Q68.** What should you check locally?

**Answer:** Schema validation, latency and error responses

Behavior.

### [Input/Output Schemas: Catching Bad Requests Early](level-2/08-input-output-schemas.md)

**Q69.** Why validate input schemas at serving time?

**Answer:** Reject malformed requests instead of returning wrong predictions

Fail clearly.

**Q70.** What's the risk of silent type coercion?

**Answer:** Wrong predictions with no error

Strict schemas.

**Q71.** What should errors return?

**Answer:** Clear 4xx messages for invalid input

Client errors.

**Q72.** What happens when features are missing?

**Answer:** Reject or handle explicitly per policy, not guess

Defined behavior.

### [Rolling Back a Bad Model Version](level-2/09-rolling-back-model-version.md)

**Q73.** How roll back a bad model?

**Answer:** Point the production alias back to the previous version

Fast.

**Q74.** What makes rollback fast?

**Answer:** Versioned immutable models and alias-based deployments

Prepared.

**Q75.** What should trigger rollback?

**Answer:** Monitored degradation or failed health checks

Automation.

**Q76.** What do you do after rollback?

**Answer:** Investigate root cause before retrying promotion

Postmortem.

### [Project: Register, Promote &amp; Serve a Real Model](level-2/10-project-register-promote-serve.md)

**Q77.** What does the register/promote/serve project combine?

**Answer:** Registering a model, gating promotion and serving it by alias

Level 2 project.

**Q78.** What proves the gate works?

**Answer:** A worse model fails promotion and a better one passes

Test the policy.

**Q79.** How verify serving?

**Answer:** Call the endpoint with valid and invalid payloads

Contract.

**Q80.** What's the key artifact?

**Answer:** A versioned, documented model in the registry

Handover.

## Level 3 · Pipelines & Deployment

### [Training Pipelines: Notebook to Reproducible Script](level-3/01-training-pipelines.md)

**Q81.** What is a training pipeline?

**Answer:** Automated, repeatable steps from data to a validated model

Orchestrated.

**Q82.** Why parameterize pipelines?

**Answer:** Reuse across datasets, dates and configs

Retraining.

**Q83.** What steps are typical?

**Answer:** Ingest, validate, featurize, train, evaluate, register

DAG.

**Q84.** Why data validation up front?

**Answer:** Bad data silently produces bad models

Gate early.

### [Pipeline Orchestration: Steps, Dependencies, Artifacts](level-3/02-pipeline-orchestration.md)

**Q85.** What does an orchestrator provide?

**Answer:** Scheduling, dependencies, retries and visibility for pipeline steps

Airflow/Kubeflow/Dagster.

**Q86.** Why idempotent steps?

**Answer:** Retries and backfills must be safe

Deterministic outputs.

**Q87.** What should steps pass between each other?

**Answer:** Versioned artifacts, not local files

Reproducible.

**Q88.** How trigger retraining?

**Answer:** Schedule, new data arrival or monitoring signals

Automation.

### [CI for ML: Testing a Model Before It Ships](level-3/03-ci-for-ml.md)

**Q89.** What does CI for ML test?

**Answer:** Code, data validation, training smoke tests and model checks

Beyond unit tests.

**Q90.** What's a training smoke test?

**Answer:** A tiny-data run proving the pipeline works end to end

Fast.

**Q91.** What data tests help?

**Answer:** Schema, ranges, null rates and distribution checks

Great Expectations.

**Q92.** What should fail a PR?

**Answer:** Broken tests or a metric regression below the gate

Quality.

### [CD for ML: Automating Deployment of a New Version](level-3/04-cd-for-ml.md)

**Q93.** What does CD for ML deploy?

**Answer:** Validated models and serving code through staged environments

Automated.

**Q94.** What gates promotion?

**Answer:** Evaluation thresholds, tests and approvals

Policy.

**Q95.** How separate model and code releases?

**Answer:** Version the model independently from the service code

Faster updates.

**Q96.** What enables safe rollout?

**Answer:** Staged deploys with monitoring and rollback

Risk control.

### [Real-Time vs Batch Inference: Two Deployment Shapes](level-3/05-realtime-vs-batch-inference.md)

**Q97.** When choose batch inference?

**Answer:** When predictions can be computed ahead (reports, recommendations)

Cheaper.

**Q98.** When choose real-time?

**Answer:** When a decision needs a fresh prediction per request

Latency budget.

**Q99.** What does online inference require?

**Answer:** Low latency, scaling and the same features as training

Consistency.

**Q100.** What hybrid exists?

**Answer:** Precompute common predictions and compute the rest online

Trade-off.

### [Containerizing a Model for Deployment](level-3/06-containerizing-a-model.md)

**Q101.** Why containerize a model?

**Answer:** Reproducible environment and portable deployment

Docker.

**Q102.** What belongs in the image?

**Answer:** Model artifact, pinned dependencies and the serving code

Minimal.

**Q103.** How keep images small?

**Answer:** Multi-stage builds and slim bases

Faster pulls.

**Q104.** What health endpoint should exist?

**Answer:** Readiness/liveness checks (model loaded)

Orchestration.

### [Canary &amp; Shadow Deployment for Models](level-3/07-canary-shadow-deployment.md)

**Q105.** What is shadow deployment?

**Answer:** The new model receives live traffic but its outputs aren't used

Safe comparison.

**Q106.** What is a canary?

**Answer:** Serving a small share of real traffic with the new model

Gradual.

**Q107.** What do you compare in shadow mode?

**Answer:** New vs current predictions and latency without user impact

Offline-like.

**Q108.** When promote a canary?

**Answer:** When metrics are acceptable against the baseline

Automated checks.

### [Feature Stores &amp; Training/Serving Skew](level-3/08-feature-stores-training-serving-skew.md)

**Q109.** What is training-serving skew?

**Answer:** Features computed differently in training and production

Silent errors.

**Q110.** What does a feature store provide?

**Answer:** Consistent, reusable features for offline and online use

One definition.

**Q111.** What prevents skew without a store?

**Answer:** Sharing the same transformation code in both paths

Single source.

**Q112.** What's point-in-time correctness?

**Answer:** Using only data available at prediction time when building training sets

Avoid leakage.

### [A/B Testing Models in Production](level-3/09-ab-testing-models.md)

**Q113.** Why A/B test models?

**Answer:** Offline metrics don't always predict business impact

Online evaluation.

**Q114.** What must be defined before the test?

**Answer:** The primary metric, sample size and duration

Avoid peeking.

**Q115.** What randomization unit is typical?

**Answer:** User (not request) to avoid inconsistent experience

Sticky assignment.

**Q116.** What guardrail metrics protect?

**Answer:** Latency, errors and key business outcomes

Do no harm.

### [Project: A Real CI/CD Pipeline for a Model](level-3/10-project-ci-cd-pipeline.md)

**Q117.** What does the CI/CD pipeline project combine?

**Answer:** Tests, training, evaluation gates, containerization and staged deployment

Level 3 project.

**Q118.** How prove the gate works?

**Answer:** A degraded model is blocked automatically

Negative test.

**Q119.** What's the pipeline's output?

**Answer:** A tested, versioned, deployable model service

Release.

**Q120.** What do you log for traceability?

**Answer:** Commit, data version, metrics and image digest

Provenance.

## Level 4 · Monitoring, Drift & Operating ML at Scale

### [Why Monitoring a Model Differs From a Service](level-4/01-monitoring-models-vs-services.md)

**Q121.** How is monitoring a model different from monitoring a service?

**Answer:** You must watch data and prediction quality, not just latency and errors

ML-specific signals.

**Q122.** What service metrics still matter?

**Answer:** Latency, throughput, errors and resource use

Foundation.

**Q123.** What's silent failure?

**Answer:** The service is healthy but predictions are wrong

Needs ML monitoring.

**Q124.** Where log predictions?

**Answer:** With inputs, model version and timestamps for later analysis

Enables drift and labeling.

### [Data Drift: When the Input Distribution Changes](level-4/02-data-drift.md)

**Q125.** What is data drift?

**Answer:** The distribution of input features changes from training

Covariate shift.

**Q126.** What statistic compares distributions?

**Answer:** PSI, KS test or Jensen-Shannon distance

Thresholds.

**Q127.** Does drift always mean worse performance?

**Answer:** No, but it's a warning to investigate

Check impact.

**Q128.** What reference should drift compare against?

**Answer:** The training (or recent healthy) data

Baseline.

### [Concept Drift: When the Relationship Itself Changes](level-4/03-concept-drift.md)

**Q129.** What is concept drift?

**Answer:** The relationship between inputs and target changes over time

Fraud patterns change.

**Q130.** How detect it?

**Answer:** Monitor performance on labeled data as labels arrive

Needs ground truth.

**Q131.** What's the difference from data drift?

**Answer:** Concept drift changes P(y|x); data drift changes P(x)

Different causes.

**Q132.** What responds to concept drift?

**Answer:** Retraining on recent data or model redesign

Adapt.

### [Model Performance Monitoring Without Ground Truth](level-4/04-monitoring-without-ground-truth.md)

**Q133.** How monitor when labels arrive late or never?

**Answer:** Proxy signals: input drift, prediction distribution and business metrics

Indirect.

**Q134.** What does a shifting prediction distribution suggest?

**Answer:** Inputs or behavior changed, warranting investigation

Early warning.

**Q135.** What's delayed labeling?

**Answer:** Outcomes known days or weeks after predictions

Backfill metrics.

**Q136.** What can approximate ground truth?

**Answer:** Human review samples or downstream outcomes

Sampling.

### [Alerting on Model Degradation](level-4/05-alerting-on-degradation.md)

**Q137.** What makes a good ML alert?

**Answer:** Actionable, tied to impact, with thresholds and runbooks

Avoid fatigue.

**Q138.** What's alert fatigue?

**Answer:** So many alerts that real problems are ignored

Tune.

**Q139.** What severity should drift alone have?

**Answer:** Lower than a confirmed performance drop

Investigate first.

**Q140.** What should an alert link to?

**Answer:** Dashboards, recent changes and the model version

Debugging.

### [Retraining Triggers: When and How to Retrain](level-4/06-retraining-triggers.md)

**Q141.** What triggers retraining?

**Answer:** Schedule, drift/performance thresholds or enough new labeled data

Policy.

**Q142.** What must happen before a retrained model ships?

**Answer:** Validation against the current model and the same gates

No blind updates.

**Q143.** What's a risk of automatic retraining?

**Answer:** Learning from bad or poisoned data and degrading silently

Guardrails.

**Q144.** What record keeps retraining auditable?

**Answer:** Data version, parameters and approval history

Lineage.

### [Model Governance &amp; Auditability](level-4/07-model-governance-auditability.md)

**Q145.** What does model governance cover?

**Answer:** Ownership, documentation, approvals, risk and audit trails

Accountability.

**Q146.** What's a model card?

**Answer:** Documentation of purpose, data, performance, limits and risks

Transparency.

**Q147.** Why keep audit logs of predictions?

**Answer:** To explain and investigate decisions affecting people

Compliance.

**Q148.** What regulations may apply?

**Answer:** GDPR/automated decision rules and sector regulations

Depends on use.

### [Cost &amp; Latency Tradeoffs in Model Serving](level-4/08-cost-latency-tradeoffs.md)

**Q149.** How trade latency against accuracy?

**Answer:** Pick the smallest model meeting the latency and quality targets

Budgets.

**Q150.** What reduces serving cost?

**Answer:** Batching, quantization/distillation, caching and autoscaling

Efficiency.

**Q151.** What's a cascade?

**Answer:** Cheap model first, expensive model only for hard cases

Cost saving.

**Q152.** What should you measure?

**Answer:** Cost per prediction and p95 latency with quality

Trade-off curve.

### [Common MLOps Anti-Patterns](level-4/09-mlops-antipatterns.md)

**Q153.** Which is an MLOps antipattern?

**Answer:** Manual deploys from a notebook with no versioning

Fix with pipelines.

**Q154.** Another antipattern?

**Answer:** Training and serving using different feature code

Skew.

**Q155.** Another?

**Answer:** No monitoring after launch

Models decay.

**Q156.** Another?

**Answer:** Tuning on the test set

Optimistic metrics.

### [Capstone: A Complete MLOps Loop for a Real Model](level-4/10-capstone-complete-mlops-loop.md)

**Q157.** What does the complete MLOps loop include?

**Answer:** Tracking, registry, CI/CD, serving, monitoring and retraining

Closed loop.

**Q158.** How prove the loop works?

**Answer:** Inject drift and watch detection, alert, retrain and safe promotion

End-to-end test.

**Q159.** What documentation helps?

**Answer:** Runbooks, model cards and architecture decisions

Operate.

**Q160.** What sustains it?

**Answer:** Ownership, monitoring and continuous improvement

Lifecycle.

## Mixed questions across all levels

**Q161.** A model has 92% test accuracy but nobody can tell which data or code produced it. What is missing?

**Answer:** Experiment tracking and lineage (params, metrics, artifacts, code and data versions)

Reproducibility.

**Q162.** With the same seed, do two random draws match?

```python
import random
random.seed(3);a=random.random()
random.seed(3);b=random.random()
print(a==b)
```

**Answer:** True

Seeds make randomness repeatable, but library versions and hardware also matter. Verified by running it.

**Q163.** You tuned hyperparameters repeatedly against the test set. What's wrong?

**Answer:** The test set is now overfit; keep a separate final holdout

Selection bias.

**Q164.** A production service should load 'the best model'. How should it reference it?

**Answer:** By a registry alias (e.g. production), not a hard-coded version or file path

Promotion and rollback by moving the alias.

**Q165.** What should a promotion gate check?

**Answer:** Metric thresholds, regression vs the current model, latency and schema compatibility

Policy as code.

**Q166.** Predictions are 'wrong' after a deploy though code is fine. A classic cause?

**Answer:** Training-serving skew: features computed differently online

Share transformations or use a feature store.

**Q167.** A request has an unexpected column type and the service returns a prediction anyway. What was missing?

**Answer:** Input schema validation at serving time

Fail clearly instead of silently coercing.

**Q168.** A new model must be evaluated on live traffic without affecting users. Which strategy?

**Answer:** Shadow deployment (compare outputs, don't use them)

Safe comparison.

**Q169.** A small percentage of users get the new model and metrics are checked before widening. What is it?

**Answer:** A canary release

Gradual exposure.

**Q170.** Why randomize A/B tests by user rather than by request?

**Answer:** A user should have a consistent experience and independent samples

Sticky assignment.

**Q171.** CI for ML should include which checks beyond unit tests?

**Answer:** Data validation, a small training smoke test and model metric gates

Ml-specific CI.

**Q172.** The pipeline retrains every night and one night the model degrades silently. What guards against shipping it?

**Answer:** Evaluation gates against the current production model before promotion

No blind updates.

**Q173.** Input feature distributions shift but labels aren't available yet. Which signal helps?

**Answer:** Data drift metrics (PSI/KS) and prediction distribution monitoring

Proxy signals.

**Q174.** A PSI of 0.3 is typically interpreted as?

```python
p=0.3
print('stable' if p<0.1 else 'moderate drift' if p<0.25 else 'significant drift')
```

**Answer:** significant drift

Common rule of thumb: &lt;0.1 stable, 0.1-0.25 moderate, &gt;0.25 significant. Verified by running it.

**Q175.** Fraud patterns change so inputs look similar but outcomes differ. What is it?

**Answer:** Concept drift (the input-output relationship changed)

Needs labeled outcomes to detect.

**Q176.** What makes an ML alert useful?

**Answer:** It's actionable, tied to impact and links to the model version and runbook

Avoid alert fatigue.

**Q177.** When is automatic retraining risky?

**Answer:** When bad or poisoned data can flow in and degrade the model silently

Add validation and gates.

**Q178.** Which artifact documents a model's purpose, data, limits and risks?

**Answer:** A model card

Governance.

**Q179.** A cheaper model handles most requests and an expensive one the hard cases. What is this?

**Answer:** A cascade (cost/latency trade-off)

Serving cost control.

**Q180.** Which is an MLOps anti-pattern?

**Answer:** Deploying manually from a notebook with no versioning or monitoring

Fix with pipelines and registry.

## Practical coding questions

### Detect data drift with PSI and KS

Implement psi (binned population stability index using the reference range, clamping out-of-range live values), level (stable / moderate / significant) and a two-sample KS statistic. Compare a live sample from the same distribution with one whose mean has shifted.

Starter code:

```
import math

def psi(expected, actual, bins=5):
    # bin both samples with edges from the reference range (clamp out-of-range values into the end bins),
    # turn counts into shares (floor at 1e-4) and return sum((a - e) * ln(a / e))
    return 0.0

def level(p):
    # < 0.1 "stable", < 0.25 "moderate drift", otherwise "significant drift"
    return ""

def ks(a, b):
    # two-sample Kolmogorov-Smirnov: the largest gap between the two empirical CDFs
    return 0.0

ref = [i / 10 for i in range(100)]                       # training feature: roughly uniform 0..9.9
same = [i / 10 + 0.05 for i in range(100)]               # same distribution
shifted = [i / 10 + 3 for i in range(100)]               # mean shifted by +3
for name, live in [("same", same), ("shifted", shifted)]:
    p = psi(ref, live)
    print(f"{name:8s} psi={p:.3f} -> {level(p)}  ks={ks(ref, live):.2f}")
```

Expected output:

```text
same     psi=0.001 -> stable  ks=0.01
shifted  psi=1.864 -> significant drift  ks=0.30
```

??? success "Show a solution"

    ```
    import math

    def psi(expected, actual, bins=5):
        """Population Stability Index between a reference sample and a live sample."""
        lo, hi = min(expected), max(expected)
        edges = [lo + (hi - lo) * i / bins for i in range(bins + 1)]
        def share(xs):
            counts = [0] * bins
            for x in xs:
                i = min(max(int((x - lo) / (hi - lo) * bins), 0), bins - 1)
                counts[i] += 1
            return [max(c / len(xs), 1e-4) for c in counts]
        e, a = share(expected), share(actual)
        return sum((ai - ei) * math.log(ai / ei) for ei, ai in zip(e, a))

    def level(p):
        return "stable" if p < 0.1 else "moderate drift" if p < 0.25 else "significant drift"

    def ks(a, b):
        """Two-sample Kolmogorov-Smirnov statistic (max CDF gap)."""
        pts = sorted(set(a + b))
        return max(abs(sum(x <= p for x in a) / len(a) - sum(x <= p for x in b) / len(b)) for p in pts)

    ref = [i / 10 for i in range(100)]                       # training feature: roughly uniform 0..9.9
    same = [i / 10 + 0.05 for i in range(100)]               # same distribution
    shifted = [i / 10 + 3 for i in range(100)]               # mean shifted by +3
    for name, live in [("same", same), ("shifted", shifted)]:
        p = psi(ref, live)
        print(f"{name:8s} psi={p:.3f} -> {level(p)}  ks={ks(ref, live):.2f}")
    ```

    The same-distribution sample scores near zero on both metrics, while the shifted sample produces a large PSI and a high KS statistic, signalling drift without needing labels. Thresholds are rules of thumb. Verified by running the solution.

### A registry with promotion gates and rollback

Implement Registry.promote: reject versions below the minimum AUC, above the latency limit or regressing against the current production version, otherwise move the production alias; rollback then restores the previous version from the history.

Starter code:

```
class Registry:
    def __init__(self):
        self.versions = {}          # version -> {"metrics": {...}, "run": ...}
        self.alias = {}             # alias -> version
        self.history = []

    def register(self, metrics, run):
        v = len(self.versions) + 1
        self.versions[v] = {"metrics": metrics, "run": run}
        return v

    def promote(self, version, gates):
        # gates: min_auc, max_p95_ms, max_regression. Collect failure messages ("auc X < Y", "p95 Nms > Mms",
        # "auc regression vs vK: X < base") and append "REJECT vN: <failures joined by '; '>" to history,
        # returning False; if all pass set alias["production"] = version, append
        # "PROMOTE vN (previous: <old or None>)" and return True. The regression gate only applies when a
        # production version exists and the new auc is below its auc minus max_regression.
        return False

    def rollback(self):
        for line in reversed(self.history):
            if line.startswith("PROMOTE") and "previous: None" not in line:
                prev = int(line.split("previous: ")[1].rstrip(")"))
                self.alias["production"] = prev
                self.history.append(f"ROLLBACK to v{prev}")
                return prev
        raise RuntimeError("nothing to roll back to")

gates = {"min_auc": 0.80, "max_p95_ms": 100, "max_regression": 0.01}
r = Registry()
v1 = r.register({"auc": 0.84, "p95_ms": 60}, "run-a")
v2 = r.register({"auc": 0.79, "p95_ms": 55}, "run-b")
v3 = r.register({"auc": 0.86, "p95_ms": 140}, "run-c")
v4 = r.register({"auc": 0.87, "p95_ms": 70}, "run-d")
for v in (v1, v2, v3, v4):
    print(v, r.promote(v, gates))
print("production ->", r.alias["production"])
print("rollback ->", r.rollback(), "production ->", r.alias["production"])
print(*r.history, sep="\n")
```

Expected output:

```text
1 True
2 False
3 False
4 True
production -> 4
rollback -> 1 production -> 1
PROMOTE v1 (previous: None)
REJECT v2: auc 0.79 < 0.8; auc regression vs v1: 0.79 < 0.84
REJECT v3: p95 140ms > 100ms
PROMOTE v4 (previous: 1)
ROLLBACK to v1
```

??? success "Show a solution"

    ```
    class Registry:
        def __init__(self):
            self.versions = {}          # version -> {"metrics": {...}, "run": ...}
            self.alias = {}             # alias -> version
            self.history = []

        def register(self, metrics, run):
            v = len(self.versions) + 1
            self.versions[v] = {"metrics": metrics, "run": run}
            return v

        def promote(self, version, gates):
            """Move the 'production' alias to `version` only if every gate passes; otherwise explain why not."""
            m = self.versions[version]["metrics"]
            failures = []
            if m["auc"] < gates["min_auc"]:
                failures.append(f"auc {m['auc']} < {gates['min_auc']}")
            if m["p95_ms"] > gates["max_p95_ms"]:
                failures.append(f"p95 {m['p95_ms']}ms > {gates['max_p95_ms']}ms")
            current = self.alias.get("production")
            if current is not None:
                base = self.versions[current]["metrics"]["auc"]
                if m["auc"] < base - gates["max_regression"]:
                    failures.append(f"auc regression vs v{current}: {m['auc']} < {base}")
            if failures:
                self.history.append(f"REJECT v{version}: " + "; ".join(failures))
                return False
            self.alias["production"] = version
            self.history.append(f"PROMOTE v{version} (previous: {current})")
            return True

        def rollback(self):
            for line in reversed(self.history):
                if line.startswith("PROMOTE") and "previous: None" not in line:
                    prev = int(line.split("previous: ")[1].rstrip(")"))
                    self.alias["production"] = prev
                    self.history.append(f"ROLLBACK to v{prev}")
                    return prev
            raise RuntimeError("nothing to roll back to")

    gates = {"min_auc": 0.80, "max_p95_ms": 100, "max_regression": 0.01}
    r = Registry()
    v1 = r.register({"auc": 0.84, "p95_ms": 60}, "run-a")
    v2 = r.register({"auc": 0.79, "p95_ms": 55}, "run-b")
    v3 = r.register({"auc": 0.86, "p95_ms": 140}, "run-c")
    v4 = r.register({"auc": 0.87, "p95_ms": 70}, "run-d")
    for v in (v1, v2, v3, v4):
        print(v, r.promote(v, gates))
    print("production ->", r.alias["production"])
    print("rollback ->", r.rollback(), "production ->", r.alias["production"])
    print(*r.history, sep="\n")
    ```

    Gating promotion on quality, latency and regression protects production from worse models, and alias-based deployment makes rollback a pointer move. This is a teaching model of a registry. Verified by running the solution.

---

More free practice: the [MLOps Mastery Path](index.md) curriculum, the [Interview Prep Pack](https://learnmasterypath.com/interview-pack/) and the [free resume ATS check](https://learnmasterypath.com/resume/).
