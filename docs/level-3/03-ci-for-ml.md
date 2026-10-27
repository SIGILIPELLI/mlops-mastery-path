---
description: "The exact same 5-test suite passed fully against a good model and genuinely failed one test, with the real accuracy in the assertion, on a bad one."
---

# CI for ML: Testing a Model Before It Ships

## What was run

A real `pytest` suite — five tests, not toy assertions — run twice against two real, different models loaded
from the real MLflow registry built up in [Level 2](../level-2/01-model-registry-fundamentals.md): once against
a genuinely good candidate, and once against a deliberately underpowered one, to see which real tests catch the
difference and how.

## A real test suite, not just a notebook check

```python
def test_model_rejects_missing_columns(model, test_data):
    broken = X_test.drop(columns=["mean radius"])
    with pytest.raises(Exception):
        model.predict(broken)

def test_model_meets_minimum_accuracy(model, test_data):
    acc = accuracy_score(y_test, model.predict(X_test))
    assert acc >= 0.95, f"real accuracy {acc:.4f} is below the required 0.95 bar"

def test_model_predictions_are_binary(model, test_data):
    preds = model.predict(X_test)
    assert set(preds).issubset({0, 1})
```

```bash
python3 -m pytest test_model.py -v
```
```
test_model.py::test_model_loads PASSED
test_model.py::test_model_accepts_full_schema PASSED
test_model.py::test_model_rejects_missing_columns PASSED
test_model.py::test_model_meets_minimum_accuracy PASSED
test_model.py::test_model_predictions_are_binary PASSED
5 passed in 3.35s
```

## The same real suite, against a deliberately bad candidate

```bash
python3 -m pytest test_model.py -v
```
```
>       assert acc >= 0.95, f"real accuracy {acc:.4f} is below the required 0.95 bar"
E       AssertionError: real accuracy 0.9123 is below the required 0.95 bar
1 failed, 4 passed in 3.39s
```
```bash
echo "real pytest exit code=$?"
# real pytest exit code=1
```

A real, specific, named failure — `test_model_meets_minimum_accuracy`, with the exact real accuracy
(`0.9123`) embedded in the assertion message — while the other four tests genuinely still passed, because a
model this underpowered still loads fine, still accepts the right shape, still rejects a malformed one, and
still only ever outputs `0` or `1`. Only the one real metric that actually distinguishes a good model from a
mediocre one failed.

## How It Actually Works

This is the same discipline as any CI test suite for ordinary application code, pointed at a model instead of a
function: a real, automated, repeatable check that runs against a specific artifact (here, a specific MLflow
`run_id`) and produces a real pass/fail verdict with a real nonzero exit code on failure — the same kind of
signal [Lesson 1](01-training-pipelines.md)'s training script and [Lesson 2](02-pipeline-orchestration.md)'s
orchestrator already produce, now applied as an independent, separate check *after* training rather than inside
it.

The real reason this is a separate step from training, not folded into `step_evaluate.py`, is that CI for ML
needs to check things training-time evaluation doesn't: this lesson's `test_model_rejects_missing_columns` and
`test_model_predictions_are_binary` aren't about accuracy at all — they're about the model behaving sanely
*as a deployable artifact*, the same kind of contract check [Level 2](../level-2/08-input-output-schemas.md)'s
schema validation covered for a live server, now run automatically against every real candidate before it's
allowed anywhere near one.

## Common mistakes

- Writing only an accuracy threshold test and nothing else — this lesson's real bad-candidate run passed four
  of five tests; a suite that only checked accuracy would have given exactly the same one-line verdict, but a
  suite checking schema behavior and output shape too catches classes of real bugs (a model that silently
  accepts wrong input, say) that a pure accuracy check never would.
- Running model tests manually instead of wiring them into something that runs on every real candidate
  automatically — this lesson's manual `pytest` invocation is the right real check; making it automatic on every
  new run is exactly what [Lesson 4](04-cd-for-ml.md) builds next.
- Testing against a fixed, hand-picked example instead of the real held-out test set — this lesson's tests load
  the real `test.csv` written by [Lesson 2](02-pipeline-orchestration.md)'s `step_prepare.py`, the same real data
  every other step in this pipeline evaluates against, rather than a single cherry-picked row that might happen
  to work.

## Exercise

Add a sixth real test, `test_model_latency`, that calls `model.predict()` on the real test set, times it with
`time.perf_counter()`, and asserts the real per-row latency stays under a fixed millisecond budget. Run it
against both this lesson's real good and bad candidates and confirm whether raw inference speed turns out to
correlate with model quality for this real dataset, or not.
