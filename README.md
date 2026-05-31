# CSE 151B SP26 — Math Reasoning Competition

**Model-intrinsic only · zero training · leaderboard score = 0.720**

We maximize accuracy of the mandated base model **Qwen3-4B-Thinking-2507** using prompting + decoding only. The single entry point [`run_inference.py`](run_inference.py) reproduces our best submission (within 1-2%) end-to-end.

## Hardware & inference time

| | |
|---|---|
| **GPU used** | 1× **NVIDIA GeForce RTX 4070 (12 GB)**, WSL2 + vLLM |
| **Total inference time** | **~5 hours** for the full **943-question** private set (two-phase, 12288 tokens, FP8) |


## Model weights — setup

**No manual download or placement is required.** On the first call, vLLM automatically downloads `Qwen/Qwen3-4B-Thinking-2507-FP8` (~5 GB) from the Hugging Face Hub.

## Install

```bash
python -m venv .venv && source .venv/bin/activate
pip install "vllm==0.21.0" transformers                
```
`run_inference.py` is **fully self-contained** — it needs only `vllm` + `transformers` and imports nothing else from this repo.

## Reproduce the submission

1. Place the private test file at **`data/private.jsonl`** (each line: `{"id", "question", "options"?}`).
2. Run the single entry point:

```bash
python run_inference.py            
```
or from Python:
```python
from run_inference import run_inference
run_inference()                                            
run_inference(data_path="data/private.jsonl", out_csv="submission.csv")
```

The output CSV has columns `id,response` (full model output per id)

## Repo contents

| Path | Purpose |
|---|---|
| **`run_inference.py`** | **Single, self-contained entry point** — `run_inference()` → submission CSV (the 0.720 config). Needs only `vllm` + `transformers`; imports nothing else in this repo. |
| `judger.py`, `utils.py` | Official grader — for local scoring of free-form / MCQ answers against the labeled `public.jsonl` dev set. |
| `data/` | `private.jsonl` (the test set `run_inference.py` reads) · `public.jsonl` (labeled dev set). |
| `results/` | Output directory (tracked but kept empty) — generated submission CSVs land here and are gitignored; reproduce them by running `run_inference.py`. |
| `LICENSE` · `.gitignore` | License and Git ignore rules. |
