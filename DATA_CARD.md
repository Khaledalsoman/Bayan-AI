# DATA_CARD.md — Bayan Project

## Dataset Identity

| Field | Value |
|---|---|
| **Name** | Bayan Synthetic Feedback Dataset |
| **Version** | 1.0 |
| **Source** | Course-provided synthetic data — SDA-AIE-211 |
| **Owner** | ميعاد المري — Meaad Al-Marri / SDAIA Academy |
| **License** | Course use only — educational, non-commercial |
| **Download date** | 2026-09-01 |
| **SHA-256 (classification CSV)** | `c50de92fdab1aa36b19cf4c0f6e31c0bc521f70d6690635e839d7ba9ec7e9a77` |
| **SHA-256 (NER JSONL)** | `ab413f0941656abf6f31ac16122abcb437d4c8c56b7cf820624e6a367bd4336e` |
| **SHA-256 (QA JSON)** | `4e894757b74d09df9e91140dd78ba6e0fcf8cffd052e2ab5702a7487a3e46f2f` |

---

## Description / الوصف

Synthetic bilingual (Arabic/English) citizen-feedback texts created specifically for the SDA-AIE-211 course. Data does **not** contain real names, ID numbers, phone numbers, personal emails, real complaints, or any organisational secrets.

---

## Fields / الحقول

| Column | Type | Description |
|---|---|---|
| `id` | string | Unique record identifier |
| `text` | string | Raw feedback text (AR or EN) |
| `language` | string | `ar` or `en` |
| `topic_label` | string | Topic category (synthetic) |
| `sentiment_label` | string | `positive` / `negative` / `neutral` |
| `split` | string | `train` / `validation` / `test` |

---

## Splits / التقسيم

| Split | Records | Notes |
|---|---|---|
| train | 24 | Used for fine-tuning |
| validation | 8 | Hyper-parameter tuning |
| test | 8 | **frozen** | Evaluation only — never touched during training |

> ⚠️ The test split is frozen. It was not used during any training or threshold tuning step.

---

## Privacy & Ethics / الخصوصية والأخلاقيات

- All data is **synthetic** — generated for course learning, not collected from real users
- No PII (names, IDs, phones, emails) present in raw data
- PII masking (`mask_pii()`) is applied as an additional safeguard at inference time
- Data is **not** uploaded to any external service beyond the course GitHub repo

---

## Limitations / القيود

- Small size — not representative of real-world distribution
- Synthetic generation may not capture real dialect variation
- Arabic coverage limited to MSA with limited dialect samples
- Labels are course fixtures (`COURSE_FIXTURE`) — not from real annotation campaigns

---

## Source URLs

| File | URL |
|---|---|
| Classification CSV | `https://raw.githubusercontent.com/almiyead-rgb/bayan-applied-nlp-course/main/data/sample/bayan_day2_classification.csv` |
| NER JSONL | `https://raw.githubusercontent.com/almiyead-rgb/bayan-applied-nlp-course/main/data/sample/bayan_day2_ner.jsonl` |
| QA JSON | `https://raw.githubusercontent.com/almiyead-rgb/bayan-applied-nlp-course/main/data/sample/bayan_day2_qa.json` |

SHA-256 hashes computed on 2026-09-01 via `hashlib.sha256(data).hexdigest()` in Python.
