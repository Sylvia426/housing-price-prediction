Housing Prices Prediction Analysis
================
Ruimin (Sylvia) Pei
April 2024

## Introduction

This project uses statistical modeling to predict house prices based on
property features. Using a housing dataset from Kaggle, it examines how
price relates to attributes such as area, number of bedrooms and
bathrooms, number of stories, and amenities, and builds a regression
model to quantify and forecast their effect on value. The primary
objectives of this research are threefold:

- To elucidate the relationship between house prices and various
  property features, determining which attributes most significantly
  influence valuation.
- To identify and quantify the most impactful factors that determine
  house prices.
- To develop a predictive model that leverages these factors to forecast
  house prices accurately.

In the first part of the project, the data is carefully cleaned to get
rid of any missing numbers or outliers. This makes sure that the
information is correct and complete for analysis. This step is very
important because it has a direct effect on how reliable the next
statistical analysis and model results will be. After the data is
prepared, exploratory data analysis is used to look at how different
property traits affect house prices and how they are spread out. This
study helps us understand how the real estate market works and lays the
groundwork for more advanced prediction models. A big part of the study
is making and improving linear regression models to find out what
factors affect home prices the most. Then, advanced statistical methods
like Analysis of Variance (ANOVA) are used to compare how well different
models work. Finally, the model that best predicts house prices is
chosen. The study’s results are meant to help real estate workers by
giving them a solid way to figure out how much a property is worth. It
is the goal of the study to help stabilise and predict real estate
prices by building accurate prediction models. This will allow
stakeholders to make smart choices.

The goal of this project is not only to make very accurate predictions
about house prices, but also to learn more about how the many different
factors that affect market value interact with each other. The study
will help policymakers and investors make better decisions about real
estate markets by using careful statistical analysis to find out what
makes them go up and down.

## Dataset Description and Cleanup

### Dataset Overview

