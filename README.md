<div align="center">

# 🏦 Lloyds Banking Group — Customer Churn Prediction

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-RandomForest-F7931E?style=for-the-badge&logo=scikit-learn&logoColor=white)](https://scikit-learn.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=for-the-badge&logo=jupyter&logoColor=white)](https://jupyter.org/)
[![imbalanced-learn](https://img.shields.io/badge/imbalanced--learn-SMOTE-6C3483?style=for-the-badge)](https://imbalanced-learn.org/)

> End-to-end churn prediction pipeline: data gathering across 5 sources, EDA, preprocessing, model training, and an actionable retention strategy for banking stakeholders.

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Tasks](#-tasks)
- [Dataset](#-dataset)
- [Methodology](#-methodology)
- [Feature Engineering](#-feature-engineering)
- [Model & Evaluation](#-model--evaluation)
- [Key Findings](#-key-findings)
- [Business Recommendations](#-business-recommendations)
- [Tech Stack](#-tech-stack)
- [How to Run](#-how-to-run)

---

## 🔍 Overview

This project builds a full customer churn prediction system for a banking context, modelled on real-world data science workflows. The core business problem: **identify which customers are most likely to leave before they do**, enabling targeted, cost-effective retention campaigns instead of reactive damage control.

The pipeline covers everything from merging five disparate data sources into a single master dataset, through exploratory analysis and preprocessing, to a tuned Random Forest model that scores every customer by churn probability — ready for CRM import.

---

## 🗂️ Tasks

### Phases Covered in This Repository

| Phase | Description |
|---|---|
| **Phase 1** | Data gathering — load and merge 5 Excel sheets on `CustomerID` |
| **Phase 2** | Exploratory Data Analysis — distributions, correlations, categorical rates, anomalies |
| **Phase 3** | Preprocessing — missing values, outlier capping, feature engineering, encoding, scaling |
| **Phase 4** | Model selection — Logistic Regression baseline vs Random Forest with GridSearchCV tuning |
| **Phase 5** | Evaluation — confusion matrix, ROC-AUC, Precision-Recall, Stratified K-Fold CV |
| **Phase 6** | Business recommendations — churn probability scoring, risk tiers, retention action mapping |

---

## 📊 Dataset

| Property | Value |
|---|---|
| Source | Customer_Churn_Data_Large.xlsx |
| Sheets | 5 (Demographics, Transactions, Service, Online Activity, Churn Status) |
| Total customers | 1,000 |
| Transaction records | 5,054 |
| Target variable | `ChurnStatus` (binary: 0 = Retained / 1 = Churned) |
| Class distribution | **79.6% retained / 20.4% churned** (imbalanced) |

**Sheet breakdown:**

| Sheet | Rows | Key Columns | Role |
|---|---|---|---|
| `Customer_Demographics` | 1,000 | Age, Gender, MaritalStatus, IncomeLevel | Base anchor table |
| `Transaction_History` | 5,054 | AmountSpent, ProductCategory, TransactionDate | RFM signals |
| `Customer_Service` | 1,002 | InteractionType, ResolutionStatus | Dissatisfaction signals |
| `Online_Activity` | 1,000 | LoginFrequency, LastLoginDate, ServiceUsage | Engagement signals |
| `Churn_Status` | 1,000 | ChurnStatus | Target variable |

---

## ⚙️ Methodology

```
5 Excel Sheets → Aggregation → Left Join Merge → EDA → Feature Engineering
→ Encoding → Scaling → SMOTE → Model Training → CV Evaluation → Risk Scoring
```

1. **Data gathering** — aggregated Transaction and Service sheets per CustomerID, then merged all 5 sheets via left join anchored on Demographics
2. **EDA** — churn distribution, spend histograms, login violin plots, unresolved rate box plots, correlation heatmap, categorical churn rates, anomaly flags
3. **Preprocessing** — missing value imputation, IQR outlier capping, log transformation on spend
4. **Feature engineering** — 3 new features derived from domain logic
5. **Encoding** — one-hot for nominal categories, ordinal for IncomeLevel
6. **Scaling** — StandardScaler applied to all continuous features
7. **Class balancing** — SMOTE applied to training set only (after train/test split)
8. **Modelling** — Logistic Regression baseline + GridSearchCV-tuned Random Forest
9. **Evaluation** — 5-Fold Stratified Cross-Validation, confusion matrix, ROC and Precision-Recall curves
10. **Scoring** — every customer assigned a churn probability and risk tier (Low / Medium / High)

---

## 🛠️ Feature Engineering

3 new features were constructed from cross-sheet domain logic:

| New Feature | Logic | Rationale |
|---|---|---|
| `unresolved_rate` | `unresolved_count / (total_interactions + 1)` | Normalises complaint burden by contact volume — more meaningful than raw count |
| `days_since_login` | `max(LastLoginDate) - LastLoginDate` in days | Converts date to numeric recency; longer inactivity = higher churn risk |
| `log_spend` | `log1p(total_spend)` | Reduces right skew in spend distribution; `log1p` handles zero-spend customers safely |

> `unresolved_count` and `complaint_count` ranked as the **strongest positive correlates with ChurnStatus** in the correlation heatmap, confirming that service quality features carry the most predictive signal.

---

## 🤖 Model & Evaluation

### Model Configuration

```python
RandomForestClassifier(
    n_estimators  = 200,        # tuned via GridSearchCV
    max_depth     = 10,         # regularisation
    class_weight  = "balanced", # compensates for 80/20 imbalance
    random_state  = 42,
    n_jobs        = -1
)
```

Logistic Regression was retained as the explainability benchmark — essential for banking stakeholders who require model transparency alongside predictive power.

### 5-Fold Stratified Cross-Validation Results

| Metric | Description | Priority |
|---|---|---|
| **ROC-AUC** | Overall discriminative power | ⭐ Primary |
| **Recall** | Fraction of actual churners caught | ⭐ Primary |
| **F1 Score** | Precision-Recall balance | Secondary |
| **Precision** | Accuracy of churn predictions | Secondary |
| **Accuracy** | Overall correct classifications | Reference only |

> **Priority metric: Recall + ROC-AUC.** In churn prediction, missing a churner (False Negative) costs more than a false alarm (False Positive). A missed churner means zero retention intervention and lost revenue. A false alarm costs only a retention offer.

### Top 10 Feature Importances

| Rank | Feature | Type | Importance |
|---|---|---|---|
| 🥇 1 | `unresolved_count` | Service | Highest |
| 🥈 2 | `complaint_count` | Service | High |
| 🥉 3 | `total_spend` | Transaction | High |
| 4 | `LoginFrequency` | Online Activity | Medium |
| 5 | `days_since_login` | Engineered | Medium |
| 6 | `transaction_count` | Transaction | Medium |
| 7 | `unresolved_rate` *(engineered)* | Engineered | Medium |
| 8 | `avg_spend` | Transaction | Low-Medium |
| 9 | `IncomeLevel` | Demographics | Low |
| 10 | `log_spend` *(engineered)* | Engineered | Low |

---

## 💡 Key Findings

- **Service quality is the dominant churn driver.** `unresolved_count` and `complaint_count` rank as the top two predictors. Customers with unresolved complaints have a median unresolved rate of 0.50 vs 0.33 for retained customers — a 17-point gap that directly signals dissatisfaction.

- **Low spend + low engagement = highest risk profile.** The scatter plot of LoginFrequency vs total_spend shows churned customers clustering densely in the bottom-left quadrant. Customers who neither spend much nor log in frequently have little reason to stay.

- **Top 5% spenders churn at 26%** — above the 20.4% average — indicating that high monetary value does not guarantee loyalty. These customers are high-priority for proactive retention.

- **Age is a weak standalone predictor.** The age distribution of churned vs retained customers overlaps heavily across 18–70, confirming that demographic signals alone are insufficient for churn prediction.

- **IncomeLevel and MaritalStatus show category-level variation.** Low-income and single/divorced customers churn at above-average rates, pointing to price sensitivity as a secondary driver.

- **Class imbalance is mild but consequential.** The 80/20 split means a naive majority-class classifier achieves 79.6% accuracy while catching zero churners. SMOTE on the training set and stratified splits were used to address this.

---

## 📣 Business Recommendations

| Churn Driver | Recommended Action |
|---|---|
| High `unresolved_count` | Proactive support outreach — resolve open tickets before escalation |
| Low `total_spend` | Loyalty reward offers to increase financial engagement |
| High `days_since_login` | Re-engagement email campaign for customers inactive 30+ days |
| High `complaint_count` | Fee waiver or service credit to address repeat dissatisfaction |
| Low `transaction_count` | Personalised product recommendations to increase purchase frequency |

**Risk tiers assigned:**
- 🔴 **High Risk** (churn probability ≥ 0.70) → Immediate retention campaign
- 🟡 **Medium Risk** (0.40–0.69) → Proactive monitoring + soft offer
- 🟢 **Low Risk** (< 0.40) → Standard engagement

All customers are scored and exported as `high_risk_customers.csv` for direct CRM import.

---

## 🧰 Tech Stack

| Category | Tools |
|---|---|
| Language | Python 3.10+ |
| Data Wrangling | `pandas`, `numpy` |
| Machine Learning | `scikit-learn` |
| Class Balancing | `imbalanced-learn` (SMOTE) |
| Visualisation | `matplotlib`, `seaborn` |
| Environment | Google Colab / Jupyter Notebook |

---

## ▶️ How to Run

**1. Clone the repository**
```bash
git clone https://github.com/1453nicat/customer-churn-prediction.git
cd customer-churn-prediction
```

**2. Install dependencies**
```bash
pip install pandas numpy matplotlib seaborn scikit-learn imbalanced-learn openpyxl jupyter
```

**3. Launch the notebook**
```bash
jupyter notebook LLOYDS.ipynb
```

> **Google Colab users:** Upload `Customer_Churn_Data_Large.xlsx` to `/content/` and run all cells in order.

---

<div align="center">

**Built as a portfolio project in customer churn prediction for banking**

[![GitHub](https://img.shields.io/badge/More%20Projects-GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/1453nicat)

</div>
