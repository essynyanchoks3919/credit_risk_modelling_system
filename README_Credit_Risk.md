# 🏦 Credit Risk Assessment & Default Prediction

A production-grade **credit risk scoring model** built on the Kaggle Credit Approval dataset (22,272 applicants, 25 features). The central challenge addressed is **data leakage** — a subtle but critical flaw in credit risk modelling where features are inadvertently computed using future information. This notebook rigorously audits for and eliminates leakage using an industry-standard observation/performance window methodology.

---

## 📁 File

`Esther_Credit_Risk_Project.ipynb`

---

## 📦 Dataset

| File | Description |
|------|-------------|
| `application_record.csv` | Applicant demographics, income, employment, housing, and asset data (22,272 rows) |
| `credit_record.csv` | Monthly credit status history per customer using DPD status codes |

### DPD Status Code Mapping

| Code | Meaning | DPD Equivalent |
|------|---------|----------------|
| `C` | Paid off / closed | 0 days |
| `X` | No loan activity | 0 days |
| `0` | Current (no overdue) | 0 days |
| `1` | 1–29 days overdue | 30 days |
| `2` | 30–59 days overdue | 60 days |
| `3` | 60–89 days overdue | 90 days |
| `4` | 90–119 days overdue | 120 days |
| `5` | 120+ days overdue | 150 days |

---

## 🔄 Pipeline Walkthrough

### Section 1 — Setup & Dependencies
XGBoost, Plotly, Scikit-learn, and Seaborn are installed and configured. Global parameters are defined upfront:

| Parameter | Value | Description |
|-----------|-------|-------------|
| `DPD_THRESHOLD` | 60 | Days Past Due threshold that defines a "bad" credit event |
| `OBS_WINDOW_MONTHS` | 3 | Feature construction period (months 0–3) |
| `PERF_WINDOW_MONTHS` | 12 | Target evaluation period (months 4–15) |
| `CV_FOLDS` | 5 | Number of cross-validation folds |
| `RANDOM_STATE` | 42 | Global seed for reproducibility |

A logging utility with elapsed-time tracking wraps every major pipeline stage.

### Section 2 — Data Acquisition
Both CSVs are loaded with renamed columns for clarity (e.g. `CODE_GENDER` → `GENDER`, `DAYS_BIRTH` → `AGE_IN_DAYS`, `AMT_INCOME_TOTAL` → `ANNUAL_INCOME`). The credit record `MONTHS_BALANCE` is converted to a positive integer, and DPD status codes are mapped to numeric days-past-due values. A binary `IS_BAD_60` flag is derived: 1 if DPD ≥ 60, else 0.

### Section 3 — Data Leakage Audit ⚠️
This is the **methodological centrepiece** of the notebook. The audit demonstrates that naively aggregating credit history over the full observation period produces a feature (`AVG_MONTHLY_RISK`) with near-perfect correlation to the target — not because it is informative, but because it is computed from the **same time window as the target variable itself**.

The audit quantifies this leakage:
- `AVG_MONTHLY_RISK_LEAKY` → correlation ~1.0 with default target (pure leakage, not signal)
- History length bias is also tested: customers with longer history may appear more likely to default simply because there are more months in which a bad event could occur

This section explains clearly why models trained with this feature produce near-100% metrics, and why those metrics are meaningless.

### Section 4 — Target Re-Engineering (Performance Window)
The solution is a **two-window framework** that mirrors industry-standard credit scorecard development:

```
OBS WINDOW  (months 0–3)   → Safe feature aggregation — no future information
PERF WINDOW (months 4–15)  → Target evaluation — no feature construction
```

**Target definition:** `TARGET = 1` if a customer experienced 60+ DPD in **any** month of the performance window.

Customers are excluded if they have insufficient history in either window. This produces a clean, leakage-free dataset where features describe the past and the target describes the future.

### Section 5 — Data Cleaning
Post-leakage-fix cleaning covers:
- Duplicate row removal
- Missing value imputation for balance columns (median fill)
- Validation that expected categorical values are present and consistent

