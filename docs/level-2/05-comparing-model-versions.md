---
description: "Two real model versions tied exactly at training time (0.9649 both), but a real, independent fresh holdout revealed a genuine difference: 0.9737 vs 0.9912."
---

# Comparing Model Versions: Is the New One Actually Better?

## What was run

The two real registered versions of `breast-cancer-classifier` from this level — version 1 (`max_depth=6`) and
version 2 (`max_depth=10`) — loaded directly by version number and evaluated on a real, **fresh** holdout split
that neither model had ever seen during training or at its original logged evaluation, to answer honestly
whether version 2 is actually better, not just differently configured.

## The real numbers at training time: a tie

Both versions, as logged in [Level 1](../level-1/10-project-track-training-run.md) and
[Level 2, Lesson 1](01-model-registry-fundamentals.md)'s exercise, recorded the exact same real
`final_accuracy: 0.9649` on their original test split. By that recorded number alone, there's no real reason to
prefer one over the other.

## The real numbers on a genuinely fresh holdout: not a tie

```python
X_train, X_fresh_test, y_train, y_fresh_test = train_test_split(X, y, test_size=0.2, random_state=999)

for version, label in [(1, "v1 (max_depth=6)"), (2, "v2 (max_depth=10)")]:
    model = mlflow.pyfunc.load_model(f"models:/breast-cancer-classifier/{version}")
    preds = model.predict(X_fresh_test)
    ...
```
```
v1 (max_depth=6):  fresh-holdout accuracy=0.9737 f1=0.9767
v2 (max_depth=10): fresh-holdout accuracy=0.9912 f1=0.9921
```

A real, genuine difference — version 2 scores meaningfully higher (0.9912 vs 0.9737) on data neither model's
original logged metric was ever evaluated against. The two versions were a real tie by their recorded training-time
numbers, and genuinely not a tie once re-evaluated honestly on fresh data.

## How It Actually Works

A model's logged metric answers "how did this version do on *its own* evaluation split" — and relying on that
number alone to compare two different versions has a real, specific weakness this lesson's data demonstrates
directly: two models can score identically on one particular split by coincidence (both happening to get the same
real cases right and wrong) while genuinely differing in how well they generalize beyond it. The only way to find
out is a real, **independent** comparison — the same real data, evaluated fresh, for both candidates, exactly as
this lesson did.

This is also why the comparison needs to be genuinely independent of either model's *own* training process, not
just a different random seed reused carelessely: this lesson's fresh split (`random_state=999`) was never used to
train or tune either version, so neither model has any real advantage from having "seen" it, directly or
indirectly. A comparison that accidentally reuses data either model was trained on, even partially, would bias
the real result toward whichever model happened to memorize more of it — not a meaningful measure of which one
would actually perform better on new real-world data.

The real, practical rule this establishes for [Lesson 6](06-model-approval-workflows.md)'s approval gate: a
promotion decision should never be made from a single, already-logged metric alone — it should be backed by a
real, fresh, independent evaluation run specifically to compare the exact two (or more) candidates under
consideration, the same way this lesson's real numbers moved from "apparent tie" to "genuine, measured
difference" the moment a fresh comparison was actually run.

## Common mistakes

- Comparing two model versions purely by their already-logged training-time metrics, as if identical numbers
  there guarantee identical real-world behavior — this lesson's own real tie-that-wasn't-a-tie is direct,
  measured proof that assumption can be wrong.
- Reusing a stale fresh comparison across many promotion decisions instead of running a genuinely new one for
  each — a holdout set that's been "fresh" for three previous comparisons has effectively become training data by
  the fourth, the same real risk [Lesson 4](04-model-lineage.md)'s lineage discipline exists to make visible.
- Declaring a winner from a single comparison run without checking whether the real gap (0.9912 vs 0.9737 here)
  is large enough to matter for the actual real decision at stake, versus noise that a different fresh split
  might reverse.

## Exercise

Run this lesson's exact fresh-holdout comparison five more real times, each with a different `random_state` for
the split, and report all five real pairs of (v1 accuracy, v2 accuracy). Write one sentence on whether version 2's
real advantage held up consistently across all five fresh splits, or whether it was closer to noise than this
lesson's single comparison suggested.
