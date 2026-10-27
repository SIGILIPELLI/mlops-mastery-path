---
description: "Every anti-pattern below is a real bug this course hit while being built -- a real skops exception, missing signature, stale threshold, shared exit code."
---

# Common MLOps Anti-Patterns

## What was run

No new code — this lesson is a direct review of real anti-patterns this exact course genuinely ran into while
being built, each one documented honestly in its own lesson at the time it happened, not invented after the
fact to illustrate a point.

## Anti-pattern 1: Trusting a serialization format without checking what it actually requires

[Level 1, Lesson 1](../level-1/01-what-mlops-solves.md) hit a real
`skops.io.exceptions.UntrustedTypesFoundException` the first time `mlflow.sklearn.log_model()` was ever called
in this course, because MLflow 3.x's default `skops` format refuses to deserialize `sklearn.tree._tree.Tree`
without explicit permission. The fix (`skops_trusted_types=[...]`) had to be discovered by reading the real
exception, not assumed in advance.

## Anti-pattern 2: Assuming a model is missing because it's not where you expect

The same lesson later found real runs whose artifacts appeared to be missing entirely — checked against the old
`mlruns/<run_id>/artifacts/model/` path — when MLflow 3.x's real Logged Models architecture had actually stored
them at a completely different, new real location (`mlruns/<exp>/models/m-<id>/artifacts/`) all along.

## Anti-pattern 3: Building on an API that's already printing its own deprecation warning

[Level 2, Lesson 2](../level-2/02-model-stages.md) used the classic `transition_model_version_stage()` call,
which worked, but printed a real `FutureWarning` stating it's deprecated since MLflow 2.9.0. The lesson pivoted
its real teaching content to aliases rather than building further on a mechanism already flagged for removal.

## Anti-pattern 4: Deploying a model with no signature at all

[Level 2, Lesson 8](../level-2/08-input-output-schemas.md) found the real, live, already-promoted `champion`
model had **no signature** (`get_input_schema()` returned `None`) because the script that trained it never
called `infer_signature()` — a real gap that had passed through a real approval gate undetected.

## Anti-pattern 5: Letting one exit code mean two different things

[Level 3, Lesson 10](../level-3/10-project-ci-cd-pipeline.md)'s first real end-to-end run printed
`"PIPELINE STOPPED"` for a run that had actually succeeded — a correctly-declined, non-improving promotion —
because `cd_deploy.py` used the same exit code (`1`) for "genuinely broken" and "correctly not promoted."

## Anti-pattern 6: A stale constant quietly diverging from the real source of truth

[Level 3, Lesson 8](../level-3/08-feature-stores-training-serving-skew.md) hardcoded a serving-side threshold
(`13.0`) that had genuinely gone stale against the real training-time value (`14.1176`) — a real, measurable
feature-value mismatch on 18 of 114 rows, caught only by checking feature values directly, not by watching
accuracy alone.

## Anti-pattern 7: Reading internal exceptions as if they were intentional API responses

[Level 2, Lesson 7](../level-2/07-serving-model-locally.md) found the raw `mlflow models serve` endpoint's real
response to a malformed request was a scikit-learn internal `ValueError` with a full stack trace embedded in the
JSON body — a real, leaky error response, in contrast to the clean, structured `400` [Lesson 8](../level-2/08-input-output-schemas.md)'s
dedicated validation layer produced for the identical malformed input.

## Anti-pattern 8: Mistaking "no data drift" for "the model is still fine"

[Level 4, Lesson 3](03-concept-drift.md) measured a real `p=0.9904` on a KS drift test — strong evidence of no
input drift at all — on the exact same real batch where a simulated concept shift genuinely dropped accuracy by
`0.1579`. Each anti-pattern on this list is a real, specific failure mode; this one is the general shape nearly
all of them share: a signal that looks clean is not the same as a system that's actually fine.

## How It Actually Works

What ties every real item on this list together is the same real discipline this entire course has tried to
model directly: none of these were caught by assuming the tooling, the framework, or a previous lesson's code
was correct. Every one was caught by actually running something and reading its real output critically — an
exception message, a warning, a `None` where a value was expected, a confusing status message, a feature value
that didn't match. The anti-pattern isn't any single mistake on this list; it's trusting a system's surface
signal (ran without crashing, returned 200, printed a seemingly reasonable message) over directly verifying
what it actually did.

## Common mistakes

- Reading this list as "things that could theoretically go wrong" rather than what it actually is: a real,
  dated incident log from one specific course build, each entry traceable to the exact lesson where it
  happened and was fixed in place.
- Assuming a framework this mature (MLflow, scikit-learn) has no sharp edges left — four of the eight real
  anti-patterns above came directly from genuine MLflow behavior (skops, Logged Models, deprecated stages, the
  serving endpoint's raw error format), not from mistakes in this course's own code.
- Fixing an anti-pattern once and assuming it can't recur — [Anti-pattern 6](#anti-pattern-6-a-stale-constant-quietly-diverging-from-the-real-source-of-truth)
  is exactly the kind of bug that reappears anywhere a value exists in two places instead of one real shared
  source.

## Exercise

Pick any one of this lesson's eight real anti-patterns, go back to its linked lesson, and re-run its real code
yourself — confirming independently that the described real behavior (the exception, the warning, the missing
signature, the stale threshold) still reproduces exactly as documented, rather than taking this lesson's own
account of it on faith.
