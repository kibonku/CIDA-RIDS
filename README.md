# CIDA-RIDS: Curriculum Importance-Weighted Diverse-Attack Robust Intrusion Detection System

This is the final project of CPRE 5600 at Iowa State University. CIDA-RIDS is a robust Network Intrusion Detection System (NIDS) framework designed for adversarially perturbed network-flow features. The project combines a safe preprocessing/regularization stack with three proposed robustness modules:

1. **Importance-Weighted PGD / FGSM (IW-PGD, IW-FGSM)** — assigns larger perturbation budgets to more important mutable features.
2. **Curriculum ε-Scheduling** — gradually increases adversarial strength during training from `ε = 0.01` to `ε = 0.20`.
3. **Multi-Attack Diversity** — samples from IW-PGD, IW-FGSM, and Gaussian perturbation during training to reduce over-specialization.

This repository contains only the final code, minimal dependencies, and teaser image for the CPRE 560 final project submission.

---

## Project Summary

Machine-learning-based NIDS models can achieve high clean accuracy on benchmark datasets but remain vulnerable to small adversarial perturbations. Standard adversarial training also has limitations: it commonly applies a uniform perturbation budget across all features, may perturb protocol-semantic features that are unrealistic to modify, and can overfit to a single attack family.

CIDA-RIDS addresses these problems through:

- feature selection using `ExtraTreesClassifier`,
- a mutability mask to protect immutable protocol-semantic features,
- label smoothing and Gaussian augmentation,
- a denoising autoencoder at inference time,
- importance-weighted adversarial training,
- curriculum adversarial scheduling, and
- multi-attack training diversity.

---

## Dataset

The code uses the **NSL-KDD** benchmark dataset. The notebook downloads the training and testing files directly from a public GitHub mirror:

- `KDDTrain+.txt`
- `KDDTest+.txt`

The raw data are not included in this repository.

---

## Main Experimental Setup

| Item | Setting |
|---|---|
| Dataset | NSL-KDD |
| Train records | 125,973 |
| Test records | 22,544 |
| Encoded features | 122 |
| Selected features | 73 |
| Mutable / immutable features | 70 / 3 |
| Classifier | 3-hidden-layer MLP |
| Denoiser | 73 → 64 → 32 → 64 → 73 autoencoder |
| Attacks | Uniform FGSM, Uniform PGD, IW-FGSM, IW-PGD |
| ε values | 0.05, 0.10, 0.15, 0.20 |
| Threat model | White-box |

---

## Key Results

CIDA-RIDS achieved the lowest attack success rate (ASR) across the evaluated attack families compared with the clean DNN baseline and uniform PGD adversarial training baseline.

| Model | Clean Acc. | IW-PGD ASR @ ε=0.20 | IW-FGSM ASR @ ε=0.20 | Explanation Stability ρ |
|---|---:|---:|---:|---:|
| B1 Clean DNN | 0.799 | 0.960 | 0.745 | 0.896 |
| B3 Uniform PGD-AT | 0.753 | 0.585 | 0.401 | 0.899 |
| **CIDA-RIDS** | **0.776** | **0.334** | **0.246** | **0.929** |

Sensitivity analysis showed that the framework is stable across label smoothing values, curriculum schedule shapes, and attack-mix ratios. Across the tested settings, ASR varied only within a narrow range.

---

## How to Run

Open:

```text
CIDA_RIDS_Final_sensitivity.ipynb
```

Then run the notebook from top to bottom with GPU enabled:

```text
Runtime → Change runtime type → T4 GPU
```

The notebook includes data loading, preprocessing, model training, attack evaluation, explanation stability, and sensitivity analysis.

---

## Main Components in the Code

- `ClassifierMLP`: 3-hidden-layer MLP classifier.
- `DenoisingAutoencoder`: denoising module used at inference.
- `select_features_and_importance`: ExtraTrees-based feature selection and importance vector extraction.
- `get_mutability_mask`: constructs the mutable/immutable feature mask.
- `iw_pgd_attack`: importance-weighted PGD attack.
- `iw_fgsm_attack`: importance-weighted FGSM attack.
- `uniform_pgd_attack`: standard PGD baseline attack.
- `uniform_fgsm_attack`: standard FGSM baseline attack.
- `train_cida_rids`: curriculum and multi-attack adversarial training loop.
- sensitivity analysis blocks for label smoothing, curriculum shape, and attack-mix ratio.

---

## Notes

- Checkpoints and generated result CSVs are intentionally not included to keep the repository lightweight.
- The raw dataset is downloaded automatically by the notebook.
- For exact reproduction of the reported results, use the Colab notebook and the same random seed (`42`).