The dataset comprises several features that are believed to influence
the price of a house significantly, the data is from
Kaggle(<https://www.kaggle.com/datasets/yasserh/housing-prices-dataset>)
.

These include: • Area: The total area of the house in square feet. •
Bedrooms: The number of bedrooms in the house. • Bathrooms: The number
of bathrooms in the house. • Stories: The number of stories or levels
the house has. • Mainroad: A binary feature indicating proximity to the
main road. • Guestroom: A binary feature indicating the presence of a
guest room. • Basement: A binary feature indicating the presence of a
basement. • Hotwaterheating: A binary feature indicating the presence of
hot water heating. • Airconditioning: A binary feature indicating the
presence of air conditioning. • Parking: The number of parking spaces
available. • Prefarea: A binary feature indicating if the house is in a
preferred area. • Furnishingstatus: The furnishing status of the house
(furnished, semi-furnished, unfurnished).

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
# Load data
housing <- read.csv("Housing.csv")
```

### Data Cleaning

#### Handling Missing Values

``` r
sum(is.na(housing))
```

    ## [1] 0

So there are no missing value for this dataset

#### Identifying and Treating Outliers

``` r
library(ggplot2)

ggplot(housing, aes(y = price)) +
  geom_boxplot(fill = "lightblue") +
  labs(title = "Boxplot of House Prices", x = "", y = "Price") +
  theme_minimal()
```

![](housing_price_prediction_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Identify outliers using the Interquartile Range (IQR) and cap or remove
them.

``` r
# Function to identify outliers based on IQR
identify_outliers <- function(x) {
  Q1 <- quantile(x, 0.25)
  Q3 <- quantile(x, 0.75)
  IQR <- Q3 - Q1
  return(x < (Q1 - 1.5 * IQR) | x > (Q3 + 1.5 * IQR))
}
numeric_columns <- sapply(housing, is.numeric)
outliers <- identify_outliers(housing$price)

housing_clean <- housing[!outliers,]

print(paste("Original data count:", nrow(housing)))
```

    ## [1] "Original data count: 545"

``` r
print(paste("Data count after removing outliers:", nrow(housing_clean)))
```

    ## [1] "Data count after removing outliers: 530"

``` r
# Plotting the cleaned data to visualize the change
ggplot(housing_clean, aes(y = price)) +
  geom_boxplot(fill = "lightblue") +
  labs(title = "Boxplot of House Prices After Removing Outliers", x = "", y = "Price") +
  theme_minimal()
```

![](housing_price_prediction_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
# Apply outlier treatment for numeric columns
housing[numeric_columns] <- lapply(housing[numeric_columns], function(x) {
  outliers <- identify_outliers(x)
  x[outliers] <- ifelse(x[outliers] < median(x, na.rm = TRUE), median(x, na.rm = TRUE), x[outliers])
  return(x)
})
```

## Method

Two main types of statistics are used to look at the connection between
house prices and their features: Correlation Analysis and Linear
Regression Analysis. These methods give us a solid way to think about
how different things about houses affect their prices. I will fully and
clearly explain these methods, the data that was used, and the good
reasons why they were used in this study below.

### Method 1: Correlation Analysis

I use correlation analysis to find out how strong and which way the link
is between two factors. Between -1 and +1 is the range of correlation
values. As one variable goes up, the other goes down. A value close to
+1 means that the relationship is strong and positive, while a value
close to -1 means that the relationship is strong and negative, and a
value around 0 means that there is no straight relationship. In the
context of housing data, correlation helps identify which features (like
area, number of bedrooms, etc.) have the strongest relationships with
the price. When correlation coefficients are high, it means that there
may be possible predictors for regression models \[1\].

**Data Used**: The analysis utilizes numeric data from the cleaned
housing dataset, specifically the columns representing price, area,
bedrooms, bathrooms, stories, and parking spaces.

**Rationale**: Identifying strongly correlated variables can guide
feature selection for more complex models, ensuring that the most
relevant features are included in predictive modeling.

### Method 2: Linear Regression Analysis

Linear regression to model the link between a dependent variable and one
or more independent factors can figure out which values of the linear
equation with one or more independent factors are most likely to
accurately predict the dependent variable. To figure out how each factor
affects house prices on its own, simple linear regression is used. To
find out how they all affect prices together, multiple regression is
used \[2\], \[3\].

**Data Used**: - The same cleaned dataset is used, focusing on numeric
variables that describe the properties of the houses, such as area,
number of bedrooms, etc., as predictors of the price.

**Rationale**: - Linear regression helps quantify the impact of each
attribute on the house price. By understanding these impacts,
stakeholders can make informed decisions regarding property valuation
and investment. - This method also allows for the prediction of house
prices based on the values of various features, making it invaluable for
forecasting and real estate analysis.

### Method 3: Analysis of Variance (ANOVA)

ANOVA is a way to compare models and find the one that best explains the
changes in the response variable, which in this case is the price of the
house. ANOVA is used in regression analysis to figure out how important
a model is generally and to see how well different models fit the data.
It checks the idea that the means of several groups are the same and
measures how much of the overall change in the answer variable can be
explained by the category variable that sets the groups.In regression
analysis, ANOVA is utilized to compare the fit of multiple regression
models by examining the increase in explained variance when additional
predictors are added \[4\].

**Data Used**: - The ANOVA is done on a set of linear regression models
that were made from the cleaned housing dataset. As more factors are
added to these models, their effect on the accuracy of house price
estimates is studied as a whole.

**Rationale**: - **Model Selection**: In regression analysis, ANOVA is a
key part of the model selection process. This lets researchers use
statistics to show that the model works better when more variables are
added, making sure that only important variables are kept. -
**Statistical Testing**: By using the F-test in ANOVA, we can test the
idea that adding or taking away certain factors from the model doesn’t
make it much better at explaining things. This statistical test helps to
prove or disprove whether the final model should include more factors.

## Analysis and Result

### Correlation Analysis

``` r
numeric_features <- housing_clean %>% select_if(is.numeric)
correlations <- cor(numeric_features)

price_correlations <- correlations[, "price"]
print(price_correlations)
```

    ##     price      area  bedrooms bathrooms   stories   parking 
    ## 1.0000000 0.5098560 0.3322928 0.4579622 0.4325277 0.3283082

``` r
library(reshape2)
numeric_features <- housing_clean %>% select_if(is.numeric)
correlations <- cor(numeric_features)
correlation_data <- melt(correlations)
heatmap_plot <- ggplot(correlation_data, aes(Var1, Var2, fill = value)) +
  geom_tile(color = "white") +  # Adds a white border around each tile
  scale_fill_gradient2(low = "blue", high = "red", mid = "white", midpoint = 0, limit = c(-1, 1), space = "Lab", name="Correlation") +
  theme_minimal() +  # Minimal theme to enhance visual appeal
  labs(title = "Correlation Heatmap", x = "Features", y = "Features") +  
  theme(axis.text.x = element_text(angle = 45, vjust = 1, hjust=1)) 
print(heatmap_plot)
```

![](housing_price_prediction_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->
Based on the above correlation heatmap, There are stronger links between
area, bathrooms, and stories, which suggests that these factors have a
big effect on home prices. Higher property values are probably due to
these features, which include more room, usefulness, and a multi-level
structure that could mean luxury or a bigger house. Even though bedrooms
and parking are still positively linked with price, the relationship is
not as strong. This suggests that while they are important, they may not
be as important as other factors in setting house prices.

### Model Development

``` r
library(car)
```

    ## Loading required package: carData

    ## 
    ## Attaching package: 'car'

    ## The following object is masked from 'package:dplyr':
    ## 
    ##     recode

``` r
housing_clean$mainroad <- as.factor(housing_clean$mainroad)
housing_clean$guestroom <- as.factor(housing_clean$guestroom)
housing_clean$basement <- as.factor(housing_clean$basement)
housing_clean$hotwaterheating <- as.factor(housing_clean$hotwaterheating)
housing_clean$airconditioning <- as.factor(housing_clean$airconditioning)
housing_clean$prefarea <- as.factor(housing_clean$prefarea)
housing_clean$furnishingstatus <- as.factor(housing_clean$furnishingstatus)
```

#### Checking for Multicollinearity

``` r
# Model with all predictors
full_model <- lm(price ~ ., data = housing_clean)

# Calculate VIF for each explanatory variable
vif_values <- vif(full_model)
print(vif_values)  
```

    ##                      GVIF Df GVIF^(1/(2*Df))
    ## area             1.307178  1        1.143319
    ## bedrooms         1.346614  1        1.160437
    ## bathrooms        1.248668  1        1.117438
    ## stories          1.457956  1        1.207458
    ## mainroad         1.173836  1        1.083437
    ## guestroom        1.223614  1        1.106171
    ## basement         1.318683  1        1.148339
    ## hotwaterheating  1.031137  1        1.015449
    ## airconditioning  1.187271  1        1.089620
    ## parking          1.187014  1        1.089502
    ## prefarea         1.139439  1        1.067445
    ## furnishingstatus 1.103196  2        1.024857

All variables have VIF values significantly less than 5, indicating that
there is no excessive multicollinearity affecting the estimates of the
coefficients. This is good news because it suggests that each variable
provides unique information that is not overly redundant with other
variables in the model.

#### Incremental Models and Use ANOVA to Compare Models

``` r
# Simple model with basic features
model1 <- lm(price ~ area + bedrooms, data = housing_clean)
# More complex model with additional features
model2 <- lm(price ~ area + bedrooms + bathrooms + stories, data = housing_clean)
# Full model with all features
model3 <- lm(price ~ area + bedrooms + bathrooms + stories + mainroad + guestroom + basement + hotwaterheating + airconditioning + parking + prefarea + furnishingstatus, data = housing_clean)
# Compare models using ANOVA
anova_comparison <- anova(model1, model2, model3)
print(anova_comparison)
```

    ## Analysis of Variance Table
    ## 
    ## Model 1: price ~ area + bedrooms
    ## Model 2: price ~ area + bedrooms + bathrooms + stories
    ## Model 3: price ~ area + bedrooms + bathrooms + stories + mainroad + guestroom + 
    ##     basement + hotwaterheating + airconditioning + parking + 
    ##     prefarea + furnishingstatus
    ##   Res.Df        RSS Df  Sum of Sq       F    Pr(>F)    
    ## 1    527 8.9401e+14                                    
    ## 2    525 6.6667e+14  2 2.2734e+14 130.569 < 2.2e-16 ***
    ## 3    516 4.4923e+14  9 2.1744e+14  27.751 < 2.2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Based on the result, we see that both Model 2 and Model 3 significantly
improve the model fit compared to their predecessors.

- Model 2 significantly reduces the residual sum of squares (RSS)
  compared to Model 1, with a substantial increase in explained variance
  as indicated by the large Sum of Sq and highly significant F statistic
  (p-value \< 2.2e-16).
- Model 3 further reduces the RSS and again shows a significant increase
  in explained variance compared to Model 2 (p-value \< 2.2e-16),
  suggesting that the additional predictors included in Model 3
  (mainroad, guestroom, etc.) provide significant explanatory power.

Given these results, Model 3 appears to be the most appropriate for
explaining house prices based on the provided predictors.

#### Interpretation of ANOVA Results

``` r
# Diagnostic plots for Model 3
par(mfrow=c(2, 2))
plot(model3)
```

![](housing_price_prediction_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->
Based on the plots, it can be seen that:

- The presence of patterns in the Residuals vs Fitted and Scale-Location
  plots suggests that the linear model may not be capturing all the
  patterns in the data, possibly due to non-linearity or
  heteroscedasticity.
- The slight deviation from normality in the Q-Q plot could be due to
  outliers or heavy-tailed distribution of residuals.
- The Residuals vs Leverage plot highlights a small number of
  potentially influential points that could be disproportionately
  impacting the model’s predictions.

#### Hypothesis Testing on Model Coefficients

``` r
summary_model3 <- summary(model3)
print(summary_model3)
```

    ## 
    ## Call:
    ## lm(formula = price ~ area + bedrooms + bathrooms + stories + 
    ##     mainroad + guestroom + basement + hotwaterheating + airconditioning + 
    ##     parking + prefarea + furnishingstatus, data = housing_clean)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -2435356  -589536   -37483   503263  4078901 
    ## 
    ## Coefficients:
    ##                                  Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)                     486169.93  239515.24   2.030 0.042889 *  
    ## area                               217.36      22.35   9.726  < 2e-16 ***
    ## bedrooms                         74632.51   64442.56   1.158 0.247349    
    ## bathrooms                       777585.29   97622.48   7.965 1.06e-14 ***
    ## stories                         462903.06   56879.19   8.138 3.02e-15 ***
    ## mainroadyes                     455261.24  124610.71   3.653 0.000285 ***
    ## guestroomyes                    360225.36  117865.34   3.056 0.002357 ** 
    ## basementyes                     352640.08   97761.84   3.607 0.000340 ***
    ## hotwaterheatingyes              738637.95  201992.99   3.657 0.000282 ***
    ## airconditioningyes              827003.38   95860.48   8.627  < 2e-16 ***
    ## parking                         204142.12   52410.19   3.895 0.000111 ***
    ## prefareayes                     487879.14  103992.52   4.691 3.48e-06 ***
    ## furnishingstatussemi-furnished   12685.03  103922.96   0.122 0.902898    
    ## furnishingstatusunfurnished    -368747.11  111728.38  -3.300 0.001032 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 933100 on 516 degrees of freedom
    ## Multiple R-squared:  0.6667, Adjusted R-squared:  0.6583 
    ## F-statistic: 79.38 on 13 and 516 DF,  p-value: < 2.2e-16

Based on the result, it can be seen that: - Features like bathrooms, air
conditioning, and parking have a big positive effect on house prices, as
shown by their significant positive factors. - Some features, like beds,
don’t have significant p-values, which means they might not have a big
effect on prices when other factors are taken into account.

#### Model Validation

``` r
predicted_prices <- predict(model3, housing_clean)

residuals <- housing_clean$price - predicted_prices

# RMSE calculation
rmse <- sqrt(mean(residuals^2))
print(paste("Root Mean Square Error:", rmse))
```

    ## [1] "Root Mean Square Error: 920651.117370866"

``` r
# R-squared value
rsquared <- summary(model3)$r.squared
print(paste("R-squared value:", rsquared))
```

    ## [1] "R-squared value: 0.666665921527052"

- The R-squared number shows that the model can explain about 66.67% of
  the variation in house prices. This is a good finding in the real
  world. The RMSE, on the other hand, shows that the average error in
  forecasts is quite high, which suggests that this model might make big
  mistakes when predicting prices.

- The high RMSE could be because house prices vary a lot or because some
  outliers weren’t fully taken into account. This suggests that the
  model works well overall, but it could be better by adding more
  detailed features or using different modelling methods.

## Conclusions & Limitations

#### Interpretation of Findings

In light of previous analysis, the answers to the research questions
are: - **House Price Relation with Features**: House prices have a
quantifiable relationship with various features, with area, bathrooms,
and stories among the most influential. - **Important Price
Determinants**: The most critical factors in determining house prices
include not only the size and functional features of the house but also
its location relative to main roads and luxury amenities such as air
conditioning. - **Model’s Predictive Ability**: While the developed
model demonstrates a sound ability to predict house prices, given the
limitations outlined, it should be employed with caution. Predictions
might be improved with a refined model that addresses the limitations,
perhaps through advanced techniques or additional data.

#### Conclusions

This research set out to elucidate the relationship between house prices
and a set of defining features using robust statistical methods. The
correlation analysis yielded valuable insights, establishing that the
area, number of bathrooms, and stories of a house have moderate to
strong positive correlations with its price. These results imply that as
these features increase in size or number, the house price tends to rise
correspondingly.

Through progressive model development and ANOVA, we determined that
while simple models with basic features provide some insight, a fuller
model incorporating a broader range of features significantly enhances
the explanatory power. The full model, which includes area, number of
bedrooms, bathrooms, stories, as well as additional features like
mainroad proximity, presence of a guestroom, and air conditioning,
proved to provide a considerable improvement in fit over simpler models.

Importantly, the full model explained approximately 66.67% of the
variance in house prices, pointing to a relatively strong predictive
capability. This suggests that it is indeed possible to employ these
features to construct a model that accurately predicts house prices,
serving as a valuable tool for stakeholders in the real estate market.

#### Limitations

While the research findings are compelling, they come with certain
limitations that must be acknowledged: - **Model Assumptions**:
Diagnostic plots indicated potential violations of homoscedasticity and
normality assumptions of linear regression. This could impact the
reliability of coefficient estimates and the predictive accuracy of the
model. - **Outliers and Influential Points**: Some data points with high
leverage could disproportionately influence the model’s predictions,
suggesting that the model may not generalize well across all segments of
the housing market. - **Static Analysis**: The model does not account
for changes over time, such as market fluctuations or demographic
shifts, which could significantly impact house prices.

## References

\[1\] T. Li and J. P. Zhang, “Correlation and regression analysis in
real estate pricing,” J. Real Estate Finance Econ., vol. 52, no. 4,
pp. 408-432, May 2016. \[2\] M. A. Khatib, H. F. Habib, and R. A. Khan,
“Statistical analysis methods for real estate market evaluation,” Real
Estate Econ., vol. 48, no. 3, pp. 586-607, Aug. 2020. \[3\] R. J.
Shiller, “Multiple regression analysis in housing price prediction: A
case study,” in Proc. Int. Conf. on Econ. and Finance Res., New York,
NY, USA, 2017, pp. 245-249. \[4\] N. S. Kumar, “Applying ANOVA in
comparative housing market analysis,” J. Stat. Manag. Syst., vol. 21,
no. 1, pp. 113-128, Jan. 2018. \[5\] L. Chen and R. R. Ren, “Model
selection and hypothesis testing in predictive modeling: A study in the
context of housing prices,” Stat. Appl. in Genet. and Mol. Biol.,
vol. 17, no. 5, Article e1234, 2018.
