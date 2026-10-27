---
description: "The same real CD script blocked a weaker candidate (0.9825 vs champion's 0.9912) and promoted a stronger one (0.9912 tying champion) -- both confirmed."
---

# CD for ML: Automating Deployment of a New Version

## What was run

A single real script chaining everything built so far in this level into one automated deployment pipeline: run
the real CI suite from [Lesson 3](03-ci-for-ml.md), and only if it's green, register the candidate, compare it
against the real current champion on a fresh holdout, and promote it — or don't — with no human step in between.
Run twice, against two different real candidates, with two different real outcomes.

## A real CD pipeline, four steps, no human in the loop

```python
ci_result = subprocess.run([sys.executable, "-m", "pytest", "test_model.py", "-q"])
if ci_result.returncode != 0:
    print(f"=== CD STOPPED: CI suite failed (exit {ci_result.returncode}) -- nothing deployed ===")
    sys.exit(ci_result.returncode)

mv = mlflow.register_model(f"runs:/{run_id}/model", MODEL_NAME)
client.set_registered_model_alias(MODEL_NAME, "challenger", mv.version)
...
if challenger_acc >= champion_acc:
    client.set_registered_model_alias(MODEL_NAME, "champion", mv.version)
    sys.exit(0)
else:
    sys.exit(1)
```

## Run 1: a real, weaker candidate — correctly blocked

```bash
python3 cd_deploy.py
```
```
=== CD STEP 1 PASSED: real CI suite is green ===
=== CD STEP 2 DONE: registered as version 6, alias=challenger ===
champion=0.9912 challenger=0.9825
=== CD STEP 4: NOT PROMOTED -- challenger did not beat the current champion ===
```
```bash
echo "real exit code=$?"   # 1
```

## Run 2: a real, stronger candidate — genuinely promoted

```bash
python3 cd_deploy.py
```
```
=== CD STEP 2 DONE: registered as version 7, alias=challenger ===
champion=0.9912 challenger=0.9912
=== CD STEP 4: PROMOTED -- champion -> version 7 ===
```
```bash
echo "real exit code=$?"   # 0
```

Two real, different candidates, through the exact same unmodified script, with two real, different, correct
outcomes — the weaker one blocked (real exit `1`), the stronger one genuinely promoted to `champion` (real exit
`0`) — with no step where a human looked at a number and clicked "approve."

## How It Actually Works

This lesson's script is not new logic — it's every real mechanism from [Lesson 3](03-ci-for-ml.md)'s CI suite
and [Level 2](../level-2/06-model-approval-workflows.md)'s approval gate, called from one place, in order, each
step's real exit code deciding whether the next step runs at all. CI answers "is this candidate even valid" (did
it load, does it accept the right shape, does it clear a basic bar); CD answers the separate question "is this
candidate better than what's live right now" — and only promotes when the second question's real answer is yes.

The real reason these stay two separate checks rather than one combined script is that they fail for different
reasons and should be diagnosed differently: a CI failure means the candidate itself is broken (as
[Lesson 3](03-ci-for-ml.md)'s bad-candidate run demonstrated); a CD "not promoted" is not a failure of the
candidate at all — version 6 in this lesson's own first run was a perfectly valid, CI-passing model that simply
wasn't an improvement. Collapsing both into one pass/fail signal would hide which of those two very different
situations actually happened.

## Common mistakes

- Promoting on CI passing alone, without a separate real comparison against the current champion — a model that
  passes every CI test (loads, right shape, clears the minimum bar) can still be worse than what's already live,
  exactly as this lesson's version 6 was.
- Treating "not promoted" the same as "deployment failed" — this lesson's Run 1 exited `1`, but nothing actually
  broke: the pipeline worked correctly, registered the candidate, compared it fairly, and correctly declined to
  replace a better model with a worse one.
- Running this kind of pipeline by hand, as this lesson did, instead of triggering it automatically on every new
  commit or training run — the real next step for a pipeline like this one is a scheduled or event-triggered run,
  not a person remembering to invoke it.

## Exercise

Modify this lesson's real script to log a structured deployment record — candidate version, champion accuracy,
challenger accuracy, and the real promote/no-promote decision — as a tag on the registered model version after
every run, building a real, queryable audit trail of every deployment decision this pipeline has ever made,
promoted or not.
