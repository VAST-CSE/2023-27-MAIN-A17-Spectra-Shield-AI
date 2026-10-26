# Spectra-Shield AI — Biophysical Trait Inversion Engine

Autonomous, non-destructive diagnostic framework developed for the pre-symptomatic detection of plant physiological stresses and traits from leaf diffuse reflectance spectroscopy (350–2500 nm).

This module implements the **Baseline Biophysical Trait Inversion Engine** using Partial Least Squares Regression (PLSR) calibrated against the **Global Spectra-Trait Initiative (GSTI)** database.

---

## 1. Project Structure

```
Spectra-shield AI/
├── data/
│   ├── raw/             # Raw GSTI database partitions (Database_p1..p3.csv, git-ignored)
│   └── processed/       # Cached matrices and cleaned subsets
├── src/
│   ├── data/
│   │   ├── downloader.py  # Streams & downloads GSTI database partitions
│   │   ├── audit.py       # Full data quality, missingness, and range audit
│   │   └── loader.py      # Standardized ingestion, filtering, and band trimming
│   ├── preprocessing/
│   │   └── spectral.py    # Leak-free sklearn transformers (SNV, SG Smoothing, Pseudo-Absorbance)
│   ├── models/
│   │   └── plsr.py        # PLSR with cross-validation tuning & VIP score extraction
│   ├── evaluation/
│   │   └── metrics.py     # R², RMSE, %RMSE, MAE, Bias, RPD calculation
│   └── train.py           # End-to-end training, grouped evaluation & benchmarking
├── notebooks/             # Exploratory analysis and spectral visualizations
├── reports/               # Markdown and JSON data audit & model evaluation reports
├── models/                # Serialized model artifacts (.joblib)
├── requirements.txt       # Python package dependencies
├── .gitignore             # Git ignore rules for data and model binaries
└── README.md              # Project documentation
```

---

## 2. Installation & Environment Setup

Ensure Python 3.10+ is available:

```bash
# Clone or navigate to the repository
cd "Spectra-shield AI"

# Install dependencies
pip install -r requirements.txt
```

---

## 3. Workflow & Usage

### Step 1: Download GSTI Database
The standardized database (~178 MB uncompressed) is partitioned into 3 files in the GSTI repository:
```bash
python src/data/downloader.py
```

### Step 2: Run Data-Audit Report
Generates comprehensive missingness, spectral range, and metadata statistics:
```bash
python src/data/audit.py
```
*Report output:* `reports/gsti_data_audit_report.md`

### Step 3: Train & Benchmark Baseline PLSR
Executes leak-free cross-dataset validation, evaluates multiple chemometric preprocessing pipelines, and computes Variable Importance in Projection (VIP) scores:
```bash
python src/train.py
```
*Model output:* `models/plsr_vcmax25_baseline.joblib`  
*Evaluation report:* `reports/baseline_plsr_vcmax25_evaluation.md`

---

## 4. Scientific Constraints & Guidelines

- **No Health / Disease Labelling:** GSTI observations must not be labeled as diseased or healthy; the database represents physiological measurements of wild, natural, and experimental plants across global biomes.
- **Leakage Prevention:** To prevent severe optimistic bias, models must use grouped splitting (by `Dataset_name` or `Site_name`) so that spectra from the same measurement campaign do not leak between training and testing.
- **No Target Imputation:** Missing target trait values are never synthetically imputed or fabricated.
- **Spectral Trimming:** Due to detector noise and low SNR at sensor boundaries, spectral bands below 400 nm and above 2400 nm are routinely excluded in standard preprocessing.

---

## 5. Provenance & Citations

* **GSTI Database:** [https://github.com/plantphys/gsti](https://github.com/plantphys/gsti) (v1.0.1)
* **Citation:** Lamour, J., Serbin, S. P., et al. (2026). *The Global Spectra-Trait Initiative: A database of paired leaf spectroscopy and functional traits associated with leaf photosynthetic capacity*. Earth System Science Data, 18, 245–265. DOI: [10.5194/essd-18-245-2026](https://doi.org/10.5194/essd-18-245-2026).
* **Best Practices:** Burnett, A. C., et al. (2021). *A best-practice guide to predicting plant traits from leaf-level hyperspectral data using partial least squares regression*. Journal of Experimental Botany, 72(18), 6175–6189.
