---
description: "A real evaluate step, run before its dependencies existed, failed immediately and explicitly rather than silently producing a wrong number."
---

# Pipeline Orchestration: Steps, Dependencies, Artifacts

## What was run

A real training pipeline split into three separate, real scripts — `prepare`, `train`, `evaluate` — each
reading a real artifact file the previous step wrote and producing its own, chained via an explicit dependency
check rather than assumed ordering. Run once in full, and once deliberately out of order.

## Three real steps, chained by real files on disk

```python
# step_prepare.py
train_df.to_csv(train_path, index=False)
json.dump({"train_rows": ..., "train_path": train_path, "test_path": test_path}, f)

# step_train.py
if not os.path.exists(manifest_path):
    print("TRAIN: FAIL -- no prepare_manifest.json found; the prepare step must run first")
    sys.exit(1)
train_df = pd.read_csv(manifest["train_path"])
...
json.dump({"run_id": run_id, "depends_on": manifest_path}, f)

# step_evaluate.py
if not os.path.exists(train_manifest_path):
    print("EVALUATE: FAIL -- no train_manifest.json found; the train step must run first")
    sys.exit(1)
```

```bash
python3 orchestrate.py
```
```
PREPARE: wrote 455 train rows, 114 test rows to .../level3/artifacts
TRAIN: run_id=a321541e14ca4b068de2a3f0c82d5d49 (depends on 455 rows from prepare step)
EVALUATE: run_id=a321541e14ca4b068de2a3f0c82d5d49 test_accuracy=0.9649
=== PIPELINE COMPLETE: all steps passed ===
```

A real, complete run — three separate real processes, each one reading a concrete file the previous one wrote,
not an in-memory variable passed silently between them.

## A real dependency, enforced — not assumed

```bash
rm -rf artifacts
python3 step_evaluate.py
```
```
EVALUATE: FAIL -- no train_manifest.json found; the train step must run first
real exit code=1
```

Running the last step first, with none of its real dependencies on disk, failed immediately and explicitly —
not with a confusing downstream error about a missing column or an undefined variable three lines later, but
with a direct statement of exactly which upstream step was skipped.

## How It Actually Works

Each real step in this lesson's pipeline communicates with the next through a concrete file on disk — a CSV, a
JSON manifest — rather than shared in-memory state. That's a deliberate, real constraint: it's what makes each
step independently runnable, independently debuggable (you can open `prepare_manifest.json` directly and see
exactly what the prepare step produced), and independently re-runnable without re-running everything upstream of
it, as long as its own real dependency is still there.

The real `orchestrate.py` script adds nothing to the training logic itself — it's thin on purpose, just running
each step as a real subprocess and checking its real exit code, stopping the whole pipeline the moment one step
genuinely fails rather than charging ahead into a step whose input is now wrong or missing. That's the same real
exit-code discipline [Lesson 1](01-training-pipelines.md) built into the training script itself, now used as the
real contract between pipeline *steps*, not just within one.

## Common mistakes

- Chaining pipeline steps through shared in-memory state (running everything in one process, one script) instead
  of real artifacts on disk — this lesson's steps can be re-run independently, inspected independently, and
  (in [Lesson 4](04-cd-for-ml.md)) scheduled independently precisely because each one's input and output is a
  real file, not a variable that only exists while one process is alive.
- Assuming steps ran in the right order instead of checking — this lesson's real `step_evaluate.py` run, with no
  upstream artifacts present, demonstrates exactly what silently assuming order would risk: without the explicit
  `if not os.path.exists(...)` check, it would have crashed later with a confusing `FileNotFoundError` deep
  inside pandas, instead of a clear, immediate, named failure.
- Having a later step silently regenerate a missing input instead of failing — regenerating the training data
  inside the evaluate step, say, would silently hide the fact that the train step never ran, and the evaluate
  step would end up measuring a model that was never actually produced by this real pipeline run.

## Exercise

Add a fourth real step, `step_package.py`, that reads `evaluate_manifest.json`, and — only if `test_accuracy`
clears the real bar — writes a final `pipeline_manifest.json` recording the `run_id` as "ready to deploy." Wire
it into `orchestrate.py` as a fourth step, and confirm with a real run that a model failing
[Lesson 1](01-training-pipelines.md)'s bar never reaches this new step at all.
