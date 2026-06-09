<!-- GitHub Copilot Instructions for ChestSight (chestsight-densenet121) -->
# Repo purpose
This repository is an MSc thesis project that fine-tunes a DenseNet-121 on the NIH ChestX-ray14 dataset via an executable Jupyter notebook. The primary artifact is the notebook ChestSight_Amit_Tiwari.ipynb which contains configuration, data loading, model definition, training, evaluation, and Grad-CAM explainability steps.

# Quick orientation (what matters most)
- Entry point: `ChestSight_Amit_Tiwari.ipynb` — treat the notebook as the canonical workflow. Follow sections 1→9 in order.
- Configs: All dataset paths and hyperparameters live in Section 1 of the notebook under `DATA_DIR` and top-level variables. When suggesting code changes, reference that section for default values.
- Data location: The dataset is external (NIH ChestX-ray14) and must be downloaded separately into `data/` (see README). The notebook expects `data/Data_Entry_2017_v2020.csv` and an `images/` folder.
- Output folders: `checkpoints/` and `outputs/` are created by the notebook for weights, plots and CSV metrics — preserve these conventions.

# Architecture and patterns (concise)
- Backbone: DenseNet-121 (ImageNet) with a custom classification head (GAP + Dropout + Sigmoid). Look in the notebook's "Model Architecture" section for layer ordering and dropout usage.
- Loss / metrics: Binary cross-entropy (BCE) with global sigmoid outputs; metric reporting uses scikit-learn classification reports and per-class summaries. Do not assume softmax or single-label losses.
- Data split: Notebook uses a random 70/10/20 split (train/val/test). Note: patient-level splitting is NOT implemented — flag to user if proposing changes that require patient grouping.
- Augmentation: Implemented in the notebook's preprocessing pipeline (tf.image / OpenCV). Keep augmentations inside the data pipeline for reproducibility.

# Developer workflows & commands
- Colab (recommended): open `ChestSight_Amit_Tiwari.ipynb` in Google Colab and enable a T4 GPU runtime. Mount Drive if you store data there.
- Local run (quick):
  - Install: `pip install -r Requirements.txt`
  - Launch: `jupyter notebook ChestSight_Amit_Tiwari.ipynb`
- Dependency file: `Requirements.txt` (TensorFlow 2.15+, NumPy, pandas, scikit-learn, OpenCV, Pillow). When proposing dependency changes, prefer minimal pin bumps and maintain TensorFlow 2.x compatibility.

# Coding agent guidance (how to be immediately useful)
- When editing training code, change only the notebook's training cell group and add a clear short comment pointing to the section (e.g., "Section 6 — Training: replace loss with focal loss").
- For new scripts (tests, reproducible runners), place them at repo root and add a short README snippet showing how to call them. Use small helper scripts (e.g., `scripts/convert_notebook.py`) rather than rewriting the notebook unless requested.
- Avoid hardcoding paths — use the top-level `DATA_DIR` defined in Section 1. If adding CLI utilities, accept `--data-dir` and default to the notebook's `DATA_DIR` value.
- Weight files: store model weights in `checkpoints/` using clear naming: `densenet121_epoch{EPOCH:02d}_val{VAL_LOSS:.4f}.h5`.

# Examples (concrete edits and references)
- Change dataset pointer: modify `DATA_DIR = '/path/to/data'` in Section 1 of `ChestSight_Amit_Tiwari.ipynb`.
- Switch loss to focal loss: add an import and replace the `loss` argument in the training compile cell; include a short justification comment and a reproducible seed set in Section 1.
- Add patient-level split: implement a helper under `scripts/` that reads `Data_Entry_2017_v2020.csv`, groups by `Patient ID` (or filename prefix), and writes `splits.pkl` — reference the notebook's Data Split cell for expected input shape.

# Safety, dataset & licensing notes
- The NIH ChestX-ray14 dataset must be downloaded separately and is governed by its own terms — do not attempt to fetch it automatically or push copies to this repo.
- Outputs may contain patient-derived images — avoid committing any `checkpoints/` or `outputs/` with patient images or private data. Prefer `.gitignore` for those folders if committing.

# What not to change without asking
- Do not change the notebook's top-level randomness/seed behavior without noting the effect in Section 1. The thesis depends on reproducible runs.
- Do not assume patient-level splitting has been applied — any model evaluation improvements that rely on patient splits must be verified and documented.

# Where to look next (key files)
- Notebook: [ChestSight_Amit_Tiwari.ipynb](ChestSight_Amit_Tiwari.ipynb)
- Repo README: [Readme.MD](Readme.MD)
- Dependencies: [Requirements.txt](Requirements.txt)

# If something is unclear
- Ask about where you'd like artifacts (checkpoints/outputs) stored, whether to add a script-based runner, or if you want patient-level split implemented.

---
Please review and tell me which areas you want expanded (example: add a small `scripts/` runner, add tests, or implement patient-level splits). 
