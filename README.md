# CSE 151B SP26 — Math Reasoning Competition

**Model-intrinsic only · zero training · leaderboard score = 0.720**

We maximize accuracy of the mandated base model **Qwen3-4B-Thinking-2507** using prompting + decoding only. The single entry point [`run_inference.py`](run_inference.py) reproduces our best submission (within 1-2%) end-to-end.

## Requirements & inference time

| | |
|---|---|
| **OS** | Linux or **WSL2** — vLLM does not run on native Windows / macOS |
| **GPU** | 1× CUDA NVIDIA GPU, **≥12 GB VRAM**. We used an **RTX 4070 (12 GB)**. The default checkpoint is FP8-quantized (runs best on Ada/Hopper — RTX 40-series, L4, H100). |
| **Disk** | ~5 GB for the auto-downloaded model weights |
| **Inference time** | **~5 hours** for the full **943-question** private set (two-phase decode, 12288 tokens, FP8) |

If the model OOMs at load, lower `gpu_memory_utilization` (e.g. `run_inference(gpu_memory_utilization=0.80)`). On non-FP8 hardware, pass an unquantized checkpoint: `run_inference(model="Qwen/Qwen3-4B-Thinking-2507")`.


## Model weights — setup

**No manual download or placement is required.** On the first call, vLLM automatically downloads `Qwen/Qwen3-4B-Thinking-2507-FP8` (~5 GB) from the Hugging Face Hub. The model is public — no Hugging Face account or token is needed.

## Install

Requires **Python 3.10–3.12** and a working NVIDIA CUDA driver.

```bash
python -m venv .venv && source .venv/bin/activate
pip install "vllm==0.21.0" transformers                
```
`run_inference.py` is **fully self-contained** — it needs only `vllm` + `transformers` and imports nothing else from this repo.

## Reproduce the submission

The 943-question test set is **already included** in this repo at `data/private.jsonl` (each line: `{"id", "question", "options"?}`), so no setup is needed — just run the single entry point:

```bash
python run_inference.py            
```
or from Python:
```python
from run_inference import run_inference
run_inference()                                            
run_inference(data_path="data/private.jsonl", out_csv="submission.csv")
```

This writes **`submission.csv`** (943 rows; columns `id,response` — the full model response per id). Upload it to the Kaggle leaderboard to score **~0.720**. The default decode uses temperature 0.6, so expect minor run-to-run variation (pass `temperature=0` for a more repeatable greedy run).

## Repo contents

| Path | Purpose |
|---|---|
| **`run_inference.py`** | **Single, self-contained entry point** — `run_inference()` → submission CSV (the 0.720 config). Needs only `vllm` + `transformers`; imports nothing else in this repo. |
| `judger.py`, `utils.py` | Official grader — for local scoring of free-form / MCQ answers against the labeled `public.jsonl` dev set. |
| `data/` | `private.jsonl` (the test set `run_inference.py` reads) · `public.jsonl` (labeled dev set). |
| `results/` | Output directory (tracked but kept empty) — generated submission CSVs land here and are gitignored; reproduce them by running `run_inference.py`. |
| `LICENSE` · `.gitignore` | License and Git ignore rules. |
