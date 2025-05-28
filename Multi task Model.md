# Multi-Task Regression Report: FICO Score & Interest Rate Prediction

## Overview

This report revolves around building and tuning a multi-task regression model from LendingClub credit application data to simultaneously predict two targets:

* **FICO Score**
* **Interest Rate**

We explore improvements via feature engineering, data preprocessing, and model enhancement.

---

## Step 1: Baseline Multi-Task Ridge Regression

### Description:

* Employed only numeric features:

  * `credit_utilization`, `credit_history_years`, `total_delinquency`, `recent_inquiries`, `credit_mix`, `log_total_balance`
* Employed `StandardScaler` to scale inputs.
* Model: `MultiOutputRegressor(Ridge(alpha=1.0))`

### Performance:

| Target        | MAE     | R²     |
| ------------- | ------- | ------ |
| FICO Score    | 20.3531 | 0.3737 |
| Interest Rate | 0.0350  | 0.1360 |

### Insights:

* Good baseline for FICO score prediction.
* Interest rate prediction is poor due to missing required financial history.

## Step 2: Advanced Multi-Task Ridge Regression (Categorical + Transformed Features)

### Description:

* Added categorical features:

  * `term`, `grade`, `sub_grade`, `purpose`, `home_ownership`
* Added and transformed numeric features:

  * `loan_amnt`, `annual_inc`, `dti` → transformed to `log_loan_amnt`, `log_annual_inc`, `log_dti`
* One-hot encoded the categorical features.
* Normalized all the numerical features.

### Model:

* `MultiOutputRegressor(Ridge(alpha=1.0))`

### Performance:

| Target        | MAE     | R²     |
| ------------- | ------- | ------ |
| FICO Score    | 18.8510 | 0.4607 |
| Interest Rate | 0.0070  | 0.9558 |

### Insights:

* Both targets have considerably improved.
* Interest rate now well-modeled with extremely high accuracy due to use of proper loan data and encoded lending scenario.
* Ridge regression continuing to handle multicollinearity nicely.

---

## Summary Comparison

| Step                      | FICO MAE | FICO R² | IR MAE | IR R²  |
|---------------------------|----------|---------|--------|--------|
| ------------------------- | -------- | ------- | ------ | ------ |
| Baseline Ridge            | 20.3531  | 0.3737  | 0.0350 | 0.1360 |
| Enhanced Features + Ridge | 18.8510  | 0.4607  | 0.0070 | 0.9558 |

---

## What's Next

* Run **non-linear models**: XGBoost, LightGBM
* Use **cross-validation** to avoid overfitting
* Consider adding **interaction terms** or **PCA** to reduce dimensionality
* Include **rejected loan data** to get semi-supervised results

---

## Conclusion

Multi-task regression benefits greatly from thoughtful feature engineering and transformation. While FICO prediction continues to make incremental improvements, interest rate estimation improves by leaps and bounds when the model is supplied with more financial and categorical context.

We will next explore more expressive models like XGBoost to get performance to the next level.
