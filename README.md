# CSE 151B SP26: Math Reasoning Competition

**Model-intrinsic only · zero training · leaderboard score = 0.720**

I maximize accuracy of the base model **Qwen3-4B-Thinking-2507** using prompting + hyperparameter tuning only. The single entry point [`run_inference.py`](run_inference.py) reproduces my best submission (within 1-2%) end-to-end.

## Setup & inference time

| | |
|---|---|
| **OS** | Linux or WSL2 required for vLLM. I used Ubuntu on WSL2. |
| **GPU** | Nividia card, RTX 4070 (12 GB) or higher |
| **Inference time** | ~5 hours for the full 943-question private set (two-phase decode, 12288 tokens, FP8) |

If the model OOMs at load, lower `gpu_memory_utilization`. 


## Model weights setup

No manual download or placement is required. On the first call of `run_inference()`, vLLM automatically downloads `Qwen/Qwen3-4B-Thinking-2507-FP8` from the Hugging Face Hub.

## Install

VLLm requires python 3.10–3.12 and a working NVIDIA CUDA driver. Some systems may default to a newer unsupported Python version, se will set up a venv with python 3.12 to ensure vLLM can function.

In a WSL terminal, navigate to wherever you placed the repo and run:

```bash
sudo apt update
sudo apt install -y python3.12 python3.12-venv python3.12-dev

python3.12 -m venv .venv
source .venv/bin/activate

pip install "vllm==0.21.0" transformers            
```
VLLm is quite large, so please allow the package time to install to the venv.

## To reproduce the submission

The 943-question test set is already included in this repo at `data/private.jsonl` (each line: `{"id", "question", "options"?}`), so no setup is needed. Run the single entry point in the same terminal with the virtual environment activated:

```bash
python run_inference.py            
```

This will first load the data, then the model, run inference, then write **`results/submission.csv`** (`id,response`, all 943 rows, `csv.QUOTE_MINIMAL`).

**NOTE:** Full inference on my hardware configuration took ~5 hours to complete. The results CSV is not written until inference is completed on the entire set, so killing the script will not yield partial results. This inference pipeline was designed exclusively for NVIDIA architecture, so there may be some hiccups in reproduction that I could not account for if this is being run on datahub. 

## Repo contents

| Path | Purpose |
|---|---|
| **`run_inference.py`** | Single entry point: `run_inference()` → `results/submission.csv`. Needs only `vllm` + `transformers`; imports nothing else in this repo. |
| `judger.py`, `utils.py` | Official grader for local scoring of free-form / MCQ answers against the labeled `public.jsonl` set. |
| `data/` | `private.jsonl` (943, test set)· `public.jsonl` (1126, labeled set). |
| `results/` | Output directory: generated submission CSVs land here when you run `run_inference.py`. |
| `LICENSE` · `.gitignore` | License and Git ignore rules. |

