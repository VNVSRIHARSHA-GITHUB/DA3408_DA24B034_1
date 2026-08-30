## Setup

Install dependencies from the repo root:

```bash
pip install -r requirements.txt
```

### Running the MLflow server (required for Q2)

From the repo root, start the MLflow tracking server pointing at `q2/mlflow.db` and `q2/mlruns`:

```bash
cd q2
mlflow server \
  --backend-store-uri sqlite:///mlflow.db \
  --default-artifact-root ./mlruns \
  --host 0.0.0.0 \
  --port 5000 \
  --allowed-hosts "*" \
  --cors-allowed-origins "http://localhost:5000,http://127.0.0.1:5000"
```

Once running, the UI is available at **http://localhost:5000**, and `q2/train.ipynb` (which sets `mlflow.set_tracking_uri("http://localhost:5000")`) can be executed to reproduce all logged runs.


## Layout

DA3408_DA24B034_1/
├── report.pdf # Q1 (Technical Debt) + Q2 (Written Analysis)
├── requirements.txt # Python dependencies for the whole project
├── AI_DISCLOSURE.md # AI usage disclosure
├── data.dvc # DVC pointer file (dataset)
├── images.csv.dvc # DVC pointer file (filename index)
├── A1_3.3.png # Q3.3 — rollback proof (v1 row count)
├── A1_3.3.1.png # Q3.3 — rollback proof (v1 rollback checkout)
├── .dvc/ # DVC config (SSH remote)
├── .dvcignore
├── .gitignore
│
├── q2/ # Q2 — MLflow Experiment Comparison
│ ├── train.ipynb # Training script with mlflow.log_param/log_metric
│ ├── mlflow.db # Local MLflow tracking DB (all runs)
│ ├── comparision_mlflow_runs.png # Run-comparison table screenshot (7 runs)
│ ├── log_param_screenshot.png # mlflow.log_param code snippet
│ ├── log_metric_screenshot.png # mlflow.log_metric code snippet
│ └── mlruns/1/models/ # Logged model artifacts
│
└── q4/ # Q4 — End-to-End Reproducibility Drill
├── PARTNER_VERIFICATION.md # Partner B's full verification report
├── verifer_mlflow_screenshot.png # Partner B's reproduced MLflow run
└── proof.png # Cross-machine note logged to Partner A's run



### Q1 — Conceptual: Technical Debt Diagnosis 
**`report.pdf`** — Question-1 answers are in this pdf


---

### Q2 — Applied: MLflow Experiment Comparison 
| Requirement | Location |
|---|---|
| Training script (MLP + MNIST, ≥6 runs, 2 hyperparameters varied) | [`q2/train.ipynb`](q2/train.ipynb) — 7 runs total varying `hidden_layer_sizes`, `learning_rate_init`, and `batch_size` |
| MLflow run-comparison table screenshot | [`q2/comparision_mlflow_runs.png`](q2/comparision_mlflow_runs.png) |
| `mlflow.log_param(...)` code | [`q2/log_param_screenshot.png`](q2/log_param_screenshot.png) (also in `train.ipynb`) |
| `mlflow.log_metric(...)` code | [`q2/log_metric_screenshot.png`](q2/log_metric_screenshot.png) (also in `train.ipynb`) |
| 150–250 word written analysis (best run, overfitting evidence, dominant hyperparameter) | **`report.pdf`** — Question 2 section |
| Local MLflow tracking store (all run data) | [`q2/mlflow.db`](q2/mlflow.db) |

**Summary of findings:** Best run — `mlp-h128-lr001-b64` (val_accuracy = val_f1_macro = 0.979). Learning rate has the larger, more consistent effect on performance than architecture depth or batch size (full reasoning in `report.pdf`).

---

### Q3 — Applied: DVC Data Versioning & Rollback 
| Requirement | Location |
|---|---|
| DVC init + push to SSH remote (v1) | [`.dvc/`](.dvc/), [`data.dvc`](data.dvc), [`images.csv.dvc`](images.csv.dvc) |
| Simulated update to v2 (2801 rows incl. header) | Same `.dvc` files, updated at v2 commit — see `git log` / `git tag` history |
| Rollback to v1 via `git checkout` + `dvc checkout`, with proof of row count | [`A1_3.3.png`](A1_3.3.png) and [`A1_3.3.1.png`](A1_3.3.1.png) — terminal screenshots showing `git checkout v1` → `dvc checkout` → row count matching v1 exactly (1800 rows), then `git checkout v2` restoring 2800 |

Git tags `v1` and `v2` mark the two dataset versions in commit history.

---

### Q4 — Capstone: End-to-End Reproducibility Drill 
**Partner A (Vrishab A V, DA24B033)** — owns the base repo [`AIOps-A1`](https://github.com/vrishabav/AIOps-A1), trained the original model, logged it to MLflow with `git_commit` tag, versioned data with DVC, and registered the model to **Staging**.

**Partner B (Sri Harsha V, DA24B034)** — this repo's `q4/` folder contains the full reproduction record:

| Requirement | Location |
|---|---|
| Full verification write-up (procedure, dataset md5 check, environment, results table) | [`q4/PARTNER_VERIFICATION.md`](q4/PARTNER_VERIFICATION.md) |
| Screenshot of reproduced MLflow run (metrics, params, `git_commit` tag) | [`q4/verifer_mlflow_screenshot.png`](q4/verifer_mlflow_screenshot.png) |
| Proof of cross-machine note logged to Partner A's original run | [`q4/proof.png`](q4/proof.png) |

**Result:** Reproduced accuracy = 0.9520 vs. Partner A's recorded 0.9485 (Δ = 0.0035), within the stated tolerance of 0.005. Dataset md5 matched byte-for-byte. Full parameter/environment breakdown in `PARTNER_VERIFICATION.md`.

---

## AI Usage Disclosure
See [`AI_DISCLOSURE.md`](AI_DISCLOSURE.md) for a full account of how AI tools were used (conceptual clarification, debugging, command assistance) and confirmation that all experiments, MLflow runs, and DVC operations were actually performed rather than fabricated.

Tool used : ChatGpt(for concept learning),  Claude(for debugging and commnand assistance)

---


