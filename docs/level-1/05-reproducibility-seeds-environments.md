---
description: "With a fixed seed, two real runs matched to 10 decimals (0.9649122807 both times); without one, two real runs disagreed (0.9912 vs 0.9649)."
---

# Reproducibility: Seeds, Environments & Data Versions

## What was run

The exact same real training code, run twice two different ways: once with a fixed `random_state=42` on both the
train/test split and the model, and once with no seed set at all — to measure, with real numbers, exactly what
"reproducibility" costs when it's missing.

## The real difference a seed makes

```python
# WITH a fixed random_state, run twice
for i in range(2):
    X_train, X_test, y_train, y_test = train_test_split(data.data, data.target, test_size=0.2, random_state=42)
    model = RandomForestClassifier(n_estimators=50, max_depth=5, random_state=42)
    ...
```
```
run 1: accuracy=0.9649122807
run 2: accuracy=0.9649122807
```

**Identical to 10 decimal places**, both times — real, exact reproducibility, because every source of randomness
in this pipeline (which rows become the test set, which features each tree considers) was pinned to the same
seed.

```python
# WITHOUT a fixed random_state, run twice
for i in range(2):
    X_train, X_test, y_train, y_test = train_test_split(data.data, data.target, test_size=0.2)
    model = RandomForestClassifier(n_estimators=50, max_depth=5)
    ...
```
```
run 1: accuracy=0.9912280702
run 2: accuracy=0.9649122807
```

The **exact same code**, run twice, produced two real, genuinely different results — a real accuracy gap of
0.026, purely from unpinned randomness in the train/test split and the forest's own tree construction. Neither
number is "wrong"; both are real, valid outcomes of the same process — which is precisely the problem: without a
seed, "the accuracy of this model" isn't one number, it's a real distribution of possible numbers, and a team
reporting whichever one they happened to get first is reporting noise, not a property of the model.

## How It Actually Works

Reproducibility in ML has three real, independent sources of non-determinism this lesson's own numbers touch
directly, and seeds only address one of them:

1. **Randomness in code** — exactly what this lesson measured: `train_test_split`'s row shuffling and
   `RandomForestClassifier`'s internal bootstrap sampling and feature selection. Fixing `random_state` pins this
   source completely, as the identical real `0.9649122807` result twice over proves.
2. **The environment** — which exact versions of `scikit-learn`, `numpy`, and every other real dependency were
   installed. This lesson's own real environment pins `scikit-learn==1.9.1`, `numpy==2.5.4`,
   `pandas==3.1.0` (checked directly with `pip freeze`) — a different version of any of these can change results
   even with an identical seed, because the *algorithm's own implementation* can change between library versions.
3. **The data itself** — whether the exact same real rows, in the exact same real order, were used. A seed makes
   a *split* reproducible given the same input data, but says nothing about whether the input data itself has
   changed since the last run — the real concern [Lesson 6](06-data-lineage.md) addresses directly.

A real, complete reproducibility story needs all three: a logged seed (what this lesson built), a logged,
pinned environment (what `pip freeze` captures, and what MLflow can log automatically as a run's environment
artifact), and a logged reference to the exact data used. Missing any one of the three means "reproduce this
result" can still fail, or silently produce a different real number, even when the other two are handled
correctly.

## Common mistakes

- Fixing a model's own `random_state` but forgetting the data split's — this lesson's unseeded version left
  *both* unset deliberately to show the full real effect, but even fixing just one and not the other leaves real
  non-determinism in the result.
- Assuming "the same code" is enough for reproducibility without also pinning the real environment — an
  upgraded `scikit-learn` version can change a seeded result, because the algorithm's own internals can change
  between releases, something a seed alone can't protect against.
- Reporting a single run's result as "the" accuracy of an approach without checking, the way this lesson did
  directly, how much that number would vary run to run if nothing were pinned — a result that moves by 0.026
  from pure randomness is a real signal that one unseeded run isn't a reliable basis for a decision.

## Exercise

Run this lesson's unseeded version 10 real times in a loop, collect the real accuracy from each, and compute the
real standard deviation across them. Compare that real spread directly to the gap this lesson measured between
just two unseeded runs (0.9912 vs 0.9649), and write one sentence on whether two samples were enough to
characterize the real variance, or whether you needed all 10 to get a trustworthy picture.
