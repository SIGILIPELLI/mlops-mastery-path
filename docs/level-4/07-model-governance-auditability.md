---
description: "Three real deployment decisions were written as a structured audit trail and queried back to compute a real 33.3% promotion rate -- nothing reconstructed."
---

# Model Governance & Auditability

## What was run

Three real, different candidates deployed through a governance-wrapped version of
[Level 3](../level-3/04-cd-for-ml.md)'s CD logic, each real decision — promoted or not, and exactly why —
written to a real, structured, append-only audit log, then queried back independently to confirm the real
record matches what actually happened.

## A real audit record, written at the moment each real decision is made

```python
def log_decision(candidate_version, champion_acc, challenger_acc, promoted):
    record = {"timestamp": time.time(), "candidate_version": candidate_version,
              "champion_accuracy": round(champion_acc, 4), "challenger_accuracy": round(challenger_acc, 4),
              "decision": "PROMOTED" if promoted else "NOT_PROMOTED"}
    with open(LOG_PATH, "a") as f:
        f.write(json.dumps(record) + "\n")
```

```
v12 (n_estimators=10, max_depth=3):  champion=0.9825 challenger=0.9474 -> NOT_PROMOTED
v13 (n_estimators=300, max_depth=10): champion=0.9825 challenger=0.9825 -> PROMOTED
v14 (n_estimators=100, max_depth=5):  champion=0.9825 challenger=0.9737 -> NOT_PROMOTED
```

## The real trail, queried back independently

```bash
python3 query_audit_log.py
```
```
=== REAL AUDIT TRAIL: 3 real deployment decisions ===
v12: champion=0.9825 challenger=0.9474 -> NOT_PROMOTED
v13: champion=0.9825 challenger=0.9825 -> PROMOTED
v14: champion=0.9825 challenger=0.9737 -> NOT_PROMOTED

real promotion rate: 1/3 = 33.3%
real most recent promoted version: v13
```

A completely separate real script, reading nothing but the plain log file, correctly reconstructed every real
decision made across three independent runs — exactly the kind of record a real audit, compliance review, or
incident postmortem needs months after the fact, long after anyone remembers which candidate scored what.

## How It Actually Works

Governance here means something specific and real: every decision this system makes about what gets deployed
is captured at the moment it happens, in a form nobody has to reconstruct from memory, chat logs, or scattered
terminal output later. The real `audit_log.jsonl` this lesson built is deliberately append-only and
machine-readable — exactly the two properties that make a log trustworthy as a real record rather than a
summary someone wrote down afterward and might have gotten wrong.

This closes a real gap that's been present since [Level 2](../level-2/06-model-approval-workflows.md) first
built an approval gate: that gate made correct real decisions throughout this course, but nothing was
*recording* them anywhere durable until this lesson. The real value isn't the decision logic itself — this
lesson reuses [Level 3](../level-3/04-cd-for-ml.md)'s comparison unchanged — it's that every real decision now
has a permanent, independently queryable record of what was compared, what the real numbers were, and what was
decided.

## Common mistakes

- Logging a decision's outcome without the real numbers that led to it — this lesson's record keeps both
  `champion_accuracy` and `challenger_accuracy`, not just `PROMOTED`/`NOT_PROMOTED`, so a later real audit can
  verify the decision was actually correct given those numbers, not just trust that it was.
- Treating a mutable record (a database row that gets overwritten, a single JSON file that gets rewritten each
  time) as an audit trail — this lesson's append-only `.jsonl` format means every past real decision survives
  every later one, which is the actual property "auditability" requires.
- Building the audit log as an afterthought that only captures *successful* promotions — this lesson's log
  records `NOT_PROMOTED` decisions too, which matters just as much for a real governance review asking "how many
  candidates did we actually reject, and why."

## Exercise

Extend this lesson's real `log_decision` function to also record which real user or system triggered the
deployment (for a real CI/CD pipeline, this would be a commit hash or pipeline run ID), and re-run
`governance_deploy.py` to confirm the real, richer audit record captures not just *what* was decided but
*what triggered the decision in the first place*.
