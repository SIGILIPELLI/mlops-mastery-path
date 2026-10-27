---
description: "The first real pipeline run printed PIPELINE STOPPED for a correct, non-error outcome -- a real exit-code bug this lesson found, fixed, and re-verified."
---

# Project: A Real CI/CD Pipeline for a Model

## What was run

Every real mechanism from this level, chained into one real end-to-end pipeline script: the real orchestrated
training pipeline from [Lesson 2](02-pipeline-orchestration.md), the real CI suite from
[Lesson 3](03-ci-for-ml.md), and the real CD promotion logic from [Lesson 4](04-cd-for-ml.md), run together as
one command. The first real run surfaced a genuine design bug in the pipeline itself, which was fixed and
re-verified before this project counted as done.

## Run 1: a real, working pipeline — with a real, misleading message

```python
STAGES = ["orchestrate.py", "cd_deploy.py"]
for stage in STAGES:
    result = subprocess.run([sys.executable, stage])
    if result.returncode != 0:
        print(f"=== PIPELINE STOPPED at {stage} (exit {result.returncode}) ===")
        sys.exit(result.returncode)
```
```bash
python3 full_cicd_pipeline.py
```
```
=== STEP OK: step_evaluate.py (exit 0) ===
=== PIPELINE COMPLETE: all steps passed ===
5 passed in 3.35s
champion=0.9912 challenger=0.9825
=== CD STEP 4: NOT PROMOTED -- challenger did not beat the current champion ===
=== PIPELINE STOPPED at cd_deploy.py (exit 1) ===
```

Every real stage actually worked correctly — training succeeded, CI passed, the real comparison correctly
declined to promote a weaker candidate. But the pipeline's own final message, **"PIPELINE STOPPED"**, reads like
a failure. That's a genuine bug in the pipeline's own exit-code design, found by actually running it, not a
hypothetical concern — [Lesson 4](04-cd-for-ml.md)'s `cd_deploy.py` used the same exit code (`1`) for two
different real situations: "the CI suite genuinely failed" and "the candidate was fine but not an improvement."

## The real fix: a distinct exit code for a distinct, correct outcome

```python
# cd_deploy.py
else:
    print("=== CD STEP 4: NOT PROMOTED -- challenger did not beat the current champion ===")
    sys.exit(2)  # distinct from exit 1 (a genuine CI/pipeline failure)

# full_cicd_pipeline.py
if result.returncode == 2:
    print(f"=== PIPELINE COMPLETE (not promoted): {stage} correctly declined to promote ===")
    sys.exit(0)
```
```bash
python3 full_cicd_pipeline.py
```
```
=== CD STEP 4: NOT PROMOTED -- challenger did not beat the current champion ===
=== PIPELINE COMPLETE (not promoted): cd_deploy.py correctly declined to promote a non-improving candidate ===
```
```bash
echo "real exit code=$?"   # 0
```

The exact same real candidate, the exact same real correct decision — now reported accurately as a successful
pipeline run, not a failure.

## How It Actually Works

This project's real value came specifically from running the finished pipeline and reading its own output
critically, rather than trusting that chaining already-tested pieces together automatically produces a correctly
*reported* result. Each individual stage — [Lesson 2](02-pipeline-orchestration.md)'s steps,
[Lesson 3](03-ci-for-ml.md)'s tests, [Lesson 4](04-cd-for-ml.md)'s gate — was already verified correct in
isolation. What this capstone caught is a bug in how those correct pieces *compose*: a single shared exit code
(`1`) meaning two semantically different things to the orchestrator wrapping them.

The real fix — a third, distinct exit code — is a small change with a real consequence: a team wiring this
pipeline into an actual CI/CD system (triggering an alert on failure, say) needs to be able to tell "something
is broken, investigate" apart from "everything worked, nothing happened to be better," and conflating them into
one signal would either hide real failures in a sea of routine non-promotions, or page someone for a correctly
functioning pipeline.

## Common mistakes

- Treating a finished, individually-tested pipeline as done without running the composed whole and reading its
  actual output — every real stage in this lesson's pipeline passed its own tests; the bug only existed in how
  their exit codes were combined, which no single stage's own tests could have caught.
- Reusing a generic "nonzero means something's wrong" convention for outcomes that aren't actually errors — this
  lesson's real `exit(2)` fix exists because "not promoted" and "broken" are different real situations that
  deserve different real signals to whatever system consumes this pipeline's result.
- Calling a multi-stage automation "CI/CD" without ever running it start to finish as a single real invocation —
  this lesson's bug was invisible when each stage was run and read individually earlier in this level; it only
  showed up once the stages were actually chained and run together, exactly as a real deployment pipeline would
  be.

## Exercise

Extend `full_cicd_pipeline.py` to run on every new push to this course's real `mlops-mastery-path` repository
(for example as a GitHub Actions workflow), using this lesson's real, now-correct three-way exit code
(`0`=promoted or not-promoted-but-fine, nonzero=genuinely broken) to decide whether the workflow should report
success or failure — completing the real automation this entire level has been building toward.
