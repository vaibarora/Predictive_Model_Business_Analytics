# Predictive Model — IceCubed Ice Cream Maker

## Project Overview

This project is a **business analytics and predictive modeling** report developed as part of the **Product Marketing and Sales** effort for **IceCubed**, a compact ice cream maker. The goal is to identify the most significant variables influencing a customer's purchase decision and to recommend a high-potential target user group for marketing campaigns.

The analysis follows a two-phase approach:
- **Phase 1 (Previous Report):** Dataset exploration, data cleaning, and Exploratory Data Analysis (EDA) to identify Key Performance Indicators (KPIs).
- **Phase 2 (This Report):** Building a Logistic Regression model to predict sales likelihood and extract the key drivers of purchase behavior.

---

## Dataset

- **Source File:** `KickstarterData_Facts-1 (2)-1.xlsx`
- **Observations:** 10,000 customer records
- **Target Variable:** `Purchased` (binary — `1` = Purchase, `0` = No Purchase)
- **Original Features (12 columns):**
  - `Amount` — Deposit amount (numeric, range: 0–100)
  - `Weekly_IceCream` — Number of ice cream products consumed per week (numeric)
  - `Weekly_Desserts` — Number of desserts consumed per week (numeric)
  - `Gender` — Customer gender (female / male)
  - `Preferred Color of Device` — black, blue, red, silver, white, no preference
  - `Favorite Flavor of Ice Cream` — chocolate, vanilla, swirl, specialty, no preference
  - `Donated to Kick Starter Before` — yes / no
  - `Household Income` — <$50K, <$100K, >$100K, Not Reported
  - `Do you own a Keurig` — yes / no

**Data Quality:** The dataset contained **zero missing values** across all columns.

---

## Data Preprocessing & Feature Engineering

- **One-Hot Encoding (Dummy Variables):** All categorical features were dummy-encoded, expanding the dataset from 12 to **26 features** (3 numeric + 23 encoded categorical variables).
- **Feature Engineering:** A binary derived feature `Weekly_Desserts>8` was created to flag customers who consume more than 8 desserts per week — this was found to be a meaningful behavioral signal.
- **Train/Test Split:** 80/20 split — 8,000 training observations, 2,000 test observations.

---

## Methodology

### Feature Selection
- **Recursive Feature Elimination (RFE)** with a `LogisticRegression` estimator was used to select the **15 most important features** from the full 26-feature set.

### Model
- **Algorithm:** Logistic Regression (`GLM` with Binomial family and logit link function via `statsmodels`)
- This approach provides interpretable coefficients, z-statistics, and p-values for each predictor.

### Threshold Optimization
- **10 probability thresholds** (0.0 to 0.9) were evaluated to analyze the trade-off between Sensitivity and Specificity, helping determine the optimal classification cutoff.

---

## Model Performance

### Training Set (8,000 observations)

| Metric | Initial Model | Refined Model |
|---|---|---|
| Accuracy | 84.36% | 85.01% |
| Precision (Purchased) | — | 85% |
| Recall (Purchased) | — | 92% |
| Precision (Not Purchased) | — | 82% |
| Recall (Not Purchased) | — | 71% |

**Refined Model Confusion Matrix (Training):**
|  | Predicted: No | Predicted: Yes |
|---|---|---|
| Actual: No | 1,963 (TN) | 820 (FP) |
| Actual: Yes | 431 (FN) | 4,786 (TP) |

### Test Set (2,000 observations)

| Metric | Value |
|---|---|
| Accuracy | **85.65%** |
| F1-Score (Purchased) | 0.88 |
| F1-Score (Not Purchased) | 0.79 |

The test accuracy exceeds training accuracy, confirming **good generalization** with no overfitting.

### GLM Model Statistics

| Statistic | Value |
|---|---|
| AIC | 5,313.97 |
| BIC | -66,471.81 |
| Log-Likelihood | -2,641.0 |
| Null Model Log-Likelihood | -5,169.0 |
| Deviance | 5,282.0 |
| No. Observations | 8,000 |
| Df Model | 15 |

The dramatic improvement in Log-Likelihood from -5,169 (null) to -2,641 (model) confirms **excellent model fit**.

### Threshold Analysis

| Threshold | Accuracy | Sensitivity | Specificity |
|---|---|---|---|
| 0.4 | 84.24% | 95.34% | 63.42% |
| 0.5 | 84.00% | 92.94% | 63.90% |

Optimal accuracy is achieved at thresholds between 0.4–0.5.

---

## Key Findings — Most Significant Predictors

### Top Positive Predictors (increase purchase likelihood)

| Rank | Variable | Coefficient | z-Stat | p-Value |
|---|---|---|---|---|
| 1 | Gender: Female | +3.76 | +10.95 | <0.0001 |
| 2 | Amount (Deposit) | +1.12 | +20.28 | <0.0001 |
| 3 | Income: >$100K | +3.12 | +11.02 | <0.0001 |
| 4 | Flavor: Chocolate | +1.62 | +12.12 | <0.0001 |
| 5 | Weekly_Desserts>8 | +1.06 | +8.92 | <0.0001 |
| 6 | Weekly_IceCream | +0.34 | +7.09 | <0.0001 |
| 7 | Weekly_Desserts | +0.31 | +5.60 | <0.0001 |

### Top Negative Predictors (decrease purchase likelihood)

| Rank | Variable | Coefficient | z-Stat | p-Value |
|---|---|---|---|---|
| 1 | Color: No Preference | -5.47 | -31.82 | <0.0001 |
| 2 | Income: <$50K | -5.05 | -13.84 | <0.0001 |
| 3 | Flavor: No Preference | -2.31 | -21.03 | <0.0001 |
| 4 | Income: <$100K | -3.85 | -10.91 | <0.0001 |

---

## Conclusions & Recommendations

### Most Significant Factor
**No color preference** is the single strongest predictor of non-purchase (coefficient: -5.47). Customers who have a defined color preference are significantly more likely to buy — suggesting product aesthetics are a key purchase driver.

### Target Customer Profile (Highest Purchase Probability)
The model identifies the following high-potential customer segment:

- **Female** customers
- **Household income above $100K**
- **Chocolate or other defined flavor preferences** (not "no preference")
- **Regular ice cream and dessert consumers** (especially >8 desserts/week)
- **Higher deposit amounts** (correlated with higher commitment/intent)

### Marketing Recommendation
Focus marketing campaigns on **high-income female customers** who are already engaged dessert and ice cream consumers. Product color and flavor variety should be highlighted in marketing materials, as indecision on these attributes is a strong negative signal.

---

## Visualizations

The notebook includes the following visualizations:
- Correlation heatmap of all features
- Confusion matrix heatmap (with counts and percentages)
- Sensitivity vs. Specificity trade-off curve across probability thresholds
- Precision-Recall curve
- Performance metrics across different classification thresholds

---

## Project Files

| File | Description |
|---|---|
| `Predictive_Model_Ice_Creeam_Maker.ipynb` | Main Jupyter notebook with full analysis |
| `KickstarterData_Facts-1 (2)-1.xlsx` | Source dataset (10,000 customer records) |
| `README.md` | Project documentation (this file) |

---

## Tools & Libraries

- **Python** (Jupyter Notebook)
- `pandas`, `numpy` — Data manipulation
- `scikit-learn` — RFE, Logistic Regression, train/test split, classification metrics
- `statsmodels` — GLM with logit link for interpretable coefficients
- `matplotlib`, `seaborn` — Visualizations
