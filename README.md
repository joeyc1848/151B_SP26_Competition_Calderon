# CSE 151B SP26 — Math Reasoning Competition

**Model-intrinsic only · zero training · leaderboard score = 0.720**

I maximize accuracy of the mandated base model **Qwen3-4B-Thinking-2507** using prompting + decoding only. The single entry point [`run_inference.py`](run_inference.py) reproduces my best submission (within 1-2%) end-to-end.

## Setup & inference time

| | |
|---|---|
| **OS** | Linux or WSL2 required for vLLM |
| **GPU** | Nividia card, RTX 4070 (12 GB) or higher. The default checkpoint is FP8-quantized |
| **Inference time** | ~5 hours for the full 943-question private set (two-phase decode, 12288 tokens, FP8) |

If the model OOMs at load, lower `gpu_memory_utilization` (e.g. `run_inference(gpu_memory_utilization=0.80)`). On non-FP8 hardware, pass an unquantized checkpoint: `run_inference(model="Qwen/Qwen3-4B-Thinking-2507")`.


## Model weights — setup

No manual download or placement is required. On the first call, vLLM automatically downloads `Qwen/Qwen3-4B-Thinking-2507-FP8` from the Hugging Face Hub.

## Install

Requires Python 3.10–3.12 and a working NVIDIA CUDA driver.

```bash
python -m venv .venv && source .venv/bin/activate
pip install "vllm==0.21.0" transformers                
```


## Reproduce the submission

The 943-question test set is already included in this repo at `data/private.jsonl` (each line: `{"id", "question", "options"?}`), so no setup is needed. Run the single entry point in a terminal:

```bash
python run_inference.py            
```
or from Python:
```python
from run_inference import run_inference
run_inference()                                            
run_inference(data_path="data/private.jsonl", out_csv="submission.csv")
```

This writes **`submission.csv`**. 

## Repo contents

| Path | Purpose |
|---|---|
| **`run_inference.py`** | Single, self-contained entry point: `run_inference()` → submission CSV (the 0.720 config). Needs only `vllm` + `transformers`; imports nothing else in this repo. |
| `judger.py`, `utils.py` | Official grader for local scoring of free-form / MCQ answers against the labeled `public.jsonl` dev set. |
| `data/` | `private.jsonl` (the test set `run_inference.py` reads) · `public.jsonl` (labeled dev set). |
| `results/` | Output directory: generated submission CSVs land here; reproduce them by running `run_inference.py`. |
| `LICENSE` · `.gitignore` | License and Git ignore rules. |
