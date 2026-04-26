# 💳 CreditIQ — Risk Intelligence Platform Dashboard

An interactive single-page HTML dashboard that operationalises the Credit Risk Assessment project as a production risk management platform. It includes a live applicant risk scorer, model comparison tools, a pending application review queue, and a full ML pipeline execution log — all in one self-contained file.

---

## 📁 File

`Credit_Risk_Project_Dashboard.html`

---

## 🚀 How to Open

No installation or server required. Simply open `Credit_Risk_Project_Dashboard.html` in any modern web browser (Chrome, Firefox, Edge, Safari).

---

## 📊 Dashboard Overview

### Portfolio Summary (Top-Level KPIs)
Displayed prominently at the top of the Overview panel:

| Metric | Value | vs Benchmark |
|--------|-------|--------------|
| ROC-AUC Score | **0.7396** | ↑ +0.0377 vs baseline |
| F1-Score | **0.61** | ↑ +0.07 vs SVM |
| Portfolio Default Rate | **12.3%** | Rolling 12-month average |
| Applicants Scored | **22,272** | ↑ 1,240 this month |

**Risk Tier Distribution:**
- 🟢 Low Risk — 68%
- 🟡 Medium Risk — 21%
- 🔴 High Risk — 11%

---

## 🗂️ Navigation Panels

### ◈ Overview
A portfolio-level view of model performance and default risk across the full applicant base.

**Charts included:**
- **Monthly Default Rate Trend** — rolling default rate over time
- **Risk Tier Distribution** — pie chart of Low / Medium / High tier breakdown
- **Default Rate by Age Group** — bar chart segmenting default likelihood across age cohorts
- **Default Rate by Income Type** — default rates across Working, Commercial Associate, Pensioner, and State Servant categories
- **Default Rate by Education** — default rates by education level (Higher Education, Secondary, Incomplete Higher, Lower Secondary)
- **Top 10 Predictive Features (SVM Tuned)** — horizontal bar chart of the most impactful model features

---

### ⬡ Model Comparison
Side-by-side evaluation of **XGBoost vs SVM**, comparing both baseline and tuned variants across all metrics.

**Champion Model Summary:**
| Best Metric | Value | Model |
|-------------|-------|-------|
| Best ROC-AUC | 0.7396 | XGBoost (Tuned) |
| Best F1-Score | 0.61 | XGBoost (Tuned) |
| Best SVM ROC-AUC | 0.69 | SVM (Tuned) |
| Best SVM F1-Score | 0.54 | SVM (Tuned) |

**Performance Table:** All model variants displayed with Accuracy, Precision, Recall, F1-Score, and ROC-AUC.

**Visual Comparisons:**
- **ROC-AUC Curves** — overlaid curves for XGBoost (Baseline), XGBoost (Tuned), SVM (Baseline), SVM (Tuned)
- **Metric Radar Chart** — normalised multi-metric spider chart for tuned models
- **Side-by-Side Metric Bar Chart** — all models compared across all metrics simultaneously

---

### ◎ Risk Predictor
A **live applicant scoring interface** powered by the production XGBoost pipeline. Operators can enter applicant details and receive an instant default probability and risk tier classification.

**Input Fields:**

| Field | Type | Options / Range |
|-------|------|-----------------|
| Age | Numeric | Years |
| Annual Income | Numeric | USD |
| Employment Duration | Numeric | Years |
| Family Size | Numeric | Count |
| Children Count | Numeric | Count |
| Gender | Toggle | Female / Male |
| Owns Car | Toggle | Yes / No |
| Owns Property | Toggle | Yes / No |
| Income Category | Dropdown | Working, Commercial Associate, Pensioner, State Servant |
| Education Level | Dropdown | Higher Education, Secondary, Incomplete Higher, Lower Secondary |
| Account Age | Slider | Months (displayed: 24 months default) |
| Avg Monthly Risk | Numeric | Observation window risk score |
| Months Tracked | Numeric | Credit history length |

**Output:**
- **Default Probability** — percentage likelihood of default
- **Risk Tier** — Low / Medium / High classification
- **Key Risk Factors** — top contributing features driving the prediction
- **Probability Gauge** — visual gauge displaying the default probability score

---

### ≡ Application Queue
A live review queue of **24 pending applications**, sorted by predicted default probability from highest to lowest risk.

**Filter Options:** All · 🟢 Low Risk · 🟡 Medium Risk · 🔴 High Risk

**Queue Columns:**

| Column | Description |
|--------|-------------|
| App ID | Unique application identifier |
| Age | Applicant age in years |
| Income | Annual income |
| Employment | Employment duration in years |
| Occupation | Job category |
| Avg Risk | Observation-window average monthly risk score |
| Default Prob | Predicted probability of default (%) |
| Risk Tier | Assigned tier: Low / Medium / High |

---

### ⊹ Pipeline Status
An execution log and status summary for the full ML pipeline run.

**Pipeline Metrics:**

| Metric | Value |
|--------|-------|
| Stages Complete | 11 / 11 — All systems nominal |
| Total Training Time | 4.3 minutes (including hyperparameter search) |
| Cross-Validation | 5-fold StratifiedKFold |
| Hyperparameter Search | 20 iterations · RandomizedSearchCV |
| Champion Model | XGBoost v2.4 |

**Execution Log:** A timestamped step-by-step log of each pipeline stage, from data loading and leakage audit through feature engineering, model training, cross-validation, and artefact export.

---

## 🛠️ Technical Details

| Property | Value |
|----------|-------|
| Type | Single-page HTML (self-contained) |
| Dependencies | None — no external server required |
| Dataset | Kaggle Credit Approval · 22,272 applicants · 25 features |
| Champion Model | XGBoost v2.4 · Live scoring |
| Compatibility | Chrome, Firefox, Edge, Safari |
