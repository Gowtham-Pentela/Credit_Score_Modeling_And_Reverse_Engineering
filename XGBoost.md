# XGBoost Multi-Target Regression Report (Target-Specific Models)

## Overview

The report encapsulates the building and performance of single XGBoost regression models to predict:

1. FICO Credit Score
2. Loan Interest Rate

Each of the models was trained independently to achieve higher accuracy in comparison with past multi-output and ridge regression models.

---

## Step: Independent XGBoost Models

### Motivation

Previous efforts with multi-output regressors provided modest gain for both targets. Optimizing individual targets as separate regression problems enables fine-tuning for better accuracy.

---

## Model 1: FICO Score Predictor

### Input Features:

* credit\\\\_utilization
* credit\\\\_history\\\\_years
* total\\\\_delinquency
* recent\\\\_inquiries
* credit\\\\_mix
* log\\\\_total\\\\_balance

### XGBoost Hyperparameters:

* n\\\\_estimators: 200
* max\\\\_depth: 3
* learning\\\\_rate: 0.1
* subsample: 1.0
* colsample\\\\_bytree: 1.0
* reg\\\\_lambda: 2
* reg\\\\_alpha: 0.5
* gamma: 1

### Performance:

* **MAE**: 17.71
* **R²**: 0.522

### Interpretation:

The model explains over half of the variance in FICO scores and reduces error by ~2.5 points compared to baseline regression methods.

---

## Model 2: Interest Rate Predictor

### Input Features:

* credit\_utilization
* credit\_history\_years
* total\_delinquency
* recent\_inquiries
* credit\_mix
* log\_total\_balance

### XGBoost Hyperparameters:

* n\_estimators: 150
* max\_depth: 4
* learning\_rate: 0.05
* subsample: 1.0
* colsample\_bytree: 1.0
* reg\_lambda: 1
* reg\_alpha: 0.3
* gamma: 0.5

### Performance:

* **MAE**: 0.0344
* **R²**: 0.1616

### Interpretation:

Though prediction error is low (MAE ~3.4%), explanatory power is still poor. The model only partially explains the correlation between interest rates and credit characteristics.

---

## Summary of Improvements

| Metric               | Previous Best | XGBoost (FICO) | XGBoost (Interest Rate) |
| -------------------- | ------------- | -------------- | ----------------------- |
| MAE (fico\_score)    | 17.64         | 17.71          | -                       |
| R² (fico\_score)     | 0.526         | 0.522          | -                       |
| MAE (interest_rate) | 0.0342        | -              | 0.0344                  |
| R² (interestrates) | 0.1665        | -              | 0.1616                  |

### Keycovery:
* Target-specific models individually did not outperform the earlier multi-task XGBoost on either metric.
* However, their interpretability and flexibility open room for personal tuning and interpretability.

---

## What to Do Next

* Explore **nonlinear feature interactions** via polynomial features or feature crossing
* Add **categorical variables** if available (e.g., state, employment status)
* Look into **semi-supervised learning** or **unsupervised clustering** for user segmentation
* Experiment with **ensemble stacking**: combine predictions of Ridge + XGBoost

This concludes the evaluation of isolated XGBoost models for two-target credit prediction.
