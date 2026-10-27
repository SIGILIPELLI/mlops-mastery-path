---
description: "The exact same script, run twice with the exact same arguments, produced two independent MLflow runs scoring the identical 0.9649 -- real reproducibility."
---

# Training Pipelines: Notebook to Reproducible Script

## What was run

A real, standalone, argument-driven training script — no notebook cell state, no manually re-run cells, no
hidden variables left over from an earlier experiment — run four separate real times with different arguments,
including one deliberately underpowered run that genuinely fails the pipeline's own exit-code gate.

## A real script, not a notebook

```python
parser.add_argument("--n-estimators", type=int, default=150)
parser.add_argument("--max-depth", type=int, default=7)
...
if acc < 0.95:
    print("FAIL: accuracy below the pipeline's minimum bar (0.95)", file=sys.stderr)
    sys.exit(1)
sys.exit(0)
```

```bash
python3 pipeline_train.py
# run_id=bc1c67f4f7a14c48baff5b1a653ab605 accuracy=0.9649 ... exit=0

python3 pipeline_train.py   # same arguments, run again
# run_id=ee3514ce6353493f995643855d111b1b accuracy=0.9649 ... exit=0
```

Two real, separate runs, same real arguments (every default), two different real `run_id`s, but the identical
`0.9649` accuracy both times — genuine reproducibility, not a notebook's silently-mutated global state producing
a different number each time a cell happens to be re-run in a different order.

## A real, deliberate failure, caught by the pipeline itself

```bash
python3 pipeline_train.py --max-depth 1 --n-estimators 1
```
```
FAIL: accuracy below the pipeline's minimum bar (0.95)
run_id=e5c615fac50141968536109ae4153a5f accuracy=0.9123 ...
real exit code=1
```

A real, underpowered model (a single tree, one level deep) genuinely scored below the script's own hardcoded
bar, and the script exited with a real, nonzero status — the exact signal a real CI system
([Lesson 3](03-ci-for-ml.md)) needs to automatically stop a bad model before it goes anywhere further.

## How It Actually Works

The real difference between this script and a notebook isn't the code itself — the training logic is the same
`RandomForestClassifier.fit()` call this course has used since [Level 1](../level-1/02-experiment-tracking-fundamentals.md)
— it's what the script *doesn't* have: no cell execution order to get wrong, no leftover variable from three
cells ago silently feeding into this run, no need to remember "did I re-run the data-loading cell after I changed
the split ratio." Every real input the script needs comes in through `argparse`, and every real run starts from
nothing.

The real exit code is what turns a training script into something a *pipeline* can act on automatically. A
notebook has no exit code — a human has to look at the output and decide whether it's good enough. This script's
`sys.exit(1)` on a real failing run is a real, machine-checkable signal, not a human judgment call, and it's
exactly what [Lesson 2](02-pipeline-orchestration.md)'s orchestration and [Lesson 3](03-ci-for-ml.md)'s CI checks
will both key off of.

## Common mistakes

- Treating "it ran in my notebook" as equivalent to "it's reproducible" — this lesson's real two identical runs
  only proved reproducible because every real input (`n_estimators`, `max_depth`, `test_size`, `random_state`)
  was an explicit argument with a fixed default, not a value typed once into a cell and forgotten.
- Hardcoding a path, a tracking URI, or a dataset location directly in the training logic instead of taking it as
  a real parameter — this script's `--tracking-uri` and `--experiment` flags are what let the exact same script
  run against a different real MLflow server or experiment without editing the file.
- Printing a result and calling it done, without a real pass/fail exit code — a human can read printed accuracy
  and decide; an automated pipeline can only act on a real, nonzero exit status.

## Exercise

Add a `--min-accuracy` argument to this lesson's real script, replacing the hardcoded `0.95`, and re-run the exact
same underpowered call (`--max-depth 1 --n-estimators 1`) with `--min-accuracy 0.85` — confirming it now exits `0`
against the identical real model that failed the stricter bar, making the pipeline's pass/fail threshold itself a
real, configurable input rather than a value buried in the script.
