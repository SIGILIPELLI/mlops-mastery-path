---
description: "Four real model sizes showed the exact same +0.0088 accuracy gain over tiny -- for 1.16ms, 4.55ms, or 24.74ms of added real latency, depending on size."
---

# Cost & Latency Tradeoffs in Model Serving

## What was run

Four real `RandomForestClassifier` configurations — from a genuinely tiny model to a genuinely large one —
each real one trained, evaluated for real accuracy, and timed for real per-1000-row prediction latency,
logged to MLflow, to see exactly what a bigger real model actually buys over a smaller one.

## Four real configurations, both metrics measured together

```python
CONFIGS = [("tiny", 5, 3), ("small", 25, 5), ("medium", 100, 7), ("large", 500, 15)]
X_timed = pd.concat([X_test] * 10, ignore_index=True).iloc[:1000]
start = time.perf_counter()
for _ in range(5):
    model.predict(X_timed)
elapsed_per_1k = (time.perf_counter() - start) / 5 * 1000
```
```
config    n_est  depth   accuracy   latency/1k rows (ms)
tiny          5      3     0.9561                   0.79
small        25      5     0.9649                   1.95
medium      100      7     0.9649                   5.34
large       500     15     0.9649                  25.53
```

## A real, measured diminishing return

```
small   : +0.0088 accuracy for +1.16ms/1k-rows real latency
medium  : +0.0088 accuracy for +4.55ms/1k-rows real latency
large   : +0.0088 accuracy for +24.74ms/1k-rows real latency
```

A real, striking pattern: every one of the three larger configurations bought the exact same real accuracy
improvement over the tiny baseline (`+0.0088`) — `small`, `medium`, and `large` all landed on identical real
test accuracy (`0.9649`) — while paying a sharply increasing real latency cost to get there. Past `small`, this
specific real dataset simply had nothing further for a bigger model to find; the extra real capacity in
`medium` and `large` bought zero additional real accuracy, only more real latency.

## How It Actually Works

Cost and latency in model serving scale with real model complexity — more trees, deeper trees, more real
computation per prediction — but real accuracy doesn't necessarily scale alongside it. This lesson's numbers are
a direct, measured illustration of a genuinely common real pattern: a dataset or problem has a real ceiling on
how much a given model family can learn from it, and once a real model is complex enough to reach that ceiling,
more complexity only adds real cost without adding real value.

The real, practical conclusion this lesson's own numbers support isn't "bigger is better" or "smaller is
always fine" — it's that the real tradeoff has to be *measured*, per problem, rather than assumed. For this
specific real dataset, `small` (25 trees, depth 5) was the genuinely efficient choice: it captured all the real
accuracy `large` did, at roughly 1/13th the real latency. A different, harder real dataset might show a
genuinely different curve, with `large` continuing to earn its real cost well past where this one plateaued.

## Common mistakes

- Choosing model size based on accuracy alone, without ever measuring the real latency or compute cost that
  size actually buys — this lesson's own numbers show three configurations tied on accuracy while differing by
  more than 20x in real latency.
- Assuming a bigger, more complex model will always be at least as accurate as a smaller one — on a real,
  harder, noisier problem than this one, a model that's too large can genuinely overfit and score *worse* on
  real held-out data than a smaller one, not just cost more.
- Measuring latency once, at low batch sizes, and extrapolating — this lesson measured over a real, repeated
  1000-row batch specifically to get a stable number; a single-row timing measurement is dominated by one-off
  overhead and would not reflect real sustained throughput cost.

## Exercise

Re-run this lesson's exact real sweep with a fifth configuration between `tiny` and `small` (try
`n_estimators=15, max_depth=4`) to find, by direct real measurement rather than guessing, roughly where this
specific real dataset's accuracy ceiling actually starts — the real point past which added model size stops
paying for itself.
