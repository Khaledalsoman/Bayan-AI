# MODEL_CARD.md — Bayan Project

## Models Used / النماذج المستخدمة

---

### 1. Classification & NER & QA — DistilmBERT

| Field | Value |
|---|---|
| **Base model** | `distilbert/distilbert-base-multilingual-cased` |
| **Source** | Hugging Face Hub |
| **License** | Apache 2.0 |
| **Tasks** | Topic classification · Sentiment · NER · Extractive QA |
| **Fine-tuning data** | Bayan synthetic dataset (course fixture) |
| **Training environment** | Google Colab Free (CPU fallback — encoder frozen, task head trained) |

#### Performance (MEASURED_SMOKE on synthetic test split)

| Task | Metric | Value |
|---|---|---|
| Topic Classification | Macro-F1 | 0.867 |
| Topic Classification | Accuracy | 0.875 |
| Topic vs TF-IDF baseline | Δ Macro-F1 | +0.200 (+20 points) |
| NER | Entity-level F1 | 0.571 |
| NER | Precision | 0.667 |
| NER | Recall | 0.500 |
| Extractive QA | No-answer accuracy | 1.000 (threshold=0.459) |

#### Limitations
- Fine-tuned on small synthetic data — do not deploy in production
- CPU fallback freezes most of the encoder; only the task head is updated
- Arabic dialectal text may perform worse than MSA (Gulf dialect gap observed in behavioural tests)

---

### 2. Semantic Search — paraphrase-multilingual-MiniLM-L12-v2

| Field | Value |
|---|---|
| **Model** | `sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2` |
| **Source** | Hugging Face Hub / sentence-transformers |
| **License** | Apache 2.0 |
| **Task** | Sentence embeddings for semantic search |
| **Index** | FAISS `IndexFlatIP` with L2 normalisation |
| **Re-ranker** | `cross-encoder/mmarco-mMiniLMv2-L12-H384-v1` |

#### Performance

| Metric | Value |
|---|---|
| Recall@3 | 1.000 |
| MRR@3 (before reranking) | 0.667 |
| MRR@3 (after reranking) | 0.722 |
| Reranking delta | +0.056 |

---

### 3. Optimised Inference — ONNX + INT8

| Field | Value |
|---|---|
| **Base** | DistilmBERT (classification head) |
| **Export** | PyTorch → ONNX FP32 → Dynamic INT8 |
| **Runtime** | ONNX Runtime 1.29.0 |
| **Role** | `SYSTEMS_SMOKE` — latency benchmark only |

#### Benchmark (CPU, Google Colab, warmup=5, repetitions=30)

| Metric | FP32 (PyTorch) | ONNX FP32 | ONNX INT8 |
|---|---|---|---|
| p95 latency | 9.9 ms | 6.4 ms | 1.7 ms |
| Model size | 16.73 MB | 16.79 MB | 4.27 MB |
| Prediction agreement | — | 1.000 | 1.000 |

---

## T12 — Measured Extension: Zero-Shot Showdown

| Metric | Zero-Shot BART | Fine-Tuned DistilmBERT |
|---|---|---|
| Macro-F1 | 0.643 | **0.867** |
| Accuracy | 0.625 | **0.875** |
| p50 latency | 5,448 ms | < 10 ms |
| Setup cost | Zero (no training) | ~12 epochs CPU |

**Decision: ADOPT fine-tuned** — Δ macro-F1 = +0.224 (+22.4 points). Fine-tuned model is both more accurate and 500× faster at inference.

---

## Intended Use / الاستخدام المقصود

- **Intended:** Educational demonstration of bilingual NLP pipeline components
- **Not intended:** Production deployment, real government systems, real citizen data

## Out-of-scope Use / الاستخدام غير المناسب

- Processing real personal data or real complaints
- Making automated decisions about real individuals
- Any use outside the course educational context
