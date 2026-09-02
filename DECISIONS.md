# DECISIONS.md — Bayan Project

A log of key technical decisions made during the project, with evidence and rationale.

---

## D1 — Tokeniser Choice

**Decision:** Use `distilbert/distilbert-base-multilingual-cased` tokeniser (WordPiece, 119k vocab)

**Evidence:**
- Fertility measured on bilingual corpus: AR fertility ≈ 1.5–2.0 tokens/word, EN fertility ≈ 1.0–1.17 tokens/word (mean fertility across samples: 1.36)
- Truncation rate at `max_length=10` smoke test: 0%; at `max_length=96` on real workload: 0% (p95 tokens = 28.95, max = 30)
- Compared against local WordPiece baseline (Section 1 of notebook)

**Rationale:** mBERT-family tokenisers handle Arabic script natively with reasonable fertility. The multilingual vocab covers both AR and EN without language-specific tokenisers.


**Why WordPiece splits Arabic the way it does:**
Arabic is a morphologically rich, agglutinative language — a single word can encode a verb, subject, object, and preposition that English expresses as 4–5 separate tokens. WordPiece was trained primarily on English Wikipedia, so it lacks many Arabic root patterns in its 119k vocabulary. When the tokeniser encounters an unseen Arabic word it falls back to character-level `##` subwords, producing fertility of 1.5–2.0 tokens/word vs 1.0–1.17 for English. Arabic diacritics and tatweel (ـ) generate extra tokens if not pre-removed — which is why `normalise_arabic_record()` strips them before tokenisation (Section 1, notebook).

**Trade-off:** Higher fertility on Arabic than English; longer texts may be truncated.

---

## D2 — Model Selection (Classification / NER / QA)

**Decision:** `distilbert/distilbert-base-multilingual-cased` fine-tuned per task

**Evidence:** DistilmBERT is 40% smaller than mBERT with ~97% of its performance on most benchmarks. Fits in Colab Free memory. Topic classification achieved macro-F1 = 0.867 vs TF-IDF baseline of 0.667 (+20 points).

**Rationale:** Balances multilingual coverage, compute budget, and task performance.

**Trade-off:** Less accurate than full mBERT or AraBERT on Arabic-heavy tasks.

---

## D3 — Arabic Normalisation Profile

**Decision:** `conservative` profile for train/eval/serve; `search` profile for semantic search queries

**Evidence:** Golden tests in Section 5 show conservative profile preserves disambiguation-critical characters. Search profile improves recall by normalising alef variants.

**Rationale:** Two-profile strategy — strict normalisation for model inputs, relaxed for retrieval.

---

## D4 — FAISS Index Type

**Decision:** `IndexFlatIP` (exact inner-product search) with L2-normalised embeddings

**Evidence:** Corpus size ~40 records (course fixture) — exact search feasible; approximate methods not needed at this scale. Recall@3 = 1.000 confirmed on full test set.

**Rationale:** Guarantees exact Recall@10; no approximation error on small corpus.

**Trade-off:** Does not scale to millions of vectors without switching to IVF/HNSW.

---

## D5 — No-Answer Threshold (QA)

**Decision:** Threshold set to 0.459 (tuned on validation set)

**Evidence:** Sweep over thresholds [0.0, 0.5] on validation; 0.459 maximised no-answer accuracy to 1.000 on validation set. Frozen threshold applied to test set: no-answer accuracy = 1.000.

---

## D6 — Quantisation Decision

**Decision:** Deploy ONNX INT8 — see `BENCHMARKS.md`

**Evidence:** p99 latency INT8 ≈ 2ms vs FP32 ≈ 11ms; quality delta = 0.000 F1 points (100% prediction agreement). Model size reduced from 16.79 MB to 4.27 MB (75% reduction).

**Rationale:** INT8 meets the 40ms p99 target with zero measured quality loss on the smoke workload. The 4× size reduction and 5× latency improvement justify adoption for CPU-constrained serving. Decision is labelled SYSTEMS_SMOKE — not a production ship decision.
