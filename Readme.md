
# 📈 Reverse Engineering FICO Score — Model Comparison & Insights

## 🧠 Objective

The aim of this project is to reverse-engineer FICO credit scores and predict interest rates from LendingClub loan application data. Different regression approaches were utilized to examine how different modeling approaches capture credit behavior trends.

---

## 🧪 Models Compared

| Model Type       | FICO Score MAE | FICO Score R² | Interest Rate MAE | Interest Rate R² |
| ---------------- | -------------- | ------------- | ----------------- | ---------------- |
| OLS Regression   | 19.998         | 0.402         | N/A               | N/A              |
| Multi-Task Ridge | 18.851         | 0.460         | 0.0070            | 0.9558           |
| XGBoost (Tuned) | 17.710         | 0.522         | 0.0344            | 0.1616           |

---

## 🧩 What We Did

### Step 1: OLS Regression

* **Linear baseline** from standardized numerical features.
* Applied **log transformation** to `total_balance`.
* Outlier removal for stability.

**Findings**:

* Good interpretability.
* Limited by linear assumptions.

---


### Step 2: Multi-Task Ridge Regression

* Simultaneously modeled **FICO score and interest rate**.
* Added synthetic second target: interest rate.
* Used **feature scaling** and regularization (L2).

**Findings**:

* Strong correlation demonstrated by high R² for interest rate (0.95).
* Slight improvement in prediction of FICO.
* Catches up **shared structure** between targets.

---


### Step 3: XGBoost Regression

* **Transformed to non-linear tree-based models**.
* **Hyperparameters optimized** with GridSearchCV.
* Trained on standardized and log-transformation of features.

**Findings**:

* Best FICO MAE and R².
* Interest rate R² reduced — probably due to lower correlation with predictors.
* Handles **non-linearity** and interactions very well.

---

##  🔍 Key Insights

### ✅ What We Discovered:

* `credit_utilization` and `log_total_balance` are the most predictive.
* Regularization and outlier removal drastically improve linear model performance.
* Joint learning is efficient with highly correlated targets.
* XGBoost can learn **non-linear interactions** that linear models are not able to learn.

### ???? What to do next:

1. **Feature Engineering**:

   * Interaction features (e.g., `credit_utilization × credit_history_years`)
   * Binning `total_delinquency` into bins

2. **Explainability**:

   * Use SHAP or LIME to explain XGBoost predictions.

3. **Ensemble Models**:

   * Stack OLS + Ridge + XGBoost for solid predictions.

4. **Time-aware modeling**:

   * Include `issue_d` for seasonality and time series trends.

5. **Categorical Encoding**:

   * Attempt original categorical features using target or frequency encoding.

---


## Conclusion

This project illustrates how the integration of traditional statistical techniques and contemporary machine learning methods provides a more holistic interpretation of financial information. Each model introduced new insights into the determinants of credit scores and loan pricing.

---


