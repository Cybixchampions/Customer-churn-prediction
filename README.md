# Customer Churn Prediction Using Machine Learning

> An end-to-end machine learning project that predicts telecom customer churn and converts model probabilities into actionable customer-risk segments.

[![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python)](https://www.python.org/)
[![Scikit-learn](https://img.shields.io/badge/Scikit--learn-ML-orange?logo=scikit-learn)](https://scikit-learn.org/)
[![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-150458?logo=pandas)](https://pandas.pydata.org/)

## 📌 Project Overview

Customer churn is a major business problem for subscription-based companies because losing existing customers can reduce recurring revenue and increase acquisition costs.

The goal of this project is to:

1. Understand customer churn patterns.
2. Build and compare multiple classification models.
3. Tune the final model using cross-validation.
4. Optimize the probability threshold for a retention-focused use case.
5. Identify important predictive features.
6. Generate customer-level churn probabilities and risk segments.

**Dataset:** 7,043 telecom customers and 33 original variables  
**Target:** `Churn Label`  
**Churn rate:** ~26.5%

---

## 🎯 Business Objective

Instead of simply asking:

> "Will this customer churn?"

the project focuses on a more useful business question:

> "Which customers have a high probability of churning, so that the retention team can prioritize them?"

This makes **recall, F1-score, ROC-AUC and probability thresholds** important evaluation considerations—not accuracy alone.

---

## 🧠 Machine Learning Workflow

```text
Raw Customer Data
       ↓
Data Quality Checks
       ↓
Cleaning & Leakage Prevention
       ↓
Exploratory Data Analysis
       ↓
Feature Selection
       ↓
Encoding + Scaling
       ↓
Train/Test Split
       ↓
Model Comparison
       ↓
Hyperparameter Tuning
       ↓
Threshold Optimization
       ↓
Model Interpretation
       ↓
Customer Risk Scoring
```

---

## 🧹 Data Preparation

The dataset contains customer demographics, services, contract information, billing information and churn-related fields.

### Leakage prevention

The following columns were excluded because they are identifiers, redundant/location fields, or contain information that would not be appropriate as predictive inputs for a pre-churn model:

- `CustomerID`
- `Count`
- `Country`
- `State`
- `City`
- `Zip Code`
- `Lat Long`
- `Latitude`
- `Longitude`
- `Churn Value`
- `Churn Score`
- `Churn Reason`

`Churn Label` was used as the target variable.

`Total Charges` was converted to numeric format before modeling.

---

## 🔬 Models Compared

Four classification algorithms were evaluated:

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Logistic Regression | 0.8020 | 0.6426 | 0.5722 | 0.6054 | 0.8489 |
| Decision Tree | 0.7935 | 0.6143 | 0.5963 | 0.6052 | 0.8345 |
| Random Forest | 0.7942 | 0.6373 | 0.5214 | 0.5735 | 0.8396 |
| Gradient Boosting | **0.8041** | **0.6601** | 0.5401 | 0.5941 | **0.8526** |

The comparison shows that Gradient Boosting achieved the highest test ROC-AUC among the initial benchmark models.

However, **Logistic Regression was selected as the final model** because it offered strong discrimination while remaining highly interpretable, and its hyperparameters were further optimized using 5-fold cross-validation.

---

## ⚙️ Hyperparameter Tuning

GridSearchCV was used to tune Logistic Regression.

### Best parameters

```text
C = 100
penalty = l2
solver = liblinear
```

### Best cross-validation ROC-AUC

**0.8590**

---

## 🎚️ Probability Threshold Optimization

The default classification threshold of 0.50 is not necessarily optimal for a churn-retention problem.

Several thresholds were tested:

| Threshold | Precision | Recall | F1 |
|---:|---:|---:|---:|
| 0.30 | 0.5303 | 0.7487 | 0.6208 |
| **0.35** | **0.5649** | **0.7219** | **0.6338** |
| 0.40 | 0.5770 | 0.6711 | 0.6205 |
| 0.45 | 0.6068 | 0.6230 | 0.6148 |
| 0.50 | 0.6302 | 0.5695 | 0.5983 |

A threshold of **0.35** was selected because it produced the highest F1-score among the tested thresholds while substantially improving recall compared with the default 0.50 threshold.

---

## 📊 Final Model Performance

Using the tuned Logistic Regression model with a **0.35 decision threshold**:

| Metric | Score |
|---|---:|
| Accuracy | **77.86%** |
| Precision | **56.49%** |
| Recall | **72.19%** |
| F1 Score | **63.38%** |
| ROC-AUC | **84.77%** |

### Interpretation

The model identifies approximately **72% of actual churners in the test set** at the selected threshold.

For a retention campaign, this higher recall can be useful because missing a customer who is likely to churn may be more costly than contacting some customers who ultimately stay.

The appropriate production threshold should ultimately be determined using real retention costs, customer lifetime value and campaign capacity.

---

## 🔍 Feature Interpretation

The Logistic Regression coefficients provide an interpretable view of which variables are most strongly associated with the prediction.

Some of the strongest signals in this model include:

- Monthly Charges
- Internet Service
- Tenure Months
- Dependents
- Contract type
- Online Security
- Payment/billing-related variables
- Streaming services

### Important caution

These coefficients represent **predictive associations**, not causal relationships.

For example, a feature associated with higher churn does not automatically mean changing that feature will cause churn to decrease.

---

## 💼 Business Recommendations

Based on the model and feature analysis, a telecom retention team could consider:

### 1. Prioritize high-risk customers

Use predicted churn probability to create a ranked retention list.

### 2. Focus on early-tenure customers

Customers with shorter tenure may benefit from stronger onboarding and engagement programs.

### 3. Investigate month-to-month contracts

Contract structure is an important predictive signal and may identify customers with greater switching flexibility.

### 4. Investigate internet-service segments

The model shows meaningful differences across internet-service categories. These segments should be investigated further for service quality, pricing and customer-experience issues.

### 5. Use targeted retention offers

Instead of offering incentives to every customer, target customers based on predicted risk and expected customer lifetime value.

---

## 📈 Visual Results

### ROC Curve

![ROC Curve](reports/roc_curve.png)

### Confusion Matrix

![Confusion Matrix](reports/confusion_matrix.png)

### Feature Importance

![Feature Importance](reports/feature_importance.png)

### Threshold Trade-off

![Threshold Trade-off](reports/threshold_tradeoff.png)

---

## 📁 Repository Structure

```text
customer-churn-prediction/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── data/
│   ├── telco_customer_churn.xlsx
│   └── README.md
│
├── notebooks/
│   └── customer_churn_analysis.ipynb
│
├── models/
│   └── customer_churn_model.pkl
│
├── outputs/
│   ├── customer_churn_predictions.csv
│   ├── feature_importance.csv
│   ├── model_comparison.csv
│   └── threshold_analysis.csv
│
└── reports/
    ├── confusion_matrix.png
    ├── feature_importance.png
    ├── roc_curve.png
    └── threshold_tradeoff.png
```

---

## 🛠️ Technologies Used

- **Python**
- **Pandas**
- **NumPy**
- **Matplotlib**
- **Seaborn**
- **Scikit-learn**
- **Jupyter Notebook**
- **Joblib**

---

## 🚀 How to Run the Project

### 1. Clone the repository

```bash
git clone https://github.com/YOUR-USERNAME/customer-churn-prediction.git
cd customer-churn-prediction
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Open the notebook

```bash
jupyter notebook
```

Then open:

```text
notebooks/customer_churn_analysis.ipynb
```

Run the cells from top to bottom.

---

## 📦 Main Deliverables

| File | Purpose |
|---|---|
| `customer_churn_analysis.ipynb` | Complete analysis and modeling workflow |
| `customer_churn_model.pkl` | Trained Logistic Regression pipeline |
| `customer_churn_predictions.csv` | Customer-level churn probabilities |
| `model_comparison.csv` | Benchmark results |
| `feature_importance.csv` | Model coefficient analysis |
| `threshold_analysis.csv` | Threshold performance comparison |
| `reports/*.png` | Portfolio-ready visualizations |

---

## ⚠️ Limitations & Future Improvements

This project is designed as a portfolio/learning project.

Potential next steps for a production-grade solution:

- Hyperparameter tuning for additional algorithms
- Class imbalance strategies
- Probability calibration
- SHAP-based model explainability
- Cost-sensitive threshold optimization
- Customer Lifetime Value integration
- Model monitoring and drift detection
- Automated retraining pipeline
- Deployment through an API or batch scoring system
- A/B testing of retention interventions

---

## 👤 Author

**Soumen Maji**

This project demonstrates practical skills in:

**Python • Data Analysis • Exploratory Data Analysis • Feature Engineering • Classification • Model Evaluation • Hyperparameter Tuning • Predictive Analytics**

---

## ⭐ If you found this project useful

Feel free to explore the notebook, review the modeling decisions, and use the project as a reference for building your own end-to-end machine learning workflow.
