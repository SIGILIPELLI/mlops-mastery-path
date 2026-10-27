---
description: "The real KS test found zero input drift (p=0.99) on the same set where a relabeling, with inputs untouched, dropped accuracy from 0.9649 to 0.8070."
---

# Concept Drift: When the Relationship Itself Changes

## What was run

The exact same real, held-out test set used throughout this course, with its real feature values left
completely untouched — confirmed by the exact same KS test from [Lesson 2](02-data-drift.md) finding no real
drift at all — but with a real, deliberate change to what the *correct label* means for a subset of rows,
simulating a genuine concept shift (for example, a diagnostic criterion changing) rather than a population
shift.

## Confirming: the real inputs have not drifted at all

```python
stat, p = ks_2samp(reference["mean radius"], X_test["mean radius"])
```
```
mean radius  KS=0.0446  p=0.9904  -> NO drift -- inputs look the same as always
```

A `p=0.9904` — about as clean a "no drift" result as [Lesson 2](02-data-drift.md)'s methodology can produce.
The real inputs genuinely have not changed.

## A real concept shift: same inputs, different real meaning

```python
shifted_mask = (y_original == 1) & (X_test["mean texture"] > X_test["mean texture"].quantile(0.60))
y_new_concept[shifted_mask] = 0   # same real row, now a different real correct answer
```
```
real rows whose TRUE label changed under the new concept: 18 / 114

real accuracy vs ORIGINAL concept (old labels): 0.9649
real accuracy vs SHIFTED concept (new labels):   0.8070

NO data drift was detected (p=0.9904), yet real accuracy differs by 0.1579
depending which real concept is now true
```

The real model's predictions never changed — it's the exact same model, scoring the exact same real rows, with
the exact same outputs. What changed is which answer actually counts as correct. Eighteen real rows that used
to be the right answer under the old criterion are now, under this lesson's simulated new criterion, wrong — a
real 0.1579 accuracy swing that [Lesson 2](02-data-drift.md)'s data-drift check, by design, cannot see at all.

## How It Actually Works

Data drift and concept drift are genuinely different real failure modes, and this lesson's result makes the
difference concrete rather than definitional: data drift is "the inputs look different from what I trained on";
concept drift is "the inputs look exactly the same, but the real relationship between input and correct answer
has moved." A model can suffer either one independently — this lesson deliberately held data drift at zero to
isolate concept drift's real effect on its own.

In a real production system, concept drift is the harder of the two to catch, precisely because nothing about
the *input* signals it — [Lesson 2](02-data-drift.md)'s whole detection strategy (compare feature distributions)
is structurally incapable of noticing it. Catching real concept drift requires either real, fresh ground-truth
labels arriving fast enough to compare against (rare in practice), or the kind of proxy signals
[Lesson 4](04-monitoring-without-ground-truth.md) builds for exactly this situation.

## Common mistakes

- Running only a data-drift check and treating "no drift detected" as "the model is still accurate" — this
  lesson's own real `p=0.9904` alongside a genuine 0.1579 accuracy swing is a direct, measured counterexample.
- Assuming concept drift requires the underlying real-world phenomenon to change — it can also come from a
  genuinely changed *labeling process* (a new diagnostic guideline, a revised fraud definition, an updated
  content policy), exactly as this lesson's simulated texture-threshold criterion shift represents.
- Building drift monitoring entirely around input features and never budgeting for the much harder, slower
  problem of getting real labels back fast enough to check for concept drift directly.

## Exercise

Design a concept-drift simulation of your own on this lesson's real test set — pick a different feature and
threshold than `mean texture` at the 60th percentile — and measure whether a smaller or larger real relabeled
subset produces a proportionally similar real accuracy swing, to get a feel for how concept drift's real impact
scales with how much of the real label space it touches.
