---
description: "A real tag query for owner=alice correctly found exactly her 2 runs out of 3; a separate query for purpose=candidate found a different, overlapping pair."
---

# Tagging & Organizing Experiments at Scale

## What was run

Three real runs, each logged with a real set of tags — `owner`, `purpose`, `model_family` — then queried two
different real ways: once filtering by `owner`, once by `purpose`, to confirm tags actually support the kind of
cross-cutting real organization a growing team needs, not just a label nobody ever queries.

## Real runs, real overlapping tags

```python
configs = [
    ('rf-50-5',  50,  5,  {'owner': 'alice', 'purpose': 'baseline',  'model_family': 'tree'}),
    ('rf-100-10', 100, 10, {'owner': 'bob',   'purpose': 'candidate', 'model_family': 'tree'}),
    ('rf-200-15', 200, 15, {'owner': 'alice', 'purpose': 'candidate', 'model_family': 'tree'}),
]
for name, n_est, depth, tags in configs:
    with mlflow.start_run(run_name=name):
        for k, v in tags.items():
            mlflow.set_tag(k, v)
        ...
```

## Two real queries, two real, different, correct results

```bash
curl -s ".../runs/search" -d '{"filter": "tags.owner = \"alice\""}'
```
```
rf-200-15  {'owner': 'alice', 'purpose': 'candidate', 'model_family': 'tree'}
rf-50-5    {'owner': 'alice', 'purpose': 'baseline',  'model_family': 'tree'}
```

```bash
curl -s ".../runs/search" -d '{"filter": "tags.purpose = \"candidate\""}'
```
```
rf-200-15  {'owner': 'alice', 'purpose': 'candidate', 'model_family': 'tree'}
rf-100-10  {'owner': 'bob',   'purpose': 'candidate', 'model_family': 'tree'}
```

Two real, independent queries, each returning exactly the correct real subset — `rf-200-15` correctly appears in
*both* results, because it genuinely satisfies both filters (Alice's run that's also a candidate), while
`rf-50-5` and `rf-100-10` each appear in only the query matching their own real tags. This cross-cutting behavior
— one run matching multiple independent real criteria — is the entire point of tags.

## How It Actually Works

A **tag** is free-form metadata attached to a run that has no effect on training itself — unlike a param (an
actual model input) or a metric (an actual measured output), a tag exists purely to make a run *findable* later,
by whatever real dimension a team actually needs to search on. This lesson's three tags demonstrate three
genuinely different real organizing axes: `owner` (who's responsible for this run — useful when a team member
needs to find their own work among hundreds of runs), `purpose` (baseline vs candidate vs experiment — useful for
filtering out exploratory noise when only real, serious candidates matter), and `model_family` (useful once a
team is comparing fundamentally different approaches, not just hyperparameters of the same one).

The real, practical reason this matters at scale: a small team with ten real runs can scroll through all of them
by eye. A real team accumulating hundreds or thousands of runs across months cannot — and the real queries this
lesson ran directly (`tags.owner = "alice"`, `tags.purpose = "candidate"`) are exactly the mechanism that keeps
that larger real history navigable. Tags composed together (as this lesson's `rf-200-15` run, matching *both*
filters, demonstrates) let a team ask precise, real questions like "show me Alice's candidate runs from the tree
family" without needing a new, bespoke tracking structure for every new way of slicing the data.

## Common mistakes

- Using a metric or param field to store information that's really just organizational metadata (who ran this,
  why) — tags exist specifically so this kind of information doesn't pollute the real training-input/output
  record a param or metric is supposed to represent.
- Tagging inconsistently — `owner: "Alice"` on one run and `owner: "alice"` on another will not match the same
  real filter query, silently fragmenting what should be one real, queryable group.
- Adding tags without ever actually querying by them — a tag that's written but never searched provides none of
  the real, practical value this lesson's two direct queries demonstrated; tags are only useful to the extent a
  team actually builds real queries around them.

## Exercise

Add a fourth real tag, `dataset_version`, to all three of this lesson's runs (reusing the real SHA-256 hash
approach from [Lesson 6](06-data-lineage.md)), then write a real, combined query filtering on *two* tags at once
(`tags.owner = "alice" AND tags.purpose = "candidate"`) and confirm it returns exactly the one real run
satisfying both conditions — the real, precise kind of multi-criteria search tags are built to support.
