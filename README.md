# Failure-Pattern Analysis of Malicious URL Detectors — Code and Data

This repository contains the pipeline code and dataset snapshots used to produce the
results reported in *"Failure-Pattern Analysis of Malicious URL Detectors Using
Explainable AI and Error-Centric Evaluation."*

## What this pipeline does

The notebook builds a domain-diversity-balanced dataset of benign, phishing, and
malware URLs, trains two baseline classifiers (a Random Forest and a character-level
LSTM) on identical data splits, isolates every misclassification from both models into
a single error vault, and applies SHAP and LIME **only** to those misclassified
samples to explain why each model failed. It then compares error patterns across
behavioural and infrastructure features (redirect depth, URL shorteners, domain age,
hosting type) to identify which real-world evasion behaviours are associated with
detector failure.

## Files

| File | Produced by | Description |
|---|---|---|
| `Research3.ipynb` | — | The complete pipeline, Steps 1–11, as separate notebook cells. |
| `requirements.txt` | — | Python packages needed to run the notebook. |
| `balanced_dataset.csv` | Step 1b | The frozen, domain-diversity-balanced dataset (15,000 URLs per class, 45,000 total) used for all reported results. |
| `snapshotted_dataset.csv` | Step 1c | The balanced dataset with WHOIS/RDAP and redirect-trace features attached, captured immediately after balancing. |
| `train_holdout_split.csv` | Step 2 | The exact domain-grouped train/holdout split (75/25) used by both baseline classifiers, so results can be reproduced without re-running the split. |
| `whois_cache.db` | Step 1c | SQLite cache of raw WHOIS/RDAP lookups. |
| `error_vault.db` | Step 6 | SQLite table of every false positive and false negative from both models, with full feature snapshots. |

*(Include whichever of the files above you still have available locally — see "What
to upload" below if some are missing.)*

## Pipeline steps (matches the notebook's markdown cells)

1. **Dataset Acquisition** — live pull from PhishTank, OpenPhish, URLHaus, and Tranco.
2. **Balance Dataset** — domain-diversity-aware balancing to 15,000 URLs/class.
3. **Snapshot Features** — one-time WHOIS/RDAP + redirect-trace capture.
4. **Split and Load** — label-verification simulation and domain-grouped train/holdout split.
5. **Lexical Features** — URL-string-derived features (length, entropy, punctuation counts, subdomain count).
6. **Random Forest** — baseline classifier training and evaluation.
7. **LSTM** — character-level baseline classifier training and evaluation.
8. **Error Vault** — isolation of every false positive/negative from both models.
9. **SHAP Analysis** — explains Random Forest's misclassifications.
10. **LIME Analysis** — explains the LSTM's misclassifications.
11. **Feature Comparison / Visualization / Stratified Error Analysis** — the figures reported in the paper's Results section.

## Environment setup

```bash
pip install -r requirements.txt
```

or, inside the notebook itself, run the first cell:

```python
!pip install -q scikit-learn tensorflow shap lime tldextract python-whois dnspython requests pandas
```

The pipeline was developed and run in Google Colab (hosted Python 3.x runtime).

## How to reproduce the reported results

1. Run Steps 1–2 (Dataset Acquisition, Balance Dataset) **or**, to reproduce the exact
   figures in the paper without re-fetching live feeds, skip straight to loading
   `balanced_dataset.csv` directly.
2. Run Step 1c (Snapshot Features) if starting from scratch, **or** load
   `snapshotted_dataset.csv` directly to reuse the original WHOIS/redirect snapshot.
3. Run Steps 2 onward (Split and Load through Stratified Error Analysis) in order.

## Important note on reproducibility

PhishTank, OpenPhish, URLHaus, and Tranco are **live, continuously updated feeds**.
Re-running Step 1 today will pull different URLs than the ones used in this study, and
WHOIS/redirect data for any given URL changes as attacker infrastructure is taken
down or repurposed. The frozen CSV/DB files in this repository are what make the
paper's specific reported numbers reproducible; re-running the acquisition steps
against live feeds is expected to produce different (though structurally similar)
results.

## Citation

If you use this code or dataset, please cite the accompanying paper (see the paper's
Reference list for full details).
