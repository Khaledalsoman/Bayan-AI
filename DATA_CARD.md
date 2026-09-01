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
| **SHA-256 (sample CSV)** | N/A — dataset loaded at runtime from course URL; no local file stored |

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
| train | *(add count)* | Used for fine-tuning |
| validation | *(add count)* | Hyper-parameter tuning |
| test | **frozen** | Evaluation only — never touched during training |

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
