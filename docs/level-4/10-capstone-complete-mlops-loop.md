---
description: "A real corrupted batch triggered real detection, retraining, and a re-verified final state -- champion stayed at v13, since the candidate wasn't better."
---

# Capstone: A Complete MLOps Loop for a Real Model

## What was run

Every real mechanism this course has built, chained into one real, uninterrupted loop: check the real deployed
state, feed the real champion a real corrupted batch, run real label-free monitoring on it, let a real alert
decide whether to act, actually trigger [Level 3](../level-3/10-project-ci-cd-pipeline.md)'s full real
retraining pipeline when it fires, and independently re-verify the real final state afterward — with no step
faked or summarized from an earlier lesson's output.

## The real, complete loop, start to finish

```python
champion_before = client.get_model_version_by_alias(MODEL_NAME, "champion").version
# ... feed a real corrupted batch to the real deployed model ...
rate_shift = abs(new_preds.mean() - REFERENCE_POSITIVE_RATE)
_, p = ks_2samp(ref_proba, new_proba)
triggered = (rate_shift > POSITIVE_RATE_ALERT_THRESHOLD) or (p < KS_PVALUE_ALERT_THRESHOLD)
if triggered:
    subprocess.run([sys.executable, "../level3/full_cicd_pipeline.py"], cwd="../level3")
champion_after = client.get_model_version_by_alias(MODEL_NAME, "champion").version
```

```
=== STEP 1 ===
real champion before this loop: v13

=== STEP 2 ===
real monitoring signals: positive_rate_shift=0.3596 ks_p_value=0.000000
real alert decision: DEGRADATION DETECTED

=== STEP 3 ===
[... the real full Level 3 pipeline actually ran: prepare, train, evaluate, CI test, register, compare ...]
champion=0.9912 challenger=0.9825
=== CD STEP 4: NOT PROMOTED -- challenger did not beat the current champion ===
real retraining pipeline exit code: 0

=== STEP 4 ===
real champion before loop: v13  |  real champion after loop: v13
real result: the retrain ran, but correctly did not replace the current champion
```

Every real stage fired exactly as it should: a real degradation was detected without any label,
[Level 3](../level-3/10-project-ci-cd-pipeline.md)'s real pipeline genuinely ran end to end in response, and the
real final, independently-reverified state shows `champion` unchanged at `v13` — not because nothing happened,
but because the entire real loop ran correctly and the retrained candidate genuinely wasn't an improvement.

## How It Actually Works

This capstone is not new logic anywhere — every real piece was already built and verified independently across
this level and [Level 3](../level-3/index.md): [monitoring without ground truth](04-monitoring-without-ground-truth.md)'s
proxy signals, [alerting](05-alerting-on-degradation.md)'s thresholds,
[retraining triggers](06-retraining-triggers.md)'s automated kickoff, and
[Level 3](../level-3/04-cd-for-ml.md)'s champion-vs-challenger gate. What this capstone verifies is that they
genuinely *compose* correctly when run as one real, continuous loop with no human step in between — the same
real kind of composition check [Level 3, Lesson 10](../level-3/10-project-ci-cd-pipeline.md)'s capstone needed,
now one level up, spanning detection through response instead of just response alone.

The real, complete MLOps loop this course has been building toward the whole time is exactly this: a model in
production, watched by checks that don't need to wait for labels, wired to a response that doesn't need a human
to notice and act, landing on a real, independently verifiable final state — whether that state is a genuinely
new champion or, as in this specific real run, a correctly unchanged one.

## Common mistakes

- Treating "the loop ran" as the same as "the loop worked" — this capstone's real final step independently
  re-queried the champion alias rather than trusting the retraining pipeline's own printed claims, the same
  discipline [Level 2, Lesson 9](../level-2/09-rolling-back-model-version.md) established for verifying a
  rollback actually took effect.
- Assuming a complete MLOps loop always ends in a new deployment — this real run's correct outcome was *no
  change*, because the automated retrain, evaluated fairly, didn't beat what was already live; a loop that
  always deploys regardless of comparison isn't automation, it's an unconditional override.
- Building each piece of a loop like this in isolation and never running the whole thing together — every
  individual piece here was already tested on its own; only running the full, real, chained loop could have
  confirmed detection genuinely reaches response, and response genuinely reaches a verified outcome.

## Exercise

Re-run this lesson's exact real loop, but first manually weaken the real champion (register and promote a
deliberately underpowered model, as in [Level 3, Lesson 3](../level-3/03-ci-for-ml.md)) before feeding in the
corrupted batch — and confirm the real loop this time produces the other real outcome: a genuine promotion,
with `champion_after` actually different from `champion_before`.
