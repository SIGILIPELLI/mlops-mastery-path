---
description: "Autolog captured 19 real params vs 2 logged manually, but silently captured zero real test metrics when accuracy_score() was used instead of model.score()."
---

# Autologging: What It Captures and What It Misses

## What was run

`mlflow.sklearn.autolog()` enabled, then the exact same real training run logged two different ways: once
calling the model's own `.score(X_test, y_test)` method, once computing the identical real result with
`sklearn.metrics.accuracy_score()` instead — to find out, by direct comparison, exactly what autologging
captures automatically and exactly where it silently stops.

## Far more than manual logging captured — for free

```python
mlflow.sklearn.autolog()
model = RandomForestClassifier(n_estimators=50, max_depth=5, random_state=42)
model.fit(X_train, y_train)
```

```bash
curl -s ".../api/2.0/mlflow/runs/get?run_id=..."
```
```
real params auto-captured: 19
  bootstrap=True, ccp_alpha=0.0, class_weight=None, criterion=gini, max_depth=5,
  max_features=sqrt, max_leaf_nodes=None, max_samples=None, ...
```

**19 real params**, automatically — every single scikit-learn hyperparameter the model actually has, including
ones never explicitly set (`bootstrap`, `criterion`, `class_weight`, and more). This is strictly more than the
real manual logging from [Lesson 1](01-what-mlops-solves.md), which only logged the two params someone remembered
to call `mlflow.log_param()` for.

## A real, honest gap: test metrics only sometimes captured

```python
# Version A: using the model's own .score() method
acc = model.score(X_test, y_test)
```
```
real metrics auto-captured: training_precision_score, training_recall_score, training_f1_score,
  training_accuracy_score, training_log_loss, training_roc_auc, training_score,
  RandomForestClassifier_score_X_test = 0.9649122807017544   <- the real test result, captured
```

```python
# Version B: using sklearn.metrics.accuracy_score() instead -- an equally common, idiomatic pattern
acc = accuracy_score(y_test, model.predict(X_test))
```
```
real metrics auto-captured: training_precision_score, training_recall_score, training_f1_score,
  training_accuracy_score, training_log_loss, training_roc_auc, training_score
  <- no test metric at all. Zero.
```

Both versions compute the **exact same real number**, `0.9649122807017544`. Autolog captured it in version A and
**completely missed it in version B** — a real, silent gap. It turns out autolog's test-metric capture works by
patching the model's own `.score()` method specifically; computing the identical result through
`sklearn.metrics.accuracy_score()` — arguably the more common, idiomatic way many real codebases actually compute
test accuracy — never touches that patched method, and autolog has no way to know a test evaluation happened at
all.

## How It Actually Works

Autologging works by **patching** specific, known function calls (model constructors, `.fit()`, `.score()`) to
automatically log their real inputs and outputs without any explicit `mlflow.log_*()` calls in the training code.
This is genuinely powerful for the calls it knows to watch — this lesson's real 19-param capture is strictly
richer than what most teams would bother logging by hand — but it is fundamentally limited to the *specific* calls
it patches. Every one of the 7 `training_*` metrics in this lesson's real output is computed **on the training
set**, because autolog hooks into `.fit()`, which only ever sees training data; it has no way to know a `.score()`
call is evaluating held-out test data unless that *exact* patched method is the one actually called.

The real, practical rule this lesson's comparison establishes: autolog is not a replacement for understanding what
it does and doesn't see — it's a real, substantial head start that still needs **explicit logging for anything
that happens outside its patched call surface.** A real team's test accuracy, the single most important number for
deciding whether a model should ship, should never depend on which of two equally common, equally correct ways of
computing it happened to be used — which is exactly why explicit `mlflow.log_metric("test_accuracy", ...)`, as
every earlier lesson in this level did by hand, remains necessary even with autologging enabled.

## Common mistakes

- Trusting autolog as a complete record without checking, the way this lesson did directly, which specific real
  calls it actually patches — a real, important number (test accuracy) can be silently absent depending purely on
  which equivalent API was used to compute it.
- Assuming autolog's absence of a metric means the evaluation didn't happen, rather than checking whether it
  happened through a code path autolog doesn't patch — this lesson's version B genuinely computed the real test
  accuracy; autolog just never saw it.
- Disabling manual logging entirely once autolog is enabled — the real, right pattern is both together: autolog
  for the comprehensive, automatic baseline (all 19 real params, for free), explicit logging for anything
  specifically important that autolog's real, finite patch surface doesn't cover.

## Exercise

Check autolog's real behavior for one more common pattern: compute test accuracy with
`model.predict(X_test)` compared manually against `y_test` using plain Python (`(model.predict(X_test) ==
y_test).mean()`), with no call to either `.score()` or `accuracy_score()`. Confirm whether autolog captures this
real result or misses it too, and add this finding to your own list of exactly which test-evaluation patterns
autolog can and can't see.
