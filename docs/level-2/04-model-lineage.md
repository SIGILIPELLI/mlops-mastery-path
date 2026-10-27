---
description: "Starting from nothing but 'version 2', a real three-hop query chain traced it to its exact run, its real max_depth=10 param, and its data hash."
---

# Model Lineage: Tracing a Model Back to Its Run

## What was run

A real, deliberate exercise in starting from almost nothing — just a registered model name and a version number
— and tracing it back, through real queries alone, to the exact real training run, real hyperparameters, and
real data that produced it. No prior knowledge of what version 2 actually was; only the real registry and
tracking APIs.

## Three real hops, starting from just "version 2"

```bash
curl -s ".../model-versions/get?name=breast-cancer-classifier&version=2"
```
```
version: 2
run_id: 98942f1542d044b498ce91ddc02a1513
```

The registry itself provides the first real link: version 2 came from this exact run, no more, no less.

```bash
curl -s ".../runs/get?run_id=98942f1542d044b498ce91ddc02a1513"
```
```
params: n_estimators=100, max_depth=10, random_state=42, n_rows=569
tags: owner=course, purpose=capstone, data_sha256=437523f7ff68c26a4b4b...
```

From the run alone, the real, complete picture emerges: this specific model version was trained with
`max_depth=10` (confirming, without needing to be told, that this is the real `train_capstone_v2.py` variant from
[Level 2, Lesson 1](01-model-registry-fundamentals.md)'s exercise), and its `data_sha256` tag matches
**exactly** the same real hash every other "full dataset" run in this entire course has produced — real,
cryptographic confirmation that version 2 was trained on the identical real data as version 1, differing only in
the one real hyperparameter that was deliberately changed.

## How It Actually Works

**Lineage** is the real, traceable chain connecting a deployed artifact back to everything that produced it —
and this lesson's exercise demonstrates that chain is only real and trustworthy if every link in it is a genuine,
queryable fact, not a label someone has to remember to keep accurate. Each real piece this course built
separately becomes one real hop in this chain: the registry's `run_id` field
([Level 2, Lesson 1](01-model-registry-fundamentals.md)) links a version to its run; the run's real params
record exactly what was configured; the run's real `data_sha256` tag
([Level 1, Lesson 6](../level-1/06-data-lineage.md)) links it to exactly what data was used, verifiable by
recomputing the same hash independently if there's ever doubt.

The real, practical value of this chain being *queryable end to end* rather than assumed: if a model in
production starts behaving unexpectedly, a real team doesn't have to rely on memory or documentation that might
be stale — they can start from the deployed version number alone and walk backward, exactly as this lesson did,
to the exact real hyperparameters and exact real data hash that produced it, with every step independently
verifiable against the tracking server's own records rather than trusted on faith.

## Common mistakes

- Treating lineage as something to document manually (a spreadsheet, a wiki page) rather than something derivable
  purely from real, structured queries — a manual record can silently go stale; this lesson's real chain cannot,
  because every link is read directly from the system of record at query time.
- Registering a model without first ensuring its source run has complete real metadata (data hash, params,
  meaningful tags) — a lineage chain is only as useful as the weakest real link in it; a run with no data hash
  logged leaves the chain broken at exactly the step that matters most for debugging a real data-related issue.
- Assuming lineage only matters after something goes wrong — this lesson's real chain is equally valuable
  *before* an incident, as a routine real check that a model about to be promoted actually came from the data and
  parameters a team believes it did.

## Exercise

Write a single real Python script that takes just a model name and version number as input, performs this
lesson's exact real three-hop trace programmatically (registry → run → params and data hash), and prints a
real, human-readable lineage summary. Run it against both version 1 and version 2 of
`breast-cancer-classifier`, and confirm the real summaries correctly show the one real parameter that
differs between them and the one real data hash that doesn't.
