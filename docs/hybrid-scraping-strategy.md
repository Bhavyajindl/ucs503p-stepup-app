# StepUp — Activity and Integrity Verification Strategy

> This document replaces the unrelated "hybrid scraping" concept with a strategy appropriate to the StepUp project.

## 1. Purpose

StepUp needs to distinguish genuine activity from suspicious submissions while keeping the verification process auditable.

The current implementation separates **activity synchronisation** from **integrity classification**.

## 2. Current Verification Pipeline

```text
Activity Source / Client
        |
        v
POST /activity/sync
        |
        v
Validate + Deduplicate
        |
        v
ActivityRecord
        |
        v
POST /activity/integrity
        |
        v
IntegrityResult
        |
        v
isVerified update
```

## 3. Duplicate Protection

Each activity submission carries a client-supplied `syncId`.

The backend checks whether that identifier already exists.

```text
syncId exists?
   |
   +-- YES --> return existing record / duplicate
   |
   +-- NO ---> create new ActivityRecord
```

This protects against duplicate rows caused by client retries.

## 4. Integrity Classes

The current data model supports six classifications:

| Classification | Meaning |
|---|---|
| Genuine Walking | Activity is classified as normal walking |
| Genuine Running | Activity is classified as normal running |
| Phone Shaking | Suspicious movement associated with phone shaking |
| Vehicle Movement | Suspicious movement associated with vehicle travel |
| Duplicate | Submission is classified as duplicate |
| Unknown | Activity cannot be assigned to another class |

## 5. Confidence and Verification

Each integrity result can store:

- classification
- confidence
- verified steps
- suspicious steps

The two genuine activity classes set:

```text
isVerified = true
```

Other classes set:

```text
isVerified = false
```

## 6. Important Current Implementation Boundary

The integrity classification is currently computed by the **calling client** and submitted to the backend.

The current backend does **not** run a server-side model over historical activity patterns.

Therefore:

```text
Current:
Client classification → Backend storage → Verification flag

Future:
Activity data → Server-side / second-opinion detector → Verification
```

## 7. Evaluation Strategy

The `EvaluationMetric` table is designed to store detector-quality metrics such as:

- Precision
- Recall
- F1
- False Positive Rate
- Latency
- Model size
- Battery cost

The report's current evaluation rows are explicitly development/demo data and are **not genuine measurements**.

The seeded demo row labelled **Proposed ML detector** contains:

| Detector | Precision | Recall | F1 | FPR |
|---|---:|---:|---:|---:|
| Proposed ML detector | 0.91 | 0.89 | 0.90 | 0.09 |

These values should be treated as simulated development data, not as measured pilot performance.

## 8. Future Improvement

A future version can introduce server-side integrity scoring or a second-opinion detector while retaining the existing `ActivityRecord` and `IntegrityResult` model.

A real pilot should evaluate the detector using collected, labelled activity data rather than seeded records.
