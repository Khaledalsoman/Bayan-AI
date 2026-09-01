# 🌟 Bayan — Bilingual Citizen-Feedback Analysis System
## بيان — نظام تحليل ملاحظات المستفيدين ثنائي اللغة

> Developed as part of **SDA-AIE-211 — Natural Language Processing with Transformers**  
> Instructor: **ميعاد المري — Meaad Al-Marri** | [SDAIA Academy](https://github.com/SDAIAAcademy)

---

## What is Bayan? / ما هو بيان؟

Bayan is a bilingual (Arabic/English) NLP pipeline for analysing short citizen-feedback texts. It accepts a raw text input, protects privacy, then runs topic classification, sentiment analysis, named-entity recognition, extractive QA, and semantic search — all served through a tested FastAPI endpoint.

The project is **educational** and uses **synthetic data only**. It does not represent a real government system and does not process real personal data.

---

## Architecture / المعمارية

```
Input Text (AR / EN)
       │
       ▼
┌─────────────────────┐
│  1. Preprocessing   │  PII masking · Unicode norm · Arabic profile · Tokenisation
└─────────────────────┘
       │
       ▼
┌─────────────────────┐
│  2. Task Models     │  Topic · Sentiment · NER · Extractive QA
└─────────────────────┘
       │
       ▼
┌─────────────────────┐
│  3. Semantic Search │  Sentence embeddings · FAISS · CrossEncoder re-rank
└─────────────────────┘
       │
       ▼
┌─────────────────────┐
│  4. Evaluation      │  Metrics · Slices · Bootstrap CI · Error analysis
└─────────────────────┘
       │
       ▼
┌─────────────────────┐
│  5. Serving         │  ONNX INT8 · FastAPI /health + /v1/classify
└─────────────────────┘
```

---

## Requirements / المتطلبات

- Python 3.11+
- Google Colab Free (recommended) or local environment with GPU optional

Install all dependencies (handled automatically by the notebook's first cell):

```
transformers==5.15.1    tokenizers==0.22.2      spacy==3.8.7
scikit-learn==1.9.0     camel-tools==1.6.0      sentence-transformers==6.0.0
faiss-cpu==1.15.0       onnx==1.22.0            onnxruntime==1.29.0
fastapi==0.141.1        httpx2==2.12.0          psutil==7.2.2
```

---

## How to Run / طريقة التشغيل

### Option 1 — Google Colab (Recommended)
1. Open `bayan_capstone.ipynb` in Google Colab
2. **Runtime → Run all**
3. The unified install cell installs all packages automatically
4. All checkpoints print `=PASS` when successful

### Option 2 — Local
```bash
git clone https://github.com/[YOUR-USERNAME]/Bayan-AI.git
cd Bayan-AI
pip install -r requirements-day4.txt
jupyter notebook notebooks/bayan_capstone.ipynb
```

---

## Results Summary / ملخص النتائج

| Task | Model | Metric | Value |
|---|---|---|---|
| Topic Classification | DistilmBERT fine-tuned | Macro-F1 | **0.867** |
| Sentiment Analysis | DistilmBERT fine-tuned | Macro-F1 | N/A (topic head only) |
| NER | DistilmBERT fine-tuned | Entity-F1 | **0.571** |
| Extractive QA | DistilmBERT fine-tuned | No-answer accuracy | **1.000** (threshold=0.459) |
| Semantic Search | MiniLM-L12-v2 + FAISS | Recall@3 | **1.000** (MRR@3=0.722) |
| Inference (ONNX INT8) | Quantised model | p99 latency | **~2ms** (p95=1.7ms) |

> Results are `MEASURED_SMOKE` on synthetic course data — see `EVALUATION_REPORT.md`

---

## Repository Structure / هيكل المستودع

```
Bayan-AI/
├── bayan_capstone.ipynb          # Main unified notebook (T1-T12)
├── reports/
│   ├── t12_extension.json        # T12 zero-shot showdown report
│   └── benchmark_results.json    # ONNX latency benchmarks
├── README.md
├── DATA_CARD.md
├── MODEL_CARD.md
├── EVALUATION_REPORT.md
├── BENCHMARKS.md
├── DECISIONS.md
├── PROGRESS.md
├── PROJECT_SUMMARY.json
├── SUBMISSION.yml
├── requirements-day1.txt
├── requirements-day2.txt
├── requirements-day3.txt
└── requirements-day4.txt
```

---

## Limitations / القيود

- Models are fine-tuned on small synthetic datasets (`MEASURED_SMOKE`) — not production-ready
- Arabic NLP uses `conservative` CAMeL Tools profile; dialect coverage is limited
- FAISS index is flat (`IndexFlatIP`) — not optimised for large-scale retrieval
- ONNX INT8 quantisation tested on CPU only; GPU path not validated
- No real personal data was used or stored at any stage

---

## Acknowledgements / الشكر والتقدير

This project was developed as part of the **SDA-AIE-211 — Natural Language Processing with Transformers** programme, prepared and delivered by **ميعاد المري (Meaad Al-Marri)**, under [SDAIA Academy](https://github.com/SDAIAAcademy).

---

## Student Declaration / إقرار المتدرب

نُفِّذ هذا المشروع ضمن برنامج SDA-AIE-211 — Natural Language Processing with Transformers، بإعداد وتقديم المدربة ميعاد المري، ضمن برامج أكاديمية سدايا.

**Student / المتدرب:** خالد علي القحطاني)*  
**Repository:** https://github.com/Khaledalsoman/Bayan-AI  
**Submission Tag:** `submission-v1.0`
