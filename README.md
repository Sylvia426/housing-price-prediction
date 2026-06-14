# Housing Prices Prediction Analysis

A statistical analysis of housing prices in **R**, using correlation analysis,
multiple linear regression, and ANOVA to identify which property features drive
price and to build a predictive model.

---

## Overview

This project investigates what determines house prices and builds a regression
model to predict them. Starting from a Kaggle housing dataset, it cleans the data,
explores feature–price relationships, checks for multicollinearity, and compares a
series of regression models using ANOVA to select the best-fitting specification.

**Dataset:** [Housing Prices Dataset](https://www.kaggle.com/datasets/yasserh/housing-prices-dataset)
— house-level records with features including area, bedrooms, bathrooms, stories,
parking, main-road access, air conditioning, furnishing status, and price.

---

## Key Findings

- **Area, bathrooms, and stories** show the strongest positive correlations with
  price and are the most influential predictors.
- **No problematic multicollinearity:** all predictors had **VIF < 5**, meaning
  each variable contributes largely unique information.
- **Model comparison via ANOVA:** three nested regression models were compared.
  The full model (all features) significantly improved fit over simpler models
  (p < 2.2e-16), and was selected as the final specification.
- The **full model explained ~66.7% of the variance** in house prices (R² ≈ 0.667),
  indicating reasonably strong predictive power.
- **Diagnostic checks** flagged mild departures from the homoscedasticity and
  normality assumptions and a few high-leverage points — documented as limitations.

---

## Methods

1. **Data cleaning** — handled missing values and treated outliers using the
   **IQR method** to reduce the influence of extreme observations.
2. **Correlation analysis** — computed a correlation matrix and heatmap to surface
   the features most strongly associated with price.
3. **Multiple linear regression** — modeled price as a function of property
   features; encoded categorical variables as factors.
4. **Multicollinearity check** — computed **VIF** for all predictors.
5. **Model selection via ANOVA** — compared three incremental models with an F-test
   to confirm that added predictors meaningfully improved explanatory power.
6. **Validation** — assessed the final model with diagnostic plots, coefficient
   hypothesis tests, **RMSE**, and **R²**.

---

## Tech Stack

`R` · `ggplot2` · `dplyr` · `car` (VIF) · `reshape2` · R Markdown

---

## Repository Contents

```
.
├── README.md
├── housing_price_prediction.Rmd    # R Markdown source (analysis + code)
├── housing_price_prediction.md     # Rendered report (charts render on GitHub)
└── housing_price_prediction_files/ # Chart images for the rendered report
```

---

## How to Run

```r
# 1. Download Housing.csv from the Kaggle link above and place it
#    in the same folder as the .Rmd file.

# 2. Install required packages
install.packages(c("ggplot2", "dplyr", "car", "reshape2", "rmarkdown"))

# 3. Open housing_price_prediction.Rmd in RStudio and click "Knit"
#    (or run: rmarkdown::render("housing_price_prediction.Rmd"))
```

---

## Full Analysis

See the complete analysis with all charts and results in
[housing_price_prediction.md](housing_price_prediction.md).

---

## Possible Extensions

- Address heteroscedasticity via log-transforming price or weighted least squares.
- Try regularized regression (Ridge / Lasso) and tree-based models for comparison.
- Add cross-validation for a more robust estimate of out-of-sample performance.

---

## Author

**Ruimin (Sylvia) Pei** — M.S. Business Analytics & AI, Johns Hopkins University
[LinkedIn](https://www.linkedin.com/in/ruiminpei/)
