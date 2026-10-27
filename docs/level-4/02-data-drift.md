---
description: "A real same-population sample triggered zero drift flags across 4 features (p>=0.35 each); a real shifted subset triggered DRIFT on all 4 (p<0.02)."
---

# Data Drift: When the Input Distribution Changes

## What was run

A real two-sample Kolmogorov-Smirnov test, comparing a real reference population (the first 400 rows of the
breast cancer dataset, standing in for "what the model was trained on") against two different real "new"
batches: one a plain random sample of the same real population, one a genuinely shifted real subset (only
large-tumor cases) — checking whether the test correctly tells them apart.

## Real batch A: same population, no real drift

```python
new_batch_same_pop = X.sample(n=100, random_state=1)
stat, p = ks_2samp(reference[feat], new_batch_same_pop[feat])
```
```
mean radius           KS=0.0800  p=0.6680  -> no significant drift
mean texture          KS=0.0550  p=0.9629  -> no significant drift
mean smoothness       KS=0.0925  p=0.4841  -> no significant drift
worst area            KS=0.1025  p=0.3561  -> no significant drift
```

## Real batch B: a genuinely shifted population

```python
new_batch_shifted = X[X["mean radius"] > X["mean radius"].quantile(0.75)]
```
```
mean radius           KS=0.7250  p=0.0000  -> DRIFT DETECTED  (reference mean=14.32, new mean=19.18)
mean texture          KS=0.3024  p=0.0000  -> DRIFT DETECTED  (reference mean=18.95, new mean=21.54)
mean smoothness       KS=0.1528  p=0.0131  -> DRIFT DETECTED  (reference mean=0.10, new mean=0.10)
worst area            KS=0.6459  p=0.0000  -> DRIFT DETECTED  (reference mean=917.18, new mean=1680.96)
```

A real, clean contrast: the same statistical test correctly found nothing unusual in a genuinely unshifted
sample, and correctly flagged every one of the four checked features — even `mean smoothness`, whose real
reference and shifted means print identically at 2 decimal places (`0.10` vs `0.10`) but whose real underlying
distributions still differed enough (`p=0.0131`) for the test to catch what a quick glance at rounded averages
would miss entirely.

## How It Actually Works

The KS test compares two real samples' empirical distributions directly — not just their means — by measuring
the largest real gap between their cumulative distribution functions. That's exactly why `mean smoothness`
still triggered a real flag despite near-identical rounded means: the two real distributions' *shapes* differed
enough even though their central tendency happened to look similar at low precision, which is exactly the kind
of real drift a naive "did the average change much" check would miss.

Data drift specifically means the real *input* distribution has shifted — the population of patients, sensors,
users, or whatever a model actually sees in production no longer resembles what it was trained on — independent
of whether the real relationship between inputs and the correct label has changed at all. That's a distinct real
phenomenon from [concept drift](03-concept-drift.md), covered next, where the inputs can look exactly the same
but what they actually *mean* has shifted instead.

## Common mistakes

- Monitoring only a model's output distribution and assuming that covers input drift too — this lesson's check
  looked directly at real feature values (`mean radius`, `worst area`, ...) arriving at the model, which is
  necessary to catch a shift before it's even had a chance to affect what the model predicts.
- Comparing only means or other single summary statistics instead of full distributions — this lesson's real
  `mean smoothness` result is a direct, measured counterexample to "the averages look the same, so nothing
  changed."
- Running a drift check once and treating the result as permanent — a real reference population needs
  periodic real re-validation itself, since what counted as "normal" when a model was trained can legitimately
  evolve over time without that being drift in the production-vs-training sense this lesson measured.

## Exercise

Run this lesson's exact real KS-test methodology against a *partial* shift — a new batch that's 80% real,
unshifted rows and only 20% large-tumor rows — and find the real mixing threshold at which `p` crosses back
above `0.05` for `mean radius`, to get a direct, measured sense of how much real contamination this specific
test can and can't detect at this sample size.
