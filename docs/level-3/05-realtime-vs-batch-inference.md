---
description: "The identical champion model, scoring the identical 114 rows, took 0.23ms/row in batch versus 13.84ms/request over real HTTP -- a measured 60x difference."
---

# Real-Time vs Batch Inference: Two Deployment Shapes

## What was run

The exact same real `champion` model, scoring the exact same real 114-row test set, two real different ways:
once as an in-process batch call over the whole set at once, and once as 114 separate real HTTP requests against
a real running FastAPI server — timing both, to see what the real difference actually is, not assume it.

## Real batch inference: one call, the whole set

```python
model = mlflow.pyfunc.load_model(f"models:/{MODEL_NAME}@champion")
start = time.perf_counter()
preds = model.predict(X_test)
elapsed = time.perf_counter() - start
```
```bash
python3 batch_score.py
```
```
BATCH: scored 114 rows in 25.76ms (0.2260ms/row)
```

## Real-time inference: one real HTTP round trip per row

```python
for _, row in X_test.iterrows():
    resp = requests.post("http://127.0.0.1:5700/predict", json={"values": row.tolist()})
```
```bash
python3 realtime_client.py
```
```
REAL-TIME: 114 individual HTTP requests in 1582.42ms total (13.8410ms/request average)
REAL-TIME: slowest request=65.6537ms fastest request=12.9443ms
```

A real, measured ~60x difference per row — not a theoretical estimate. The model itself does the identical real
computation both times; the real gap is everything *around* it: HTTP connection and serialization overhead,
paid once per row in the real-time case, paid once total in the batch case.

## How It Actually Works

Batch inference amortizes every real fixed cost — loading the model, setting up the HTTP connection, JSON
serialization — across however many rows it scores in one call, which is why this lesson's real batch run
scored 114 rows almost as fast as a single real-time request. Real-time inference pays a full round trip for
every single row, because each request in this lesson's client is a genuinely separate real network call, with
its own real connection and its own real serialization overhead, even though the actual model computation inside
each one is tiny.

The real, practical choice between the two isn't about which is "faster" in the abstract — it's about what the
real caller actually needs. A nightly scoring job over a real database table of a million rows has no reason to
pay per-row HTTP overhead a million times, and should look like this lesson's `batch_score.py`. A real product
feature that needs a prediction the instant one specific user submits one specific form has no rows to batch —
it needs this lesson's `realtime_server.py`, and has to accept the real per-request overhead that comes with it.

## Common mistakes

- Building a real-time serving endpoint for a workload that's actually run on a schedule over a known, bounded
  set of rows — this lesson's real numbers show exactly what that costs: roughly 60x the per-row time, for no
  real benefit, since nothing in a scheduled batch job needs the sub-request latency a real-time endpoint exists
  to provide.
- Batch-scoring a workload that actually needs an answer the moment one new row shows up — the inverse mistake:
  waiting to accumulate a batch when the real caller is a single waiting user gets the opposite problem, adding
  real, unnecessary latency to a request that should have been answered immediately.
- Assuming the overhead difference without measuring it for your own real model and real network — this lesson's
  specific ~60x figure is a property of this exact model, this exact dataset, and `localhost` network
  conditions; a larger model or a real production network would almost certainly produce different real numbers,
  worth measuring the same way rather than assumed.

## Exercise

Modify `realtime_server.py` to accept a *list* of rows in one request instead of one, and modify
`realtime_client.py` to send all 114 real rows in a single real HTTP call instead of 114 separate ones. Measure
the real resulting latency and compare it against both this lesson's pure batch number and its pure per-row
real-time number — confirming where "batched real-time" actually lands between the two.
