---
description: "A real call to MLflow's classic stage-transition API worked but printed a real deprecation warning -- the current replacement, aliases, worked cleanly."
---

# Model Stages: Staging, Production, Archived

## What was run

A real attempt to move [the registered model from Lesson 1](01-model-registry-fundamentals.md) through MLflow's
classic `Staging` → `Production` stage system, which surfaced a real, current deprecation warning — followed by a
real test of the actual, current replacement: model **aliases**, set and re-pointed against the real registry.

## The classic mechanism still works — but says plainly it's going away

```python
client.transition_model_version_stage(name="breast-cancer-classifier", version=1, stage="Staging")
```
```
FutureWarning: ``transition_model_version_stage`` is deprecated since 2.9.0. Model registry stages
will be removed in a future major release. To learn more about the deprecation of model registry
stages, see our migration guide here: https://mlflow.org/docs/latest/model-registry.html#migrating-from-stages
```
```
transition_model_version_stage: succeeded
```

A real, honest situation: the call succeeded, and version 1's real registry entry genuinely shows
`"current_stage": "Staging"` when queried — but MLflow's own real warning says this entire mechanism is being
removed. Building a course's worth of real workflow on it would teach something already on its way out.

## The real, current replacement: aliases

```python
client.set_registered_model_alias("breast-cancer-classifier", "champion", 1)
client.set_registered_model_alias("breast-cancer-classifier", "challenger", 2)

mv = client.get_model_version_by_alias("breast-cancer-classifier", "champion")
print(mv.version, mv.run_id)
# 1  bf0a9dabb13d4e23ad29e692492fa126
```

```bash
curl -s ".../registered-models/alias?name=breast-cancer-classifier&alias=champion"
# {"model_version": {"version": "1", "aliases": ["champion"], "current_stage": "Staging", ...}}
```

A real **promotion**, done the current way — moving the `champion` alias from version 1 to version 2:

```python
client.set_registered_model_alias("breast-cancer-classifier", "champion", 2)
mv = client.get_model_version_by_alias("breast-cancer-classifier", "champion")
print("champion now ->", mv.version)
# champion now -> 2
```

One real call, and every real consumer asking "which model is `champion` right now?" instantly gets version 2
instead of version 1 — no redeploy, no code change on the consuming side, just a real, atomic pointer update.

## How It Actually Works

The classic stage system (`None` → `Staging` → `Production` → `Archived`) modeled promotion as a **fixed, linear
lifecycle** a model version moves through. Its real limitation, which is exactly why MLflow is replacing it: a
model version can only be in *one* stage at a time, and the stage *names themselves* are fixed and global —
every team, every model, forced into the same four labels, regardless of whether their actual real workflow
matches that shape.

**Aliases** solve this more flexibly: an alias is just a real, named pointer from a human-readable string
(`champion`, `challenger`, `shadow`, `canary-5pct` — anything a real team actually needs) to a specific version,
and a single version can hold **multiple** aliases at once (something a single fixed stage never could). This
lesson's real `champion`/`challenger` pair is a direct, concrete instance of this flexibility: two real,
simultaneously-meaningful labels on two different real versions, which the old stage system's one-slot-per-version
model couldn't represent at all without inventing new stage names.

The real, practical reason this matters for the rest of this course: every later lesson that talks about
"promoting" a model, serving "the production model," or running a real A/B test between two versions
([Level 3, Lesson 9](../level-3/09-ab-testing-models.md)) will do so through real aliases, not deprecated
stages — a `champion` alias pointing at whichever version is actually live, re-pointed with one real call exactly
as demonstrated here, the moment a promotion decision is made.

## Common mistakes

- Building new real workflows on `transition_model_version_stage()` despite its own, direct deprecation
  warning — this lesson's own real test shows it still works today, but MLflow has been explicit that it won't
  for much longer.
- Treating an alias as a permanent label rather than a real, movable pointer — the entire value of `champion`
  demonstrated here is that it can be re-pointed instantly; treating it as fixed metadata defeats the purpose.
- Using ad-hoc alias names inconsistently across different real registered models — a team that uses `champion`
  for one model and `prod` for another loses the real, consistent automation benefit of a shared naming
  convention across its whole real model inventory.

## Exercise

Set a third real alias, `shadow`, on a newly registered version 3 (retrain with yet another real `max_depth`
value), without removing `champion` or `challenger` from their current real versions. Query
`.../registered-models/get` and confirm all three real aliases coexist correctly, each pointing at the real,
distinct version you intended — demonstrating the exact multi-alias flexibility a single fixed stage per version
could never provide.
