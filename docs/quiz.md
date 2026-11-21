---
title: "Final Quiz"
description: "Test what you have learned across all four levels of MLOps Mastery Path with a 20-question final quiz."
---

# Final Quiz

20 questions drawn from all four levels of this course. You need **70% (14 out of 20)** to pass —
you can retake it as many times as you like. Your best result is saved in this browser.

<div id="mp-quiz-root"></div>

<script type="application/json" id="mp-quiz-data">
[
  {
    "question": "A model has 92% test accuracy but nobody can tell which data or code produced it. What is missing?",
    "options": [
      "A dashboard",
      "Experiment tracking and lineage (params, metrics, artifacts, code and data versions)",
      "More epochs",
      "A bigger model"
    ],
    "correctIndex": 1,
    "explanation": "Reproducibility."
  },
  {
    "question": "With the same seed, do two random draws match?\n```python\nimport random\nrandom.seed(3);a=random.random()\nrandom.seed(3);b=random.random()\nprint(a==b)\n```\n",
    "options": [
      "Maybe",
      "False",
      "Error",
      "True"
    ],
    "correctIndex": 3,
    "explanation": "Seeds make randomness repeatable, but library versions and hardware also matter. Verified by running it."
  },
  {
    "question": "You tuned hyperparameters repeatedly against the test set. What's wrong?",
    "options": [
      "The test set is now overfit; keep a separate final holdout",
      "Nothing",
      "Too few runs",
      "Bad seed"
    ],
    "correctIndex": 0,
    "explanation": "Selection bias."
  },
  {
    "question": "A production service should load 'the best model'. How should it reference it?",
    "options": [
      "Path",
      "By a registry alias (e.g. production), not a hard-coded version or file path",
      "Run id",
      "Latest file"
    ],
    "correctIndex": 1,
    "explanation": "Promotion and rollback by moving the alias."
  },
  {
    "question": "What should a promotion gate check?",
    "options": [
      "Metric thresholds, regression vs the current model, latency and schema compatibility",
      "Taste",
      "Only accuracy",
      "Nothing"
    ],
    "correctIndex": 0,
    "explanation": "Policy as code."
  },
  {
    "question": "Predictions are 'wrong' after a deploy though code is fine. A classic cause?",
    "options": [
      "GPU",
      "Training-serving skew: features computed differently online",
      "Disk",
      "Latency"
    ],
    "correctIndex": 1,
    "explanation": "Share transformations or use a feature store."
  },
  {
    "question": "A request has an unexpected column type and the service returns a prediction anyway. What was missing?",
    "options": [
      "Input schema validation at serving time",
      "Tracing",
      "More data",
      "A cache"
    ],
    "correctIndex": 0,
    "explanation": "Fail clearly instead of silently coercing."
  },
  {
    "question": "A new model must be evaluated on live traffic without affecting users. Which strategy?",
    "options": [
      "Canary",
      "Blue-green",
      "Big bang",
      "Shadow deployment (compare outputs, don't use them)"
    ],
    "correctIndex": 3,
    "explanation": "Safe comparison."
  },
  {
    "question": "A small percentage of users get the new model and metrics are checked before widening. What is it?",
    "options": [
      "Shadow",
      "Rollback",
      "A/B",
      "A canary release"
    ],
    "correctIndex": 3,
    "explanation": "Gradual exposure."
  },
  {
    "question": "Why randomize A/B tests by user rather than by request?",
    "options": [
      "Law",
      "A user should have a consistent experience and independent samples",
      "Cheaper",
      "Faster"
    ],
    "correctIndex": 1,
    "explanation": "Sticky assignment."
  },
  {
    "question": "CI for ML should include which checks beyond unit tests?",
    "options": [
      "Only lint",
      "Nothing",
      "Data validation, a small training smoke test and model metric gates",
      "Screenshots"
    ],
    "correctIndex": 2,
    "explanation": "Ml-specific CI."
  },
  {
    "question": "The pipeline retrains every night and one night the model degrades silently. What guards against shipping it?",
    "options": [
      "Faster GPU",
      "More retries",
      "Evaluation gates against the current production model before promotion",
      "Bigger data"
    ],
    "correctIndex": 2,
    "explanation": "No blind updates."
  },
  {
    "question": "Input feature distributions shift but labels aren't available yet. Which signal helps?",
    "options": [
      "Data drift metrics (PSI/KS) and prediction distribution monitoring",
      "Accuracy",
      "Loss",
      "Epochs"
    ],
    "correctIndex": 0,
    "explanation": "Proxy signals."
  },
  {
    "question": "A PSI of 0.3 is typically interpreted as?\n```python\np=0.3\nprint('stable' if p<0.1 else 'moderate drift' if p<0.25 else 'significant drift')\n```\n",
    "options": [
      "stable",
      "moderate drift",
      "significant drift",
      "none"
    ],
    "correctIndex": 2,
    "explanation": "Common rule of thumb: <0.1 stable, 0.1-0.25 moderate, >0.25 significant. Verified by running it."
  },
  {
    "question": "Fraud patterns change so inputs look similar but outcomes differ. What is it?",
    "options": [
      "Data drift",
      "Latency",
      "Concept drift (the input-output relationship changed)",
      "Skew"
    ],
    "correctIndex": 2,
    "explanation": "Needs labeled outcomes to detect."
  },
  {
    "question": "What makes an ML alert useful?",
    "options": [
      "It's actionable, tied to impact and links to the model version and runbook",
      "Is silent",
      "Fires on every drift",
      "Email only"
    ],
    "correctIndex": 0,
    "explanation": "Avoid alert fatigue."
  },
  {
    "question": "When is automatic retraining risky?",
    "options": [
      "Never",
      "With GPUs",
      "Always",
      "When bad or poisoned data can flow in and degrade the model silently"
    ],
    "correctIndex": 3,
    "explanation": "Add validation and gates."
  },
  {
    "question": "Which artifact documents a model's purpose, data, limits and risks?",
    "options": [
      "A model card",
      "A chart",
      "A key",
      "A license"
    ],
    "correctIndex": 0,
    "explanation": "Governance."
  },
  {
    "question": "A cheaper model handles most requests and an expensive one the hard cases. What is this?",
    "options": [
      "Shadow",
      "Canary",
      "A cascade (cost/latency trade-off)",
      "Ensemble"
    ],
    "correctIndex": 2,
    "explanation": "Serving cost control."
  },
  {
    "question": "Which is an MLOps anti-pattern?",
    "options": [
      "Deploying manually from a notebook with no versioning or monitoring",
      "CI/CD",
      "Registry",
      "Drift alerts"
    ],
    "correctIndex": 0,
    "explanation": "Fix with pipelines and registry."
  }
]
</script>

