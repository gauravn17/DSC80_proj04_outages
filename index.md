# 📊 Major U.S. Power Outage Analysis (2000–2016)
### DSC 80 — Project 4  
### **Author:** Gaurav Nair  

---

## Overview
This project analyzes large-scale U.S. power outage events from 2000–2016.  
The analysis covers:
- data cleaning,
- missingness assessment,
- hypothesis testing,
- predictive modeling, and
- fairness evaluation.

The final goal is to build a model that predicts **outage duration** based on climate conditions, outage causes, and customer impact.

---

# Step 1 — Data Cleaning

The original Excel sheet contained metadata rows and inconsistent formatting.  
After cleaning:
df_clean.shape → (1476, 57)

Key cleaning steps:
- Removed metadata/header rows  
- Standardized column names  
- Parsed date and time columns  
- Created `YEAR` and `MONTH` features  
- Removed invalid outage durations (≤ 0)  
- Dropped rows missing key timestamp fields  

The cleaned dataset preserves all 57 variables describing outage conditions, climate factors, economic metrics, and population attributes.

---

# Step 2 — Univariate & Bivariate Analysis

### **Univariate Observations**
- **Outage Duration** is heavily right-skewed. Most outages are short, with rare extreme events.
- **Customers Affected** also shows a heavy-tailed distribution.

### **Bivariate Observations**
- Longer outages tend to impact more customers.
- Weather-related outages generally have higher outage durations compared to equipment failures.
- Climate region differences suggest that geography affects outage severity.

---

# Step 3 — Missingness Analysis

We focus on the missingness of **`CUSTOMERS_AFFECTED`**, a frequently missing field in the dataset.

### 3A — Proportion Missing

Proportion missing = 0.28523

About **28.5%** of entries lack customer impact data.

---

### 3B — MAR Test: Does missingness depend on CAUSE_CATEGORY?

Permutation test:
Observed statistic = 0.27623
p-value = 0.918

### Interpretation:
Since **p = 0.918**, we **fail to reject the null hypothesis**.

➡️ Missingness **does not depend** on the cause of the outage.  
➡️ Evidence suggests **MCAR** with respect to cause category.

---

### 3C — MCAR Test: Does missingness depend on MONTH?

Permutation test:
Observed statistic = 0.08800
p-value = 0.0

### Interpretation:
Since **p = 0.0**, we **reject the null hypothesis**.

➡️ Missingness **does depend** on month.  
➡️ Data reporting varies by time of year, suggesting **MAR**.

---

### 3D — NMAR Discussion
Large outages may destroy monitoring systems, making it impossible to record the number of affected customers.

➡️ Missingness may be **NMAR**, partially driven by the actual magnitude of the outage.

To adjust for this, additional fields such as *utility reporting quality*, *infrastructure damage*, or *severity measures* would help convert NMAR → MAR.

---

# Step 4 — Hypothesis Testing

### **Question:**  
> Do weather-related outages last longer than intentional-attack outages?

### Group Sizes:
- Weather-related outages: **743**
- Intentional attacks: **403**

### Results:
Observed difference = -1666.63
p-value = 0.918

The difference is large in absolute value but statistically insignificant.

### Interpretation:
➡️ We **fail to reject the null hypothesis**.  
➡️ No evidence that weather outages are significantly longer than intentional attacks.

---

# Step 5 — Feature Engineering

Target: **`OUTAGE_DURATION`**

Selected features:
- `CUSTOMERS_AFFECTED`
- `CAUSE_CATEGORY`
- `CLIMATE_REGION`
- `MONTH`
- `YEAR`

Engineered interaction:
- `CUSTOMERS_YEAR = CUSTOMERS_AFFECTED × YEAR`  
  (captures long-term scaling with population growth and infrastructure changes)

---

# Step 6 — Baseline Model (Linear Regression)

A linear regression with one-hot encoding served as the baseline.
Baseline RMSE = 3412.49

The baseline struggled due to:
- non-linearity,
- skewed distributions,
- heavy-tailed durations.

---

# Step 7 — Final Model (Random Forest)

A Random Forest Regressor was trained with:
- numerical scaling,
- categorical encoding,
- hyperparameter tuning via GridSearchCV.

### Final Performance:
Final RMSE = 3041.06

### Improvements:
- Handles non-linearity  
- Captures interactions between climate region and outage cause  
- More robust to skew and extreme cases  

However, due to the chaotic nature of outage events, prediction remains challenging.

---

# Step 8 — Fairness Analysis

### Groups Compared:
- **West** climate region  
- **Southeast** climate region  

### Observed Difference:
Observed RMSE Difference = -1666.63

Negative → Model performs **worse** in the Southeast.

### Permutation Test:
p-value = 0.918

### Interpretation:
➡️ Despite the large RMSE gap, we find **no statistically significant evidence** of unfairness.  
➡️ Differences are consistent with random variation rather than systematic bias.

---

# Summary & Conclusions

- Cleaned dataset: **1,476 outage events**  
- Missingness is a mix of **MAR**, **MCAR**, and potentially **NMAR**  
- Hypothesis test showed no duration difference between weather and attacks  
- Final model improves over baseline but RMSE remains high  
- Fairness analysis shows **no statistically significant bias**

This project highlights the complexity and variability of real-world outage data and demonstrates how statistical testing and modeling can inform infrastructure resilience analysis.

---

# Website Link

**https://gauravn17.github.io/DS80_proj04_outages/**

---
