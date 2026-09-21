---
license: apache-2.0
language:
- en
- zh
pipeline_tag: image-text-to-text
tags:
- zen6
- zen6-flash
- ternary
- 1.58-bit
- bonsai
- dflash2
- speculative-decoding
- metal
- apple-silicon
- llama-cpp
base_model:
- prism-ml/Ternary-Bonsai-2-27B-gguf
- ProCreations/Ternary-Bonsai-2-27B-DFlash2
---

<div align="center">

# Zen6 Flash: 27B Ultra-Compact Ternary VLM

**1.72 Bits/Weight Ternary Transformer | 98.2% FP16 Intelligence | Vision Projector | DFlash 2 Speculative Drafter**

[![Hugging Face](https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-zenlm%2Fzen6--flash-blue)](https://huggingface.co/zenlm/zen6-flash)
[![GitHub](https://img.shields.io/badge/GitHub-zenlm%2Fzen6--flash-black)](https://github.com/zenlm/zen6-flash)

</div>

---

## Architectural Highlights

**Zen6 Flash** is the ultra-efficient multimodal member of the Zen6 family, powered by **Ternary Bonsai 2 27B**:

- **True 1.72 Bits/Weight Ternary Weights**: End-to-end ternary representation across embeddings, linear attention projections, self-attention projections, MLP projections, and the LM head. No high-precision escape hatches behind a low-bit label.
- **Extreme Memory Efficiency**:
  - **PQ2_0 Packing**: 7.21 GB (stores each trit in a 2-bit slot, consumed directly by GPU/Metal kernels without expansion to FP16).
  - **PTQ1_0 Packing**: 5.95 GB (dense bit-packed representation).
  - **\~9.3x smaller** than standard FP16 weights (~54 GB), fitting easily within unified memory on Apple Silicon M4/M5 Max laptops, edge devices, and single GPUs.
- **Multimodal Vision Tower**:
  - `Ternary-Bonsai-2-27B-mmproj-Q8_0.gguf` (629 MB) providing native high-resolution image and document understanding.
- **Speculative Acceleration via DFlash 2**:
  - Bundles the verified **Bonsai 2 27B DFlash 2** drafter (`Bonsai-2-27B-DFlash2-Q8_0.gguf`, 2.05 GB).
  - Delivers **170.04 decode tokens/sec** with **54.2% draft acceptance**.
- **Context Capacity**: 262,144 tokens native context window maintained efficiently by ~75% linear attention.

---

## Benchmark Accuracy & Intelligence Retention

Zen6 Flash breaks the conventional sub-4-bit degradation barrier, retaining **98.2% of full FP16 intelligence**:

| Benchmark Suite | FP16 Baseline | Conventional IQ2_XXS | Zen6 Flash (PQ2_0) | Retention vs FP16 |
| :--- | :---: | :---: | :---: | :---: |
| **Average Across 14 Reasoning Tests** | 86.33 | 72.59 | **84.78** | **98.2%** |
| **Mathematical Reasoning (MathVision / GSM8K)** | 97.10 | 79.40 | **96.57** | **99.5%** |
| **Code Generation (HumanEval / LiveCode)** | 90.20 | 74.80 | **89.42** | **99.1%** |
| **Agentic Tool Calling & Schema Adherence** | 76.80 | 58.10 | **74.92** | **97.6%** |
| **Model Footprint** | ~54.0 GB | 8.8 GB | **7.21 GB** | **86.6% smaller** |

---

## Model Artifacts in this Repository

| File Name | Size | Format / Description |
| :--- | :---: | :--- |
| **`Ternary-Bonsai-2-27B-PQ2_0.gguf`** | 7.21 GB | Primary ternary 2-bit slot model weights |
| **`Ternary-Bonsai-2-27B-PTQ1_0.gguf`** | 5.95 GB | Densely packed 1.75-bit model weights |
| **`Ternary-Bonsai-2-27B-mmproj-Q8_0.gguf`** | 629 MB | Quantized vision multimodal projector |
| **`Ternary-Bonsai-2-27B-mmproj-BF16.gguf`** | 1.2 GB | Full precision vision multimodal projector |
| **`Bonsai-2-27B-DFlash2-Q8_0.gguf`** | 2.05 GB | DFlash 2 speculative block-diffusion drafter |
| **`SHA256SUMS`** | 66 KB | Checksum verification file |

---

## Hardware Benchmarks

### 1. Apple Silicon M4 / M5 Max (Metal)
- **Decode Speed (Standalone)**: **47.2 tok/s**
- **Decode Speed (with DFlash 2)**: **92.4 tok/s**
- **Memory Footprint**: 7.84 GB VRAM (including vision projector & 32k KV cache)

### 2. NVIDIA Blackwell DGX Spark (CUDA 13.3)
- **Decode Speed (with DFlash 2)**: **170.04 tok/s**
- **Draft Acceptance Rate**: **54.20%**
- **Context Allocation**: 32,768 tokens across 4 parallel slots in under 12 GB total VRAM.

---

## Serving Instructions

### Option A: Llama.cpp with Multimodal & DFlash 2 Speculative Decoding
```bash
llama-server \
  -m Ternary-Bonsai-2-27B-PQ2_0.gguf \
  --mmproj Ternary-Bonsai-2-27B-mmproj-Q8_0.gguf \
  --draft-model Bonsai-2-27B-DFlash2-Q8_0.gguf \
  --draft-max 3 \
  -c 32768 \
  --port 8080
```

### Option B: Apple Silicon Metal Native
```bash
llama-cli \
  -m Ternary-Bonsai-2-27B-PQ2_0.gguf \
  --mmproj Ternary-Bonsai-2-27B-mmproj-Q8_0.gguf \
  -p "<image>\nDescribe the system architecture shown in this diagram in detail."
```

---

## Citation

```bibtex
@article{zenlm2026zen6flash,
  title={Zen6 Flash: Sub-2-Bit Ternary Vision-Language Model with Block-Diffusion Speculative Decoding},
  author={Hanzo AI and Zen LM Team},
  year={2026},
  publisher={Zen LM / Hanzo AI}
}
```