## Coding challenges

Try these on your own computer, then compare your output. Nothing you write is sent anywhere.

### Detect data drift with PSI and KS

Implement psi (binned population stability index using the reference range, clamping out-of-range live values), level (stable / moderate / significant) and a two-sample KS statistic. Compare a live sample from the same distribution with one whose mean has shifted.

**Starter code**

```python
import math

def psi(expected, actual, bins=5):
    # bin both samples with edges from the reference range (clamp out-of-range values into the end bins),
    # turn counts into shares (floor at 1e-4) and return sum((a - e) * ln(a / e))
    return 0.0

def level(p):
    # < 0.1 "stable", < 0.25 "moderate drift", otherwise "significant drift"
    return ""

def ks(a, b):
    # two-sample Kolmogorov-Smirnov: the largest gap between the two empirical CDFs
    return 0.0

ref = [i / 10 for i in range(100)]                       # training feature: roughly uniform 0..9.9
same = [i / 10 + 0.05 for i in range(100)]               # same distribution
shifted = [i / 10 + 3 for i in range(100)]               # mean shifted by +3
for name, live in [("same", same), ("shifted", shifted)]:
    p = psi(ref, live)
    print(f"{name:8s} psi={p:.3f} -> {level(p)}  ks={ks(ref, live):.2f}")
```

**Expected output**

```text
same     psi=0.001 -> stable  ks=0.01
shifted  psi=1.864 -> significant drift  ks=0.30
```

