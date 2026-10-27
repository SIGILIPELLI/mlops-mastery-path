---
description: "The normal batch correctly skipped retraining; the corrupted batch triggered a real retraining pipeline run, which executed end to end and exited 0."
---

# Retraining Triggers: When and How to Retrain

## What was run

[Lesson 5](05-alerting-on-degradation.md)'s real alert checks, wired directly to a real decision: if either
check fires, actually invoke [Level 3](../level-3/10-project-ci-cd-pipeline.md)'s real, complete CI/CD pipeline
as the retraining response; if not, skip it entirely. Run against the same real normal and corrupted batches
used throughout this level.

## A real, automatic decision — not just a printed recommendation

```python
triggered = (rate_shift > POSITIVE_RATE_ALERT_THRESHOLD) or (p < KS_PVALUE_ALERT_THRESHOLD)
if triggered:
    subprocess.run([sys.executable, "full_cicd_pipeline.py"], cwd="../level3")
```

```
=== CHECK 1: normal batch ===
[normal] rate_shift=0.0000 ks_p=1.000000 -> no retrain needed
SKIPPED real retraining -- no degradation signal present

=== CHECK 2: corrupted batch ===
[corrupted] rate_shift=0.3596 ks_p=0.000000 -> RETRAIN TRIGGERED
Running real retraining pipeline...
real retraining pipeline exit code: 0
```

The real normal batch correctly triggered nothing. The real corrupted batch genuinely kicked off
[Level 3](../level-3/10-project-ci-cd-pipeline.md)'s full pipeline as a subprocess — prepare, train, evaluate,
run the real CI suite, register a new candidate, compare it against the current champion — and that real
pipeline run completed with exit code `0`.

## How It Actually Works

This lesson adds no new detection logic at all — it reuses [Lesson 5](05-alerting-on-degradation.md)'s exact
real checks unchanged — and instead closes the real loop those checks were missing: a detected problem that
only ever gets printed or logged still needs a human to notice it and manually kick off a fix. Wiring the
trigger directly to [Level 3](../level-3/01-training-pipelines.md)'s already-built, already-tested pipeline is
what turns "we noticed something's wrong" into "the system already started responding."

The real retraining this lesson triggers isn't retraining *on* the corrupted data that caused the alert — it's
re-running the standard pipeline against the real, known-good training data, producing a fresh candidate that
then goes through the exact same real comparison [Level 3](../level-3/04-cd-for-ml.md) always applies. That's a
deliberate, real distinction: an automated retraining trigger should produce a model to re-evaluate, not blindly
trust whatever caused the alert as new training signal — training directly on a corrupted or drifted batch
would risk teaching the model the very problem the alert just caught.

## Common mistakes

- Automatically promoting a freshly retrained model without the real comparison step — this lesson's triggered
  run still went through [Level 3](../level-3/04-cd-for-ml.md)'s real champion-vs-challenger gate (and, in this
  specific real run, correctly did not promote), rather than assuming "retrained" automatically means "better."
- Triggering retraining directly on the batch that caused the alert — this lesson's real retraining pipeline
  trains on the known-good dataset, not the corrupted batch that tripped the check, specifically to avoid
  teaching the model the problem that was just detected.
- Retraining on every single alert with no real rate limiting — a real production system firing this lesson's
  trigger on every noisy blip would burn real compute and real time constantly; a mature real version would
  debounce triggers (for example, require the same alert to persist across several consecutive real checks)
  before actually kicking off a full pipeline run.

## Exercise

Add a real cooldown to this lesson's trigger: track the real timestamp of the last retraining run in a file, and
skip triggering again if less than some real minimum interval (say, one hour) has passed since the last one,
even if a new real alert fires — logging a clear message explaining why the trigger was suppressed.
