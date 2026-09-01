# PROGRESS.md — Bayan Project

Day-by-day completion log and recovery points.

---

## Day 1 — Text Processing & Tokenisation

**Status:** ✅ Complete

**Completed:**
- Unicode inspection and normalisation (NFC)
- PII masking: `<EMAIL>` and `<PHONE>` regex patterns
- Two-copy contract: `raw_text` (preserved) + `model_text` (processed)
- Arabic: diacritics removal, alef normalisation, tatweel removal (CAMeL Tools)
- spaCy sentenciser (language-agnostic `xx`)
- Local WordPiece tokeniser implementation
- Subword metrics: token fertility and truncation rate
- HuggingFace AutoTokenizer comparison (mBERT)

**Gate A checkpoint:** `DAY1_NOTEBOOK1_CORE=PASS` | `DAY1_NOTEBOOK2_CORE=PASS`

**Commit:** `feat(preprocessing): add bilingual pipeline and tokenizer decision`

---

## Day 2 — Classification, NER & QA

**Status:** ✅ Complete

**Completed:**
- TF-IDF + LinearSVC baseline (topic + sentiment)
- DistilmBERT fine-tuning: topic classification + sentiment
- MEASURED_SMOKE results recorded
- NER: word-to-subword label alignment (`align_word_labels`)
- BIO entity extraction (`bio_entities`, `entity_report`)
- Extractive QA: constrained span search + no-answer handling (`best_span`)
- Memory management between NER and QA models

**Gate B checkpoint:** `DAY2_NOTEBOOK3_CORE=PASS` | `DAY2_NOTEBOOK4_CORE=PASS`

**Commit:** `feat(tasks): add topic-sentiment, NER and QA evidence`

---

## Day 3 — Arabic NLP, Semantic Search & Evaluation

**Status:** ✅ Complete

**Completed:**
- CAMeL Tools pipeline: `conservative` and `search` profiles
- Golden tests for Arabic normalisation
- Arabizi candidate heuristic
- Sentence embeddings: `paraphrase-multilingual-MiniLM-L12-v2`
- FAISS `IndexFlatIP` index with manifest + SHA256
- CrossEncoder re-ranking
- `no_answer` threshold tuning
- Macro-F1, Bootstrap CI, paired bootstrap difference
- Sliced evaluation with SMALL_SLICE flagging
- Behavioural tests (MFT + invariance)
- Manual error taxonomy (100 errors, 3+ ranked recommendations)

**Gate C checkpoint:** core checks verified

**Commit:** `feat(search-eval): add FAISS search and sliced evaluation`

---

## Day 4 — Optimisation & Serving

**Status:** ✅ Complete

**Completed:**
- ONNX export: PyTorch FP32 → ONNX FP32 → Dynamic INT8
- Latency benchmark: p50/p95/p99 on 16 concurrent requests
- Quality parity check (FP32 vs INT8)
- FastAPI app: `/health` + `/v1/classify` endpoints
- Pydantic `ClassifyRequest` model with validation
- TestClient smoke tests: AR + EN + invalid input + startup canaries
- SYSTEMS_SMOKE results recorded

**Gate D checkpoint:** `SYSTEMS_SMOKE=PASS`

**Commit:** `perf(serving): benchmark and document optimisation decision`

---

## Pre-Submission

**Status:** ✅ Complete

- All documentation files written
- Repository made public
- Verified from private window
- Final commit + `submission-v1.0` tag created

**Commit:** `docs(release): finalize cards, reports and README`  
**Tag:** `submission-v1.0`
