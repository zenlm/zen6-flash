---
license: apache-2.0
language:
- en
- zh
pipeline_tag: image-text-to-text
tags:
- zen6
- zen6-flash
- zen
- ternary
- speculative-decoding
- metal
- apple-silicon
- llama-cpp
base_model:
- prism-ml/Ternary-Bonsai-2-27B-gguf
- ProCreations/Ternary-Bonsai-2-27B-DFlash2
---

<div align="center">

# Zen 6 Flash

**The compact Zen 6 · ternary 27B · 1.77 bits per weight · reads images · fits a laptop**

[![Hugging Face](https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-zenlm%2Fzen6--flash-blue)](https://huggingface.co/zenlm/zen6-flash)
[![GitHub](https://img.shields.io/badge/GitHub-zenlm%2Fzen6--flash-black)](https://github.com/zenlm/zen6-flash)
[![License](https://img.shields.io/badge/License-Apache%202.0-green.svg)](https://github.com/zenlm/zen6-flash/blob/main/LICENSE)

</div>

Zen 6 Flash is the ternary build of [Zen 6](https://huggingface.co/zenlm/zen6),
from Zen LM, the open model family of [Zoo Labs Foundation](https://zoo.ngo), a
501(c)(3) non-profit. It is chosen for the same two jobs as Zen 6, where the
machine is small:

- **Agentic coding that runs on your own machine**, in under 8 GB with its
  vision projector, on an Apple Silicon laptop or one GPU.
- **Marketing work**, reading images and documents beside the brief.

Zen 6 and Zen 6 Flash are available now; Zen 7 is in research preview:
[request access](https://hanzo.ai/research-access). Zen 6 Flash answers on
`api.hanzo.ai` as `zen6-flash`.

## What the files are

Every weight is ternary, the embeddings and the LM head included. The language
model holds 26,895,998,464 weights (read from the GGUF tensor table), so a file's
bits per weight is its size in bits over that count, and its ratio to FP16 is
53.79 GB (the count × 2 bytes) over its size.

| File | Size | Bits per weight | Smaller than FP16 |
| :--- | :---: | :---: | :---: |
| `Ternary-Bonsai-2-27B-PTQ1_0.gguf`, densely packed | 5.95 GB | 1.77 | 9.0x |
| `Ternary-Bonsai-2-27B-PQ2_0.gguf`, one trit per 2-bit slot, read by GPU and Metal kernels as is | 7.21 GB | 2.14 | 7.5x |
| `Ternary-Bonsai-2-27B-mmproj-Q8_0.gguf`, vision projector | 629 MB | | |
| `Ternary-Bonsai-2-27B-mmproj-BF16.gguf`, vision projector, full precision | 931 MB | | |
| `Bonsai-2-27B-DFlash2-Q8_0.gguf`, speculative drafter | 2.06 GB | | |
| `SHA256SUMS` | 66 KB | | |

| | |
| :--- | :--- |
| **Context** | 262,144 tokens native; about three layers in four are linear attention |
| **Inputs** | text and images |
| **Architecture** | <span data-upstream>GGUF `general.architecture`: `qwen35`</span> |

## Accuracy against full precision

| Suite | FP16 | A conventional 2-bit build (IQ2_XXS) | Zen 6 Flash (PQ2_0) | Retained |
| :--- | :---: | :---: | :---: | :---: |
| Average of 14 reasoning tests | 86.33 | 72.59 | 84.78 | 98.2% |
| Math (MathVision, GSM8K) | 97.10 | 79.40 | 96.57 | 99.5% |
| Code (HumanEval, LiveCode) | 90.20 | 74.80 | 89.42 | 99.1% |
| Tool calling and schema adherence | 76.80 | 58.10 | 74.92 | 97.6% |
| Footprint | 53.79 GB | 8.8 GB | 7.21 GB | 86.6% smaller |

## Measured

- **Apple Silicon M4 / M5 Max (Metal):** 47.2 tok/s decode alone, 92.4 tok/s with
  the drafter; 7.84 GB including the vision projector and a 32K cache.
- **DGX Spark (CUDA 13.3):** 170.04 tok/s decode with the drafter at 54.2% draft
  acceptance; 32,768 tokens across 4 parallel slots in under 12 GB.

## Serve it

llama.cpp, with the vision projector and the drafter:

```bash
llama-server \
  -m Ternary-Bonsai-2-27B-PQ2_0.gguf \
  --mmproj Ternary-Bonsai-2-27B-mmproj-Q8_0.gguf \
  -md Bonsai-2-27B-DFlash2-Q8_0.gguf \
  --spec-draft-n-max 3 \
  -c 32768 \
  --port 8080
```

On a Mac, one prompt:

```bash
llama-cli \
  -m Ternary-Bonsai-2-27B-PQ2_0.gguf \
  --mmproj Ternary-Bonsai-2-27B-mmproj-Q8_0.gguf \
  -p "<image>\nDescribe the system architecture shown in this diagram in detail."
```

## Citation

```bibtex
@misc{zenlm2026zen6flash,
  title  = {Zen 6 Flash},
  author = {Zen LM},
  year   = {2026},
  publisher = {Zoo Labs Foundation}
}
```

<div data-upstream>

## License & attribution

Apache-2.0; see [LICENSE](https://github.com/zenlm/zen6-flash/blob/main/LICENSE).
Zen 6 Flash is Ternary Bonsai 2 27B by prism-ml (`prism-ml/Ternary-Bonsai-2-27B-gguf`,
Apache-2.0), itself built from Qwen3.8-27B by the Qwen team (Apache-2.0), with the
speculative drafter by ProCreations (`ProCreations/Ternary-Bonsai-2-27B-DFlash2`).

</div>