### Section 6 — Feature Engineering
Domain-driven features are constructed using credit risk expertise:

| Feature Group | Features | Risk Logic |
|---------------|----------|------------|
| **Housing Risk** | `HOUSING_RISK_SCORE` | Tiered mapping by housing type — rented/municipal housing = high risk; owned property = low risk |
| **Occupation Risk** | `OCCUPATION_RISK_SCORE` | Manual/low-barrier roles = high risk; professional/specialist roles = low risk; unknown = high risk (adverse selection assumption) |
| **Income Features** | `INCOME_PER_MEMBER`, income-to-employment ratio | Measures financial capacity relative to household size |
| **Employment Tenure** | `EMPLOYMENT_STABILITY` flags | Duration-based thresholds distinguish stable from precarious employment |
| **OBS Window Aggregates** | `OBS_MAX_DPD`, `OBS_MEAN_DPD`, `OBS_N_LATE`, `OBS_WORST_STATUS` | Credit behaviour metrics computed strictly within the safe observation window |

### Section 7 — Exploratory Data Analysis
EDA includes:
- **Target distribution** — class imbalance visualisation (good vs bad credit ratio)
- **Feature drift charts** — how each feature's distribution shifts between good and bad customers
- **Income × Housing Risk crosstab** — heatmap of default rates across segments
- **Boxplots & violin plots** — employment stability vs occupation risk by default status
- **High-risk flag analysis** — default rates across age groups
- **Correlation matrix** — all engineered features and the target

### Section 8 — Modelling & Validation

Two classifiers are trained and compared using the same preprocessor and cross-validation strategy:

#### XGBoost
- `scale_pos_weight` set to the ratio of negative-to-positive class to handle imbalance
- Trained inside a scikit-learn `Pipeline` with `ColumnTransformer` for numeric/categorical preprocessing
- 5-fold `StratifiedKFold` cross-validation
- Evaluated on ROC-AUC, Precision, Recall, F1-Score, and PR-AUC

#### SVM (RBF Kernel)
- Feature scaling via `StandardScaler` inside the same `Pipeline` structure
- `CalibratedClassifierCV` wrapper applied to produce reliable probability outputs
- Same 5-fold stratified cross-validation strategy as XGBoost

Both models produce ROC curves, Precision-Recall curves, calibration plots, and full classification reports.

---

## 📊 Key Results

| Model | ROC-AUC | F1-Score | Notes |
|-------|---------|----------|-------|
| XGBoost (Tuned) | **0.7396** | **0.61** | Champion model — +0.0377 vs baseline |
| SVM (Tuned) | 0.69 | 0.54 | Strong baseline; slower to train |

**Portfolio default rate:** 12.3% (rolling 12-month average)
**Risk tier distribution:** 68% Low Risk · 21% Medium Risk · 11% High Risk

---

## 🛠️ Technologies Used

| Category | Tools |
|----------|-------|
| Data Processing | Pandas, NumPy |
| Machine Learning | Scikit-learn, XGBoost |
| Visualisation | Matplotlib, Seaborn, Plotly |
| Hyperparameter Tuning | RandomizedSearchCV |
| Model Persistence | joblib |
| Environment | Google Colab, Google Drive |

---

## 🚀 Getting Started

### Prerequisites
```bash
pip install pandas numpy scikit-learn xgboost plotly seaborn matplotlib joblib
```

### Setup
1. Upload the notebook to **Google Colab**.
2. Mount Google Drive — the first cell handles this automatically.
3. Place `application_record.csv` and `credit_record.csv` in a Drive folder named `credit_risk_assessment/`.
4. Run all cells in order.

---

## 📂 Data Source

**Kaggle Credit Approval Dataset**
Available on [Kaggle](https://www.kaggle.com/).

---

*Random seed: `42` · All observation-window features are computed strictly before the performance window to guarantee zero look-ahead bias.*
