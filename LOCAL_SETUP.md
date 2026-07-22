# Running ANLP Notebooks on Your Own Machine

> **Recommended:** Use the course JupyterHub at
> <https://www.anlp-course-um.nl> — everything is pre-installed and ready
> to go. The instructions below are **only** for students who prefer to
> work locally (e.g. for faster GPU access, offline work, or when the
> cluster is busy).

---

## Table of Contents

1. [Prerequisites](#1-prerequisites)
2. [Clone the Repository](#2-clone-the-repository)
3. [Create a Python Environment](#3-create-a-python-environment)
4. [Install Python Dependencies](#4-install-python-dependencies)
5. [Download NLTK Data](#5-download-nltk-data)
6. [Download spaCy Models](#6-download-spacy-models)
7. [GPU Support (optional but recommended)](#7-gpu-support-optional-but-recommended)
8. [OpenAI API Key (Assignments 11, 12 & 16)](#8-openai-api-key-assignments-11-12--16)
9. [Assignment-specific Notes](#9-assignment-specific-notes)
10. [Submitting Your Work](#10-submitting-your-work)
11. [Troubleshooting](#11-troubleshooting)

---

## 1. Prerequisites

| Requirement | Details |
|---|---|
| **Python** | 3.13 (recommended and tested). Python 3.10–3.12 should also work. Python 3.14+ may cause compatibility issues with some packages. |
| **pip** | Latest version (`python -m pip install --upgrade pip`) |
| **Git** | To clone the repository |
| **OS** | Windows 10/11, macOS 12+, or Linux (Ubuntu 20.04+) |
| **RAM** | Minimum 8 GB; 16 GB recommended |
| **Disk space** | ~10 GB free (for packages, models, and datasets) |

---

## 2. Clone the Repository

```bash
git clone https://github.com/TextMiningUM/ANLP-2026-2027.git
cd ANLP-2026-2027
```

---

## 3. Create a Python Environment

We strongly recommend using a **virtual environment** to avoid conflicts with
other projects.

### Option A — conda (recommended if you have Anaconda/Miniconda)

```bash
conda create -n anlp python=3.13 -y
conda activate anlp
```

### Option B — venv (built-in)

```bash
python -m venv .venv

# Activate on Linux/macOS:
source .venv/bin/activate

# Activate on Windows (PowerShell):
.\.venv\Scripts\Activate.ps1

# Activate on Windows (cmd):
.\.venv\Scripts\activate.bat
```

---

## 4. Install Python Dependencies

A `requirements.txt` is provided in the repository root:

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

This installs **all** packages needed across all 19 assignments. The full
install takes roughly 5–15 minutes depending on your network and hardware.

> **Note on PyTorch:** The command above installs the CPU version of PyTorch.
> If you have an NVIDIA GPU, see [Section 7](#7-gpu-support-optional-but-recommended)
> for GPU-accelerated installation.

---

## 5. Download NLTK Data

Several assignments rely on NLTK corpora and models. Run this **once** after
installing the Python packages:

```python
import nltk
nltk.download([
    'punkt',
    'punkt_tab',
    'stopwords',
    'wordnet',
    'omw-1.4',
    'words',
    'movie_reviews',
    'brown',
    'universal_tagset',
    'book',
    'tagsets_json',
    'averaged_perceptron_tagger',
    'averaged_perceptron_tagger_eng',
    'treebank',
    'vader_lexicon',
    'maxent_ne_chunker',
    'maxent_ne_chunker_tab',
])
```

Or from the command line:

```bash
python -m nltk.downloader punkt punkt_tab stopwords wordnet omw-1.4 words movie_reviews brown universal_tagset book tagsets_json averaged_perceptron_tagger averaged_perceptron_tagger_eng treebank vader_lexicon maxent_ne_chunker maxent_ne_chunker_tab
```

---

## 6. Download spaCy Models

Several assignments require spaCy English models:

```bash
python -m spacy download en_core_web_sm
python -m spacy download en_core_web_md
```

---

## 7. GPU Support (optional but recommended)

A CUDA-capable **NVIDIA GPU** significantly speeds up the deep-learning
assignments (06, 07, 08, 09, 11, 12). All assignments include CPU fallbacks, 
so a GPU is **not** strictly required — they will just run slower.

### Which assignments benefit from a GPU?

| Assignment | Topic | GPU benefit |
|---|---|---|
| 06 — PyTorch | Deep learning fundamentals | Moderate |
| 07 — Transformers | Transformer models | **High** |
| 08 — Encoder Models | BERT-based models | **High** |
| 09 — Decoder Models | GPT-based models | **High** |
| 11 — Fine-tuning LLMs | LLM fine-tuning | **Very High** |
| 12 — Multi-modal Models | Vision-language models | **Very High** |

### GPU Compatibility

**Works with ALL modern NVIDIA GPUs:**
- ✅ RTX 30xx series (3060, 3070, 3080, 3090)
- ✅ RTX 40xx series (4060, 4070, 4080, 4090)
- ✅ RTX 50xx series (5080, 5090) — when available
- ✅ Professional cards (A100, H100, L40, RTX 6000, etc.)
- ✅ Older cards: GTX 1660, RTX 20xx series

**Minimum requirement:** NVIDIA GPU with Compute Capability 5.0+ (GTX 900 series from 2014+)

### VRAM Requirements

The amount of GPU memory determines which models and batch sizes you can use:

| GPU VRAM | Suitable for | Typical models | Batch size |
|---|---|---|---|
| 4–6 GB | Basic training | Small BERT, DistilBERT | 2–4 |
| 8–12 GB | Most assignments | BERT-base, GPT-2 | 8–16 |
| 16–24 GB | Large models | BERT-large, GPT-2-large | 16–32 |
| 24+ GB | Fine-tuning LLMs | LLaMA-7B (with 4-bit) | 32+ |

> **Note:** Assignment 11 (Fine-tuning LLMs) requires **8GB+ VRAM**. If you have less,
> use model quantization (4-bit/8-bit) or the course JupyterHub cluster.

### Installing PyTorch with CUDA

First, **uninstall** any existing PyTorch:

```bash
pip uninstall torch torchvision torchaudio -y
```

Install PyTorch with CUDA 12.4 (works on all NVIDIA drivers 525.60.13+):

```bash
# Windows / Linux / macOS
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu124
```

> **Why cu124?** CUDA 12.4 is **backward compatible** with all modern GPUs.
> It works whether your `nvidia-smi` shows CUDA 11.x, 12.x, or 13.x.
> The driver version matters more than the CUDA version shown.

### Verify GPU Access

Create a test script `test_gpu.py`:

```python
import torch

print(f"PyTorch version: {torch.__version__}")
print(f"CUDA available: {torch.cuda.is_available()}")

if torch.cuda.is_available():
    print(f"CUDA version: {torch.version.cuda}")
    print(f"GPU: {torch.cuda.get_device_name(0)}")
    
    # Check VRAM
    props = torch.cuda.get_device_properties(0)
    print(f"VRAM: {props.total_memory / 1024**3:.1f} GB")
    print(f"Compute capability: {props.major}.{props.minor}")
else:
    print("No GPU detected — using CPU only")
```

Run it:

```bash
python test_gpu.py
```

Expected output:
```
PyTorch version: 2.6.0+cu124
CUDA available: True
CUDA version: 12.4
GPU: NVIDIA GeForce RTX 4070 Ti
VRAM: 12.0 GB
Compute capability: 8.9
```

### Troubleshooting

**Problem:** `CUDA available: False`

**Solutions:**
1. Check if you have an NVIDIA GPU: `nvidia-smi`
2. Update NVIDIA drivers: <https://www.nvidia.com/Download/index.aspx>
3. Reinstall PyTorch with CUDA (see above)
4. Verify you're using the correct Python environment

**Problem:** `OutOfMemoryError` during training

**Solutions:**
1. **Reduce batch size:** Change `batch_size=16` to `batch_size=4` or lower
2. **Use gradient accumulation:**
   ```python
   # Instead of batch_size=32, use batch_size=8 with 4 accumulation steps
   trainer = Trainer(..., per_device_train_batch_size=8, gradient_accumulation_steps=4)
   ```
3. **Enable gradient checkpointing:**
   ```python
   model.gradient_checkpointing_enable()
   ```
4. **Use mixed precision:**
   ```python
   trainer = Trainer(..., fp16=True)  # or bf16=True for newer GPUs
   ```
5. **Use model quantization (8-bit or 4-bit):**
   ```python
   from transformers import AutoModelForCausalLM
   model = AutoModelForCausalLM.from_pretrained("gpt2", load_in_8bit=True)
   ```

**Problem:** Training is slow even with GPU

**Check:**
1. Verify GPU is actually being used:
   ```python
   print(next(model.parameters()).device)  # Should show 'cuda:0'
   ```
2. Monitor GPU utilization: `nvidia-smi -l 1` (refreshes every second)
3. Ensure data is on GPU: `inputs = inputs.to('cuda')`

---

## 8. OpenAI API Key (Assignments 11, 12 & 16)

Assignments 11 (*Fine-tuning LLMs*), 12 (*Multi-modal Models*), and 16 
(*Agents*) use the **OpenAI API**. You will need:

1. An OpenAI account with **billing enabled** at <https://platform.openai.com>.
2. An API key generated at <https://platform.openai.com/api-keys>.

The cost is modest — expect roughly **€2–€5** for completing these assignments
with `gpt-4o-mini` or `gpt-4o`.

The notebooks will prompt you for the key using `getpass` (it is never stored
in the notebook). Alternatively, set it as an environment variable:

```bash
# Linux/macOS
export OPENAI_API_KEY="sk-..."

# Windows PowerShell
$env:OPENAI_API_KEY = "sk-..."
```

> **Important:** Never commit your API key to Git. The `.gitignore` should
> already exclude sensitive files, but double-check before pushing.

---

## 9. Assignment-specific Notes

### Assignment 01 — Tokenization
- Fetches live webpages via `urllib.request`; requires internet access.
- Uses various tokenization libraries (NLTK, spaCy, etc.).

### Assignment 04 — Syntax and Semantics
- Requires parsing libraries for syntax tree visualization.
- Uses `svgling` for tree rendering.

### Assignment 05 — Statistical NLP
- Covers n-gram models and language model evaluation.
- Downloads text corpora from NLTK.

### Assignment 06 — PyTorch
- Introduction to PyTorch tensor operations.
- GPU highly recommended for training exercises.

### Assignment 07 — Transformers
- Introduction to the Transformers library.
- Downloads pre-trained models from HuggingFace (~500MB each).

### Assignment 08 — Encoder Models
- Fine-tuning BERT and similar models.
- GPU strongly recommended (CPU training may take 20+ minutes).

### Assignment 09 — Decoder Models
- Working with GPT-style models.
- Text generation tasks; GPU recommended.

### Assignment 10 — XAI in NLP
- Explainability methods for NLP models.
- Uses attention visualizations and LIME/SHAP.

### Assignment 11 — Fine-tuning LLMs
- Fine-tuning large language models.
- **GPU with 8GB+ VRAM required** for most exercises.
- OpenAI API needed for comparison tasks.

### Assignment 12 — Multi-modal Models
- Vision-language models (CLIP, BLIP, etc.).
- Downloads large model checkpoints (~1–2GB).
- GPU recommended; OpenAI API for GPT-4V tasks.

### Assignment 13 — Speech Recognition
- Uses speech processing libraries (librosa, soundfile).
- May require audio file downloads.

### Assignment 14 — Abstracting and MT
- Machine translation and text summarization.
- Uses sequence-to-sequence models.

### Assignment 15 — Dialog
- Dialog systems and conversational AI.
- Covers both rule-based and neural approaches.

### Assignment 16 — Agents
- AI agents and autonomous systems.
- Uses OpenAI Agents SDK; requires `openai>=1.40.0`.

### Major Assignments

#### A1 — NLP Fundamentals
- Comprehensive assessment of basic NLP concepts (Assignments 01-05).

#### A2 — Deep Learning for NLP
- Applied deep learning for NLP (Assignments 06-10).

#### A3 — NLP Applications
- Advanced NLP applications (Assignments 11-16).

---

## 10. Submitting Your Work

Even if you develop locally, you must **submit via the JupyterHub**:

1. Before submitting, **restart the kernel and run all cells** to ensure
   the notebook executes cleanly from top to bottom.
2. Upload your completed notebook to the JupyterHub by copying it into the
   appropriate `Submitted Work/<topic>/` folder on the cluster.
3. Keep the **original filename** — do not rename the notebook.

> **Tip:** As a final check, download your notebook from JupyterHub after
> uploading and verify it opens correctly.

---

## 11. Troubleshooting

### "ModuleNotFoundError: No module named '...'"
You likely missed a dependency. Make sure you installed from the provided
`requirements.txt` and that your virtual environment is activated.

### CUDA / GPU not detected
- Verify you installed the CUDA version of PyTorch (not the CPU version).
- Check that your NVIDIA drivers are up-to-date: `nvidia-smi`.
- Ensure `torch.cuda.is_available()` returns `True`.

### NLTK data not found
Re-run the NLTK download commands in [Section 5](#5-download-nltk-data).
You can also manually set the NLTK data path:
```python
import nltk
nltk.data.path.append('/path/to/your/nltk_data')
```

### HuggingFace model download is slow
Models are cached in `~/.cache/huggingface/`. The first load takes time;
subsequent loads are instant. If your network is restricted, consider
downloading models on a different network and copying the cache folder.

### Package version conflicts
If you encounter version incompatibilities, try creating a fresh environment:
```bash
conda create -n anlp-fresh python=3.10 -y
conda activate anlp-fresh
pip install -r requirements.txt
```

### "RuntimeError: CUDA out of memory"
Reduce the batch size in training cells, or switch to CPU by setting:
```python
device = torch.device("cpu")
```

### spaCy model not found
Re-run the spaCy model download commands:
```bash
python -m spacy download en_core_web_sm
python -m spacy download en_core_web_md
```

---

## Summary of External Services & Costs

| Service | Assignments | Cost | Required? |
|---|---|---|---|
| Course JupyterHub | All | Free | Primary platform |
| OpenAI API | 11, 12, 16 | ~€2–€5 | Yes, for these assignments |
| Internet access | Most | — | Yes (data & model downloads) |

---

*Last updated: July 2026*
