# EVALUATION_REPORT.md — Bayan Project

> **Evidence boundary:** All results below are `MEASURED_SMOKE` on synthetic course-fixture data.  
> Results are from the student's own notebook run — not copied from reference outputs.  
> Environment, date, and commit are recorded for each result.

---

## Environment

| Field | Value |
|---|---|
| Platform | Google Colab Free |
| Python | 3.11.x |
| Run date | 2026-09-01 |
| Notebook commit | `044353e082f8118438429234cf49ab49594261b7` |
| Data kind | `COURSE_FIXTURE` — synthetic, bilingual |

---

## 1. Text Classification (T4 / R3)

### TF-IDF Baseline (LinearSVC)

| Metric | Topic | Sentiment |
|---|---|---|
| Macro-F1 | 0.667 | N/A (topic head only) |
| Accuracy | 0.625 | N/A |

### DistilmBERT Fine-tuned

| Metric | Topic | Sentiment |
|---|---|---|
| Macro-F1 | 0.867 | N/A (topic head only) |
| Improvement over baseline | +0.200 (+20.0 points ✅ ≥ +8 required) | N/A |

---

## 2. NER (T5 / R2)

| Metric | Value |
|---|---|
| Entity-level F1 | 0.571 |
| Precision | 0.667 |
| Recall | 0.500 |

Label alignment strategy: first subword carries the entity label; special tokens get `O`.

Note: NER trained on 12 synthetic examples (MEASURED_SMOKE); score reflects small fixture only.

---

## 3. Extractive QA (T6 / R3)

| Metric | Value |
|---|---|
| No-answer accuracy | 1.000 (threshold = 0.459, frozen on validation) |
| Span extraction accuracy | SMOKE ONLY — single span extracted ("الرياض") |

Constrained span search used; max answer length enforced.

---

## 4. Semantic Search (T8 / R4)

| Metric | Value |
|---|---|
| Recall@3 | 1.000 |
| MRR@3 | 0.667 |

Model: `paraphrase-multilingual-MiniLM-L12-v2` | Index: FAISS `IndexFlatIP`

---

## 5. Bootstrap Confidence Intervals (T9 / R5)

| Task | Macro-F1 | 95% CI lower | 95% CI upper |
|---|---|---|---|
| Topic (ALL, n=36) | 0.782 | 0.611 | 0.896 |
| Arabic slice (n=24) | 0.758 | 0.561 | 0.927 |
| English slice (n=12, SMALL_SLICE) | 0.829 | 0.500 | 1.000 |

Bootstrap samples: 500 | Method: percentile

---

## 6. Sliced Evaluation (T9)

| Slice | Estimate | Flag |
|---|---|---|
| Arabic texts only (n=24) | 0.758 | — |
| English texts only (n=12) | 0.829 | SMALL_SLICE |
| Gulf variant (n=12) | 0.658 | SMALL_SLICE |
| MSA variant (n=12) | — | SMALL_SLICE |

---

## 7. Behavioural Tests (T9 / R5)

| Test type | Cases | Pass rate |
|---|---|---|
| MFT (minimum functionality) | 6 behavioural canary cases | 3/6 = 50% (smoke set — Gulf dialect gap identified) |
| Invariance | Paired B-A comparison | CI includes zero; no directional claim |

---

## 8. Error Analysis (T10 / R6)

Manual review of errors, categorised:

| Error type | Count | Example |
|---|---|---|
| Label ambiguity | 2 | "health" vs "transport" confusion on symptom+mobility text |
| Short/uninformative text | 1 | Underspecified single-word complaint |
| Code-switching (AR+EN) | 1 | Mixed AR/EN Gulf dialect app complaint |
| Other (dialect gap) | 2 | Gulf variant health/transport cases (EV-006, EV-030) |

### Top 3 Ranked Recommendations

1. Collect targeted Gulf-dialect training examples for health and transport categories to close the dialect gap identified in EV-006 and EV-030
2. Add contrastive examples for app/status wording to resolve class confusion around EV-019 and EV-020
3. Add ambiguity detection — abstain or request context when input is underspecified (single-word or very short complaints)
