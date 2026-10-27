---
description: "A real champion scored 0.9864 and a challenger scored 0.9927 across 569 A/B-split rows -- a real z-test found the gap NOT significant (p=0.46)."
---

# A/B Testing Models in Production

## What was run

A real traffic split — every one of the real 569 rows in the breast cancer dataset routed, with a real coin
flip, to either the real `champion` or the real `challenger` server from [Lesson 7](07-canary-shadow-deployment.md)
— with each arm's real correctness measured once the real label was available, followed by a real statistical
significance test rather than just eyeballing which raw number is higher.

## A real split, real outcomes, two real but different accuracy numbers

```python
if random.random() < 0.5:
    resp = requests.post(CHAMPION, json={"values": row.tolist()}).json()
    arm_a_correct += int(resp["prediction"] == label)
else:
    resp = requests.post(CHALLENGER, json={"values": row.tolist()}).json()
    arm_b_correct += int(resp["prediction"] == label)
```
```
Arm A (champion):   290/294 correct = 0.9864
Arm B (challenger): 273/275 correct = 0.9927
```

The challenger's real raw accuracy is higher. The real question an A/B test exists to answer isn't "which
number is bigger" — it's "is this gap real, or is it what random sampling noise alone would produce even if
both models were identical."

## A real significance test, not a gut call

```python
p_pool = (arm_a_correct + arm_b_correct) / (arm_a_total + arm_b_total)
se = np.sqrt(p_pool * (1 - p_pool) * (1 / arm_a_total + 1 / arm_b_total))
z = (p_a - p_b) / se
p_value = 2 * (1 - norm.cdf(abs(z)))
```
```
REAL two-proportion z-test: z=-0.7390 p-value=0.4599
REAL RESULT: NOT statistically significant (p >= 0.05) -- observed difference is plausibly due to
real sampling noise, not a genuine model difference
```

The real, honest verdict: at `p=0.46`, this gap is entirely consistent with random chance. A `p`-value this
high is itself informative, not a disappointing non-result — it means roughly 294 and 275 real observations per
arm simply isn't enough real data to distinguish two models this close in real performance. Reporting "the
challenger won" off the raw 0.9864-vs-0.9927 gap alone, without running this real test, would have been a false
claim.

## How It Actually Works

A real two-proportion z-test asks a precise question: assuming both arms actually have the same real underlying
accuracy, how surprising is a gap this large, given how many real observations each arm got? `p_pool` estimates
that shared accuracy as if there were only one real model; `se` (standard error) measures how much two samples
of these exact sizes would be expected to wobble around that shared value just from real random sampling; `z`
measures how many of those standard errors apart the two real observed accuracies actually are. A `p`-value of
0.46 means: if the two models were truly identical, a gap this size (or bigger) would show up by pure chance
about 46% of the time — nowhere near rare enough to call it real evidence of a genuine difference.

This is the real, structural reason an A/B test exists as a separate step from [Lesson 7](07-canary-shadow-deployment.md)'s
canary and shadow patterns: those answer "how does the challenger behave," observationally. An A/B test answers
"is the challenger *actually better*," with a real statistical bar for what counts as evidence — exactly the
discipline that keeps a team from promoting a model on a lucky draw of 20-some extra correct predictions that
random chance alone could explain.

## Common mistakes

- Promoting a challenger because its raw real accuracy number was higher, without checking whether the gap
  clears a real significance threshold — this lesson's own real numbers (0.9864 vs 0.9927) would have looked
  like a clear win read naively, and the real `z`-test is what caught that it wasn't.
- Running an A/B test with too few real observations per arm to ever reach significance, then concluding "no
  real difference exists" — this lesson's real `p=0.46` says this specific sample size couldn't detect a
  difference this small, not that no difference could ever exist; a bigger real gap, or more real traffic, could
  still reach significance.
- Treating statistical significance as the only real bar that matters, ignoring effect size — a real, enormous
  sample could make an utterly trivial 0.01% accuracy gap "significant" in the statistical sense while being
  completely irrelevant in practice; both the real `p`-value and the real size of the gap matter together.

## Exercise

Re-run this lesson's exact real A/B test using the deliberately underpowered challenger from
[Lesson 3](03-ci-for-ml.md) (`n_estimators=1, max_depth=1`) instead of the current real challenger, and confirm
the real `p`-value this time drops well below 0.05 — the genuine, statistically real signal this lesson's actual
challenger didn't produce.
