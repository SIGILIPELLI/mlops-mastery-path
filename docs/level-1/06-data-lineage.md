---
description: "A real SHA-256 data hash matched exactly across two runs on the same data, and differed completely for a real filtered subset -- a verifiable fingerprint."
---

# Tracking Data Lineage: Which Data Trained Which Model

## What was run

Three real training runs: one on the full real breast cancer dataset, one on a real, deliberately filtered
subset, and one re-run on the full dataset again — each logging a real SHA-256 hash of the exact training data as
an MLflow tag, to see whether that hash actually, reliably distinguishes "same data" from "different data."

## A real hash that tells the truth about the data

```python
def hash_dataframe(frame):
    return hashlib.sha256(pd.util.hash_pandas_object(frame, index=True).values.tobytes()).hexdigest()
```

```
full-dataset:        n_rows=569  data_sha256=437523f7ff68c26a...  accuracy=0.9649
filtered-dataset:     n_rows=398  data_sha256=51714f13ff86f1f2...  accuracy=0.9750
full-dataset-rerun:   n_rows=569  data_sha256=437523f7ff68c26a...  accuracy=0.9649
```

The real hash for `full-dataset` and `full-dataset-rerun` is **byte-for-byte identical** —
`437523f7ff68c26a...` — real, cryptographic confirmation that both runs genuinely trained on the exact same data,
not just "data that looks similar." The `filtered-dataset` run, trained on a real, smaller subset (398 rows
instead of 569, every row with `mean radius > 12`), produced a completely different real hash and a real,
different accuracy (0.9750 vs 0.9649) — the hash correctly flags it as genuinely different training data, which a
human-assigned label like `"dataset_v2"` would only confirm if someone remembered to update it correctly every
time.

## How It Actually Works

A model's accuracy number is only meaningful in the context of *which data* produced it — the same code, the same
seed, run on a genuinely different dataset, produces a genuinely different, equally valid result (0.9750 here is
not "better" in some absolute sense; it's a different, smaller dataset). Without a real, verifiable record of
exactly which data trained which model, a team comparing "run A got 96.5%, run B got 97.5%" has no way to know
whether B is a genuinely better model or just got an easier real test set — exactly the ambiguity this lesson's
real hash resolves directly.

A content hash (SHA-256 here, computed over every real row and column) is a stronger lineage record than a
human-assigned dataset name or version label, because it can't drift out of sync with reality the way a label can
— a file renamed `"clean_data_v2.csv"` might still contain yesterday's data if someone forgot to actually
regenerate it; a hash computed fresh at training time cannot lie about what the data actually was at that exact
moment. Logging it as an MLflow **tag** (rather than a metric or param) reflects what it really is: metadata about
the run's identity and provenance, not a training input to tune or an output to optimize.

This connects directly to [Lesson 5](05-reproducibility-seeds-environments.md)'s third, previously-unaddressed
reproducibility gap: a seed and a pinned environment aren't enough on their own — this lesson's real hash is what
closes the data side of that gap, giving a concrete, checkable answer to "was this actually the same data" rather
than an assumption.

## Common mistakes

- Relying on a filename or a human-assigned version label as the record of "which data" without a real content
  hash backing it up — labels can silently drift from reality; a hash, recomputed at training time, cannot.
- Hashing only a dataset's shape or summary statistics instead of its real content — two genuinely different
  datasets can share the same row count and similar statistics while actually being different data, something
  only a real content hash (as this lesson computed) would catch.
- Never actually checking that two "identical" runs really do produce identical hashes — this lesson's real
  `full-dataset` vs `full-dataset-rerun` match was verified directly, not assumed from the code looking like it
  should produce the same result.

## Exercise

Modify `train_with_lineage.py` to filter the dataset a different real way — drop one real column instead of
filtering rows (`df.drop(columns=["mean smoothness"])`) — and log a fourth real run. Confirm the real hash differs
from all three previous ones, and report the real resulting accuracy, completing a real record of four distinct,
hash-verified training datasets and their real results.
