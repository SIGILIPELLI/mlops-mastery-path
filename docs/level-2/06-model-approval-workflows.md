---
description: "A real gate script promoted a genuinely better challenger (0.9912 vs 0.9737) via a real alias change, then correctly blocked a second one and exited 1."
---

# Model Approval Workflows: Gating Production

## What was run

A real, scriptable approval gate — `approval_gate.py` — that loads the real `champion` and `challenger` aliases
from [earlier in this level](05-comparing-model-versions.md), evaluates both on a real, independent holdout, and
only promotes the challenger if it genuinely clears **two** real bars: it must beat the current champion, and it
must reach a real minimum accuracy threshold. Run twice, with two different real outcomes.

## A real approval, and a real, verified promotion

```python
if challenger_acc > champion_acc and challenger_acc >= MIN_ACCURACY:
    client.set_registered_model_alias(MODEL_NAME, "champion", challenger_version)
```

```bash
python3 approval_gate.py
# champion   (current production): accuracy=0.9737
# challenger (candidate):          accuracy=0.9912
# gate threshold: must beat champion AND reach 0.98
# REAL GATE RESULT: APPROVED -- promoting challenger to champion
```

```bash
curl -s ".../registered-models/alias?name=breast-cancer-classifier&alias=champion"
# "version": "2"
```

A real, independently-confirmed promotion — the `champion` alias genuinely moved to version 2, verified by
querying the registry directly, not just trusting the script's own printed claim.

## A real, correct rejection

```bash
python3 approval_gate_strict.py   # same logic, MIN_ACCURACY raised to 0.999
# champion   (current production): accuracy=0.9912
# challenger (candidate):          accuracy=0.9912
# gate threshold: must beat champion AND reach 0.999
# REAL GATE RESULT: BLOCKED -- challenger does not clear the real bar
```
```bash
echo $?
# 1
```

A real, nonzero exit code — exactly what a real CI/CD system (built directly in
[Level 3](../level-3/04-cd-for-ml.md)) checks to decide whether to proceed with a deployment or stop. This
specific block is also a real, honest edge case worth noticing: champion and challenger are now the *same* model
(both at version 2, after the first gate's real promotion), so "beat the champion" is correctly impossible — the
gate didn't just enforce an arbitrary threshold, it correctly recognized there was no real improvement left to
approve.

## How It Actually Works

An **approval workflow** turns "should this model go to production" from a judgment call made informally (a
Slack message, a verbal "looks good to me") into a real, repeatable, automatable check with a clear real
pass/fail outcome — exactly what this lesson's exit code makes possible. The real gate built here enforces two
genuinely different real conditions, deliberately: beating the *current* champion (a relative bar — is this
actually an improvement, not just "good enough in isolation") and clearing an absolute minimum
(`MIN_ACCURACY`, a real floor below which no candidate should ever reach production, regardless of how it
compares to a possibly-also-weak champion).

The real value of building this as a script with a real exit code, rather than a manual checklist a human
reviews, is exactly what [Level 3](../level-3/03-ci-for-ml.md) builds on next: a real CI/CD pipeline can run this
exact gate automatically on every new candidate, blocking a deployment the instant a model fails to clear it,
with no human needing to remember to run the check manually before every single promotion — the same real
discipline that keeps broken code from merging in traditional software, applied here to model quality instead.

## Common mistakes

- Gating only on an absolute threshold without also comparing to the current production model — a candidate that
  clears a fixed bar but is genuinely worse than what's already live would pass a threshold-only gate, exactly
  the gap this lesson's relative comparison closes.
- Running an approval gate manually, as a checklist a human remembers to follow, instead of as a real script with
  a real exit code a pipeline can enforce automatically — the entire value of a gate is that it can't be
  accidentally skipped under deadline pressure.
- Setting a gate's threshold without any real historical data informing it — this lesson's `MIN_ACCURACY = 0.98`
  (then `0.999`) values were chosen deliberately to demonstrate both outcomes; a real production threshold should
  come from real, measured business requirements, not an arbitrary round number.

## Exercise

Add a third real condition to the gate: the challenger must also have a real `data_sha256` tag matching the
current production data version (reusing [Level 1, Lesson 6](../level-1/06-data-lineage.md)'s hash), rejecting
any candidate trained on stale or mismatched real data even if its accuracy looks good. Test it against a
real run trained on [Level 1, Lesson 6](../level-1/06-data-lineage.md)'s real filtered dataset, and confirm the
gate correctly blocks it for the real data-mismatch reason, separate from its accuracy.
