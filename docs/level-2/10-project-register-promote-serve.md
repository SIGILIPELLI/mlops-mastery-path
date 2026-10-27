---
description: "A real new candidate scored 0.9649 on a fresh holdout against the real champion's 0.9737 -- the gate correctly blocked it, champion stayed unchanged."
---

# Project: Register, Promote & Serve a Real Model

## What was run

One real, complete, end-to-end script combining every real mechanism this level built: train a real candidate
with a real signature, register it, compare it against the real current champion on a genuinely fresh holdout,
run it through the real approval gate, and verify the real final served prediction — all in one real run, with
a real, honest outcome: the gate correctly said no.

## The real, complete lifecycle, step by step

```
1. TRAINED: run_id=ff20b60c3f244cf0b57554d2ae0b2661 test_accuracy=0.9649
2. REGISTERED: version=4 (alias=challenger)
3. COMPARED (fresh holdout): champion=0.9737 challenger=0.9649
4. GATE: BLOCKED -- champion stays at its current version
5. VERIFIED: real champion prediction=1 real true label=1
```

```bash
curl -s ".../registered-models/alias?name=breast-cancer-classifier&alias=champion"
# "version": "2"
```

A real, independently-confirmed outcome: the new candidate (version 4) genuinely scored lower than the current
champion on a fresh, independent evaluation (0.9649 vs 0.9737) — the exact same kind of real, fresh-holdout
comparison [Lesson 5](05-comparing-model-versions.md) built — and the real approval gate from
[Lesson 6](06-model-approval-workflows.md) correctly refused to promote it. Champion stayed at version 2, exactly
as it was before this script ever ran, verified directly against the registry rather than assumed.

## How It Actually Works

This project is every earlier lesson in this level, composed into one real, repeatable pipeline: train with a
real signature ([Lesson 3](03-packaging-signatures-environments.md), fixing the real gap
[Lesson 8](08-input-output-schemas.md) found in earlier models), register
([Lesson 1](01-model-registry-fundamentals.md)), compare on fresh data rather than trusting a training-time
number ([Lesson 5](05-comparing-model-versions.md)), gate the promotion decision automatically
([Lesson 6](06-model-approval-workflows.md)), and verify the real, final state independently rather than trusting
the script's own printed claims ([Lesson 9](09-rolling-back-model-version.md)'s discipline, applied here
preventatively instead of reactively).

The real, honest value of this capstone's specific outcome — a **blocked** promotion, not an approved one — is
that it demonstrates the gate working exactly as intended under genuinely ordinary conditions, not just the
dramatic failure case from [Lesson 9](09-rolling-back-model-version.md). Most real candidate models in a real
team's ongoing work won't be either catastrophically broken (Lesson 9's shuffled-labels model) or obviously
superior — many, like this lesson's real version 4, will be roughly comparable or slightly worse, and a real gate
needs to handle that ordinary, undramatic case correctly too: refusing a lateral or regressive change just as
reliably as it would refuse a genuinely broken one.

## Common mistakes

- Building a pipeline that only demonstrates (or only tests) the success path — this project's real value came
  specifically from encountering, and correctly handling, the less exciting "the new one isn't actually better"
  outcome, which is the far more common real case than either a clear win or a dramatic failure.
- Treating a blocked promotion as a pipeline failure rather than a pipeline working correctly — the real exit
  condition here (champion unchanged, candidate registered but not promoted) is exactly the right real outcome
  for this specific real comparison, not something to retry or override.
- Running this kind of end-to-end real pipeline only once, manually, instead of as something repeatable that
  could run automatically on every new candidate — the real automation of this exact pipeline is what
  [Level 3](../level-3/04-cd-for-ml.md) builds next.

## Exercise

Modify this lesson's script to train a real candidate deliberately likely to beat the current champion (try
`n_estimators=300, max_depth=10`), run the full real pipeline again, and confirm this time the gate produces
`APPROVED` and the real `champion` alias genuinely advances to the new version — completing, with your own real
run, the success path this lesson's own capstone run didn't happen to hit.