<details><summary>Show solution and explanation</summary>

<pre><code>import math

def psi(expected, actual, bins=5):
    &quot;&quot;&quot;Population Stability Index between a reference sample and a live sample.&quot;&quot;&quot;
    lo, hi = min(expected), max(expected)
    edges = [lo + (hi - lo) * i / bins for i in range(bins + 1)]
    def share(xs):
        counts = [0] * bins
        for x in xs:
            i = min(max(int((x - lo) / (hi - lo) * bins), 0), bins - 1)
            counts[i] += 1
        return [max(c / len(xs), 1e-4) for c in counts]
    e, a = share(expected), share(actual)
    return sum((ai - ei) * math.log(ai / ei) for ei, ai in zip(e, a))

def level(p):
    return &quot;stable&quot; if p &lt; 0.1 else &quot;moderate drift&quot; if p &lt; 0.25 else &quot;significant drift&quot;

def ks(a, b):
    &quot;&quot;&quot;Two-sample Kolmogorov-Smirnov statistic (max CDF gap).&quot;&quot;&quot;
    pts = sorted(set(a + b))
    return max(abs(sum(x &lt;= p for x in a) / len(a) - sum(x &lt;= p for x in b) / len(b)) for p in pts)

ref = [i / 10 for i in range(100)]                       # training feature: roughly uniform 0..9.9
same = [i / 10 + 0.05 for i in range(100)]               # same distribution
shifted = [i / 10 + 3 for i in range(100)]               # mean shifted by +3
for name, live in [(&quot;same&quot;, same), (&quot;shifted&quot;, shifted)]:
    p = psi(ref, live)
    print(f&quot;{name:8s} psi={p:.3f} -&gt; {level(p)}  ks={ks(ref, live):.2f}&quot;)
</code></pre>

<p>The same-distribution sample scores near zero on both metrics, while the shifted sample produces a large PSI and a high KS statistic, signalling drift without needing labels. Thresholds are rules of thumb. Verified by running the solution.</p>
</details>

### A registry with promotion gates and rollback

Implement Registry.promote: reject versions below the minimum AUC, above the latency limit or regressing against the current production version, otherwise move the production alias; rollback then restores the previous version from the history.

**Starter code**

```python
class Registry:
    def __init__(self):
        self.versions = {}          # version -> {"metrics": {...}, "run": ...}
        self.alias = {}             # alias -> version
        self.history = []

    def register(self, metrics, run):
        v = len(self.versions) + 1
        self.versions[v] = {"metrics": metrics, "run": run}
        return v

    def promote(self, version, gates):
        # gates: min_auc, max_p95_ms, max_regression. Collect failure messages ("auc X < Y", "p95 Nms > Mms",
        # "auc regression vs vK: X < base") and append "REJECT vN: <failures joined by '; '>" to history,
        # returning False; if all pass set alias["production"] = version, append
        # "PROMOTE vN (previous: <old or None>)" and return True. The regression gate only applies when a
        # production version exists and the new auc is below its auc minus max_regression.
        return False

    def rollback(self):
        for line in reversed(self.history):
            if line.startswith("PROMOTE") and "previous: None" not in line:
                prev = int(line.split("previous: ")[1].rstrip(")"))
                self.alias["production"] = prev
                self.history.append(f"ROLLBACK to v{prev}")
                return prev
        raise RuntimeError("nothing to roll back to")

gates = {"min_auc": 0.80, "max_p95_ms": 100, "max_regression": 0.01}
r = Registry()
v1 = r.register({"auc": 0.84, "p95_ms": 60}, "run-a")
v2 = r.register({"auc": 0.79, "p95_ms": 55}, "run-b")
v3 = r.register({"auc": 0.86, "p95_ms": 140}, "run-c")
v4 = r.register({"auc": 0.87, "p95_ms": 70}, "run-d")
for v in (v1, v2, v3, v4):
    print(v, r.promote(v, gates))
print("production ->", r.alias["production"])
print("rollback ->", r.rollback(), "production ->", r.alias["production"])
print(*r.history, sep="\n")
```

