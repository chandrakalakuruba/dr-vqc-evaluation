# Does a Variational Quantum Layer Help? A Leakage-Free, Externally Validated Evaluation of a Hybrid CNN–Transformer for Diabetic Retinopathy Grading

Code, data splits and result tables for the paper:

> C. Kuruba and S. Raju, "Does a Variational Quantum Layer Help? A Leakage-Free, Externally Validated Evaluation of a Hybrid CNN–Transformer for Diabetic Retinopathy Grading," submitted to *IEEE Journal of Biomedical and Health Informatics*, 2026.

The study evaluates an eight-qubit variational quantum circuit (VQC), simulated classically with PennyLane, inside a multi-scale EfficientNet-B2 + Transformer grader for five-grade diabetic retinopathy (DR) grading. It compares the hybrid model with a same-input no-VQC control, a classical-only model and two CNN-only ablations (three seeds each), audits APTOS 2019 and DDR for duplicate images and conflicting labels, and validates all models without retraining on IDRiD and DDR. It also evaluates multi-source training against a pre-specified criterion and label-free test-time adaptation (AdaBN).

## Repository structure

```
dr-vqc-evaluation/
├── README.md
├── LICENSE
├── requirements.txt
├── data_splits/      cleaned partitions and audit outputs (image identifiers only, no images)
├── notebooks/        Google Colab notebooks, numbered in the order they are run
└── results/          result tables behind Tables IV–XII of the paper
```

## Notebooks

| Notebook | What it does | Paper |
|---|---|---|
| `01_aptos_audit_and_train.ipynb` | Audits APTOS (MD5 hashing, removal of duplicates and conflicting labels), writes `aptos_clean_split.csv`, and trains the hybrid model or the no-VQC control | Sec. III-B, IV; Tables II, IV, V |
| `02_ddr_audit_and_splits.ipynb` | Audits DDR, checks overlap with APTOS and IDRiD, and creates the 70/15/15 DDR partitions | Sec. III-C; Table II |
| `03_train_controls_aptos.ipynb` | Trains the classical-only and CNN-only (multi-scale and single-scale) control models | Sec. IV-G; Tables IV, VI, VII |
| `04a_train_multisource.ipynb` | Trains the multi-source (APTOS + DDR) hybrid model | Sec. III-F; Tables IX, X |
| `04b_train_multisource_batch.ipynb` | Runs the multi-source training configurations in batch | Tables V, IX |
| `05a_external_eval_idrid.ipynb` | Zero-shot evaluation on IDRiD | Tables VII, VIII |
| `05b_external_eval_ddr.ipynb` | Zero-shot evaluation on DDR | Tables VII, VIII |
| `06_size_timing_adabn.ipynb` | Parameter counts, inference timing, AdaBN adaptation and paired bootstrap intervals | Sec. III-G, V-F, V-G; Tables XI, XII |
| `07_diagnostic_classical_head.ipynb` | Diagnostic comparing the classical head with the full model's predictions | Sec. VI-A |
| `08_analysis.ipynb` | Summary tables, seed ensembles and paired bootstrap comparisons | Tables IV–X |

Run the notebooks in numerical order: notebook 02 and later notebooks read `aptos_clean_split.csv`, which notebook 01 writes.

### Model configurations

All models were trained with seeds 42, 43 and 44 (set `SEED` at the top of each notebook). In notebook 01, the hybrid model and the no-VQC control differ only in two settings of the configuration cell:

| Model in the paper | `quantum_mode` | `tag` |
|---|---|---|
| Hybrid (VQC) | `"circuit"` | `"v3"` |
| No-VQC control | `"skip"` | `"skip50"` |

With these tags, the checkpoint names match those in the archived models (for example `best_v3_circuit_aNone_pe0_L4_s42.pth`). The other `quantum_mode` options in the configuration cell were not used in the paper.

## Datasets

The images are not redistributed here. Download them from their original sources:

- **APTOS 2019 Blindness Detection:** <https://www.kaggle.com/competitions/aptos2019-blindness-detection> (3,662 labelled images). Notebook 01 expects a pre-partitioned copy with `train_1.csv`, `valid.csv` and `test.csv` (2,930 / 366 / 366 images); the cleaned partitions actually used are given in `data_splits/aptos_clean_split.csv`.
- **DDR:** T. Li *et al.*, *Information Sciences*, vol. 501, 2019. We used the 12,522 gradable images (the official dataset without its 1,151 ungradable images); the cleaned partitions are in `data_splits/ddr_clean_split.csv`.
- **IDRiD:** P. Porwal *et al.*, *Data*, vol. 3, no. 3, 2018, available from IEEE DataPort. All 516 graded images were used as an external test set.

Set `BASE_PATH` at the top of each notebook to the Google Drive folder that holds the data and outputs.

## Data splits

| File | Contents |
|---|---|
| `data_splits/aptos_clean_split.csv` | The 3,504 APTOS images retained after the audit, with their partition and MD5 hash |
| `data_splits/ddr_clean_split.csv` | The 12,424 DDR images retained after the audit, with their partition |
| `data_splits/ddr_audit.csv` | DDR audit details (duplicate groups and removed copies) |

## Trained models

The trained checkpoints (`.pth`, three seeds per configuration) are archived on Zenodo: [archive DOI]

## Environment

Experiments were run on Google Colab with an NVIDIA Tesla T4 GPU, Python 3.13 and the package versions in `requirements.txt`:

```
pip install -r requirements.txt
```

## Citation

If you use this code or the data splits, please cite the paper (citation details will be added on publication).

## License

The code is released under the MIT License (see `LICENSE`). The datasets remain subject to their own licences.
