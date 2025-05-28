# OLS Regression Model: Step-by-Step Improvement Report

## Introduction

The following report documents the development and refinement of an OLS (Ordinary Least Squares) regression model that is intended to reverse-engineer FICO scores from LendingClub credit application data. Every refinement step is explained with motivation, methodology, and its impact on model performance.

---

## Step 1: Baseline Model (Raw Features)

### Description:

* Raw numeric and percentage features without any transformation or scaling.
* Used six attributes: `credit_utilization`, `credit_history_years`, `total_delinquency`, `recent_inquiries`, `credit_mix`, `total_balance`.

### Performance:

* **R²**: 0.3608
* **MAE**: 20.55
* **Condition Number**: ~210,000 (Extreme multicollinearity risk)

### Interpretation:

| Feature                | Coefficient | Interpretation                             |
| ---------------------- | ----------- | ------------------------------------------ |
| credit_utilization    | -72.28      | High utilization lowers score sharply      |
| credit_history_years | +0.57       | Longer history raises score slightly     |
| total_delinquency     | -8.57       | Strong penalty per delinquency             |
| recent_inquiries      | -3.88       | Moderate penalty per inquiry               |
| credit_mixed            | -0.43       | Surprise negative effect                 |
| total_balance         | +0.0002     | Effect appears small (unit-scale issue) |

---

## Step 2: Feature Scaling

### Description:

* Normalized all the features to have zero mean and unit variance using `StandardScaler`.
* Resolves scale problems and allows coefficients to be directly compared.

### Performance:

* **R²**: 0.3608 (unchanged)
* **MAE**: 20.55 (unchanged)
* **Condition Number**: 1.72 (Numerically stable)

### Interpretation:

| Feature                | Coefficient | Interpretation                            |
| ---------------------- | ----------- | ----------------------------------------- |
| credit_utilization    | -17.86      | Most harmful factor                      |
| total_delinquency     | -8.98       | Second strongest negative effect           |
| total_balance         | +5.20       | Importance after recovery rescaling       |
| credit_history_years | +4.38       | Positive contribution, moderate strength |
| credit_mix            | -3.31       | Still negative, maybe overcrediting |
| recent_inquiries      | -3.44       | Stable, slight penalty               |

### Insights:

* **Scaling** reported that `total_balance` was previously underweighted.
* Condition number grew substantially, removing multicollinearity problems.
* Coefficients now represent feature impact on the same scale.

---

## Step 3: Outlier Removal

### Description:

* Removed extreme outliers with z-score threshold of 3 from all features.
* ~14.4k rows (~6.4%) were identified and removed.

### Performance:

* **R²**: 0.3988 (Improved)
* **MAE**: 20.08 (Improved)
* **Condition Number**: 1.96 (Numerically stable)

### Interpretation:

| Feature              | Coefficient | Interpretation                                 |
| -------------------- | ----------- | --------------------------------------------------- |
| credit_utilization    | -18.98      | Less negative effect                            |
| total_delinquency     | -10.02      | More penalty per delinquency                    |
| total_balance       | +6.40       | Larger than before                          |
| credit_history_years | +4.49        | Stable, positive contribution                       |
| credit_mix            | -4.17        | More negative — possibly indicating over-leveraging |
| recent\_inquiries      | -3.18       | Still moderate penalty                              |

### Insights:

* Removing outliers **dramatically improved** R² and minimized MAE.
* Model is now more **resistant to extreme cases** and more generalizable.
* Coefficients tuned since the impact of extreme high/low values was removed.

---

## Step 4: Log Transformation

### Description:

* Performed log transformation of the `total_balance` feature to address right skewness and reduce the undue impact of extreme values.
* Used `log1p()` instead of log to prevent division by zero.

### Motivation:

* `total_balance` contains several orders of magnitude and was likely skewed.
* Log transforming compresses big values and improves model linearity. 

### Performance:

* **R²**: 0.4023 (Improved)
* **MAE**: 19.998 (Reduced compared to Unfiltered Data)
* **Condition Number**: 2.01 (Numerically stable)

### Interpretation:

| Feature                | Coefficient | Interpretation                                   |
| ---------------------- | ----------- | ------------------------------------------------ |
| credit\_utilization    | -19.26      | Still the most influential negative feature      |
| total\_delinquency     | -9.99       | Stable negative penalty                          |
| log_total_balance    | +6.88       | Strongest positive contributing feature                |
| credit_history_years | +4.56       | Slightly better than last stage                      |
| credit_mix          | -4.48       | Higher negative weight — over-leveraging risk |
| recent_inquiries      | -3.14       | Stable penalty                                   |

### Insights:

* The log transformation of `total_balance` yielded a **minor but consistent gain** in accuracy.
* This step improved the **feature interpretability** significantly by reducing scale distortion.
* Feature importance was slightly altered, giving more transparency to the effect of `log_total_balance`.

---

## Step 5: Ridge Regression (L2 Regularization)

### Description:

* Ridge regression adds an L2 penalty in order to pull large coefficients toward zero and reduce model complexity.
* Features are identical to the previous step: `credit_utilization`, `credit_history_years`, `total_delinquency`, `recent_inquiries`, `credit_mix`, `log_total_balance`
* Alpha: 1.0 (medium strength of regularization)

### Performance:

* **R²**: 0.4023
* **MAE**: 19.998

### Coefficients:

| Feature               | Coefficient |
| --------------------- | ----------- |
| credit\_utilization   | -19.2575    |
| credit\_history\_years | +4.5606     |
| total\_delinquency    | -9.9919     |
| recent\_inquiries     | -3.1392     |
| credit_mix       | -4.4827     |
| log_total_balance | +6.8806     |

### Insights:
* Results were **similar** to OLS after log transformation since the model is straightforward and features are standardized.
* Regularization **did not impact coefficients** at this level of alpha — demonstrates that multicollinearity is already controlled.
* Ridge will perform better with more features or noisy predictors.

---

## Step 6: ElasticNet Regression (L1 + L2 Regularization)

### Description:

* ElasticNet applies both L1 and L2 penalties to provide a balance between feature selection and coefficient shrinkage.
* Especially helpful when there are correlated features or sparsity is desired without giving up all of the features.

### Performance:

* **R²**: 0.3045 (Decreased)
* **MAE**: 21.4444 (Increased)

### Coefficients

| Feature                | Coefficient |
| ---------------------- | ----------- |
| credit\_utilization    | -10.9440    |
| credit\_history\_years | +2.3387     |
| total\_delinquency     | -5.5872     |
| recent\_inquiries      | -1.9468     |
| credit\_mix           | -0.8893     |
| log\_total\_balance   | +2.1796     |

### Insights:

* ElasticNet reduced all coefficients in magnitude, meaning regularization pressure.
* Performance declined in R² and MAE both from Ridge and OLS.
* Suggests to this model being over-penalizing the current feature set.
* Still valid when we introduce more features or higher dimensionality.

---

## Conclusion

Feature scaling, outlier removal, log transformation, and regularization using Ridge and ElasticNet have all contributed to the **stability, interpretability, and predictive capability** of the model. Ridge confirmed the stability of coefficients. ElasticNet illustrated the impact of stronger regularization on coefficients and overall performance. The model is now set for future upgrade, including non-linear models or more sophisticated feature engineering.
