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
- [Feature Engineering](#-feature-engineering)
- [Model & Evaluation](#-model--evaluation)
- [Key Findings](#-key-findings)
- [Business Recommendations](#-business-recommendations)
- [Tech Stack](#-tech-stack)
- [How to Run](#-how-to-run)

---

## 🔍 Overview

This project builds a full customer churn prediction system for a banking context, modelled on real-world data science workflows as a part of the **LLOYDS Banking Group Data Science Job Simulation** hosted on [Forage](https://www.theforage.com/). The core business problem is **identifying which customers are most likely to leave before they do**, enabling targeted, cost-effective retention campaigns instead of reactive damage control. Also, the pipeline covers everything from merging five disparate data sources into a single master dataset, through exploratory analysis and preprocessing.

---

## 🗂️ Tasks

### Phases Covered in This Repository

| Phase | Description |
|---|---|
| **Phase 1** | Data gathering - load and merge 5 Excel sheets on `CustomerID` |
| **Phase 2** | Exploratory Data Analysis - distributions, correlations, categorical rates, anomalies |
| **Phase 3** | Preprocessing - missing values, outlier capping, feature engineering, encoding, scaling |
| **Phase 4** | Model selection - Logistic Regression baseline vs Random Forest with GridSearchCV tuning |
| **Phase 5** | Evaluation - confusion matrix, ROC-AUC, Precision-Recall, Stratified K-Fold CV |
| **Phase 6** | Business recommendations - churn probability scoring, risk tiers, retention action mapping |

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

## 🛠️ Feature Engineering

3 new features were constructed from cross-sheet domain logic:

| New Feature | Logic | Rationale |
|---|---|---|
| `unresolved_rate` | `unresolved_count / (total_interactions + 1)` | Normalises complaint burden by contact volume - more meaningful than raw count |
| `days_since_login` | `max(LastLoginDate) - LastLoginDate` in days | Converts date to numeric recency; longer inactivity means higher churn risk |
| `log_spend` | `log1p(total_spend)` | Reduces right skew in spend distribution; `log1p` handles zero-spend customers safely |

> `unresolved_count` and `complaint_count` ranked as the **strongest positive correlates with ChurnStatus** in the correlation heatmap, confirming that service quality features carry the most predictive signal.

---

## 🤖 Model & Evaluation

### 5-Fold Stratified Cross-Validation Results

| Metric | Description | Priority |
|---|---|---|
| **ROC-AUC** | Overall discriminative power | ⭐ Primary |
| **Recall** | Fraction of actual churners caught | ⭐ Primary |
| **F1 Score** | Precision-Recall balance | Secondary |
| **Precision** | Accuracy of churn predictions | Secondary |
| **Accuracy** | Overall correct classifications | Reference only |

> **Priority metric: Recall and ROC-AUC.** In churn prediction, missing a churner (False Negative) costs more than a false alarm (False Positive). A missed churner means zero retention intervention and lost revenue. A false alarm costs only a retention offer.

---

## 💡 Key Findings

- **Service quality is the dominant churn driver** (`unresolved_count` and `complaint_count`). Customers with unresolved complaints have a median unresolved rate of 0.50 vs 0.33 for retained customers.

- **Low spend + low engagement = highest risk profile.** The scatter plot of LoginFrequency vs total_spend shows churned customers clustering densely in the bottom-left quadrant. Customers who neither spend much nor log in frequently have little reason to stay.

- **Top 5% spenders churn at 26%** indicating that high monetary value does not guarantee loyalty. These customers are high-priority for proactive retention.

- **IncomeLevel and MaritalStatus show category-level variation.** Low-income and single/divorced customers churn at above-average rates, pointing to price sensitivity as a secondary driver.

- **Class imbalance is mild but consequential.** The 80/20 split means a naive majority-class classifier achieves 79.6% accuracy while catching zero churners. SMOTE on the training set and stratified splits were used to address this.

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
## 🏆 Certificate

[**LLOYDS Banking Group Data Science Job Simulation** - Forage](https://www.theforage.com/completion-certificates/Zbnc2o4ok6kD2NEXx/EuvC8GPjkZ6xaiP9p_Zbnc2o4ok6kD2NEXx_699b43f63b2e4c13b63d62c9_1777102463356_completion_certificate.pdf)
📅 Completed: **April 25, 2026**
---

<div align="center">

**Built as part of the British Airways × Forage Job Simulation**

[![GitHub](https://img.shields.io/badge/More%20Projects-GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/1453nicat)

</div>
