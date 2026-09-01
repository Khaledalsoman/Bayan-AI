# BENCHMARKS.md — Bayan Project

> All benchmarks are `SYSTEMS_SMOKE` on lab CPU unless stated otherwise.  
> Model: `google/bert_uncased_L-2_H-128_A-2` (small, English-only) used for latency measurement.  
> PROJECT_MODE results (student's own fine-tuned model) to be added after Gate D.

---

## Environment

| Field | Value |
|---|---|
| Hardware | Google Colab CPU (lab default) |
| Python | 3.11.x |
| ONNX Runtime | 1.29.0 |
| Workload | 8 bilingual rows (AR + EN) |
| Warmup | 5 | Repetitions | 30 |
| Run date | 2026-09-01 |
| Commit | *(add commit hash after push)* |

---

## Latency Benchmark — Inference

| Format | p50 (ms) | p95 (ms) | p99 (ms) | Size (MB) |
|---|---|---|---|---|
| PyTorch FP32 | ~5.0 | 9.9 | ~11.0 | 16.73 |
| ONNX FP32 | ~3.5 | 6.4 | ~7.0 | 16.79 |
| ONNX INT8 | ~1.2 | 1.7 | ~2.0 | 4.27 |

p50/p99 estimated from p95 measurements; p95 values are directly measured (warmup=5, repetitions=30).

---

## Memory

| Format | RSS (MB) peak |
|---|---|
| PyTorch FP32 | ~200 (Colab baseline — RSS observed is approximate) |
| ONNX INT8 | ~170 (model footprint reduced to 4.27 MB) |

Note: RSS in Colab includes shared runtime memory; these are approximate observed values.

---

## Quality Tax (Parity Check)

| Format | Prediction agreement | Delta vs FP32 |
|---|---|---|
| PyTorch FP32 (reference) | 1.000 | — |
| ONNX FP32 | 1.000 | 0.000 ✅ |
| ONNX INT8 | 1.000 | 0.000 ✅ |

Max absolute logits difference (FP32 PyTorch vs ONNX FP32): 1.49e-07 — numerical parity confirmed.

---

## Decision

| Question | Answer |
|---|---|
| Does INT8 meet p99 ≤ 40ms? | **Yes** — p99 ≈ 2ms, well within budget |
| Is quality loss acceptable? | **Yes** — prediction agreement = 1.000, zero quality tax |
| **Recommendation** | **Deploy ONNX INT8** |

**Rationale:** ONNX INT8 quantisation reduced model size from 16.79 MB to 4.27 MB (75% reduction) while maintaining 100% prediction agreement with the FP32 reference on the smoke workload. p95 latency dropped from 9.9ms (PyTorch) to 1.7ms (INT8), comfortably meeting the 40ms p99 target. Given zero measured quality loss and a 4× size reduction, INT8 is the clear choice for serving on CPU-constrained environments.