**Expected output**

```text
1 True
2 False
3 False
4 True
production -> 4
rollback -> 1 production -> 1
PROMOTE v1 (previous: None)
REJECT v2: auc 0.79 < 0.8; auc regression vs v1: 0.79 < 0.84
REJECT v3: p95 140ms > 100ms
PROMOTE v4 (previous: 1)
ROLLBACK to v1
```

<details><summary>Show solution and explanation</summary>

<pre><code>class Registry:
    def __init__(self):
        self.versions = {}          # version -&gt; {&quot;metrics&quot;: {...}, &quot;run&quot;: ...}
        self.alias = {}             # alias -&gt; version
        self.history = []

    def register(self, metrics, run):
        v = len(self.versions) + 1
        self.versions[v] = {&quot;metrics&quot;: metrics, &quot;run&quot;: run}
        return v

    def promote(self, version, gates):
        &quot;&quot;&quot;Move the &#x27;production&#x27; alias to `version` only if every gate passes; otherwise explain why not.&quot;&quot;&quot;
        m = self.versions[version][&quot;metrics&quot;]
        failures = []
        if m[&quot;auc&quot;] &lt; gates[&quot;min_auc&quot;]:
            failures.append(f&quot;auc {m[&#x27;auc&#x27;]} &lt; {gates[&#x27;min_auc&#x27;]}&quot;)
        if m[&quot;p95_ms&quot;] &gt; gates[&quot;max_p95_ms&quot;]:
            failures.append(f&quot;p95 {m[&#x27;p95_ms&#x27;]}ms &gt; {gates[&#x27;max_p95_ms&#x27;]}ms&quot;)
        current = self.alias.get(&quot;production&quot;)
        if current is not None:
            base = self.versions[current][&quot;metrics&quot;][&quot;auc&quot;]
            if m[&quot;auc&quot;] &lt; base - gates[&quot;max_regression&quot;]:
                failures.append(f&quot;auc regression vs v{current}: {m[&#x27;auc&#x27;]} &lt; {base}&quot;)
        if failures:
            self.history.append(f&quot;REJECT v{version}: &quot; + &quot;; &quot;.join(failures))
            return False
        self.alias[&quot;production&quot;] = version
        self.history.append(f&quot;PROMOTE v{version} (previous: {current})&quot;)
        return True

    def rollback(self):
        for line in reversed(self.history):
            if line.startswith(&quot;PROMOTE&quot;) and &quot;previous: None&quot; not in line:
                prev = int(line.split(&quot;previous: &quot;)[1].rstrip(&quot;)&quot;))
                self.alias[&quot;production&quot;] = prev
                self.history.append(f&quot;ROLLBACK to v{prev}&quot;)
                return prev
        raise RuntimeError(&quot;nothing to roll back to&quot;)

gates = {&quot;min_auc&quot;: 0.80, &quot;max_p95_ms&quot;: 100, &quot;max_regression&quot;: 0.01}
r = Registry()
v1 = r.register({&quot;auc&quot;: 0.84, &quot;p95_ms&quot;: 60}, &quot;run-a&quot;)
v2 = r.register({&quot;auc&quot;: 0.79, &quot;p95_ms&quot;: 55}, &quot;run-b&quot;)
v3 = r.register({&quot;auc&quot;: 0.86, &quot;p95_ms&quot;: 140}, &quot;run-c&quot;)
v4 = r.register({&quot;auc&quot;: 0.87, &quot;p95_ms&quot;: 70}, &quot;run-d&quot;)
for v in (v1, v2, v3, v4):
    print(v, r.promote(v, gates))
print(&quot;production -&gt;&quot;, r.alias[&quot;production&quot;])
print(&quot;rollback -&gt;&quot;, r.rollback(), &quot;production -&gt;&quot;, r.alias[&quot;production&quot;])
print(*r.history, sep=&quot;\n&quot;)
</code></pre>

<p>Gating promotion on quality, latency and regression protects production from worse models, and alias-based deployment makes rollback a pointer move. This is a teaching model of a registry. Verified by running the solution.</p>
</details>
