# 📊 Statistics 2  
**Authors:** Jeremy Jornet · Uri  
**Institution:** Technion — Israel Institute of Technology  
**Course:** Statistics 2  

---

## 🧭 Overview

This repository contains four applied projects carried out during the *Statistics 2* course at Technion.  
Across these projects, we progressively explore the pillars of **statistical inference** using a real-world dataset of hotel bookings:

1. **Project 1 — Exploratory Data Analysis**  
2. **Project 2 — Estimation and Hypothesis Testing**  
3. **Project 3 — Linear and Logistic Regression**  
4. **Project 4 — Parametric and Non-Parametric Tests**

Dataset used throughout: [Kaggle — Hotels Booking Data (Cleaned Version)](https://www.kaggle.com/datasets/khairullahhamsafar/hotels-booking-data-cleaned-version)

---

# 🧮 Project 1 — Exploratory Data Analysis

### 🎯 Objective
Perform a detailed **exploratory data analysis (EDA)** of hotel booking data to understand price behavior, customer patterns, and prepare the dataset for future modeling.

---

### 🧹 Data Preparation & Feature Engineering
The dataset (originally 119k rows) was filtered to:
- Country: **PRT (Portugal)**
- Hotel: **City Hotel**
- Sample size: **20,000 random observations**

New derived variables:

| Feature | Description |
|----------|-------------|
| `total_guests` | adults + children + babies |
| `number_of_nights` | weeknights + weekend nights |
| `weekend_included` | 1 if includes Saturday/Sunday |
| `arrival_day` | days since first recorded arrival |
| `arrival_day_of_week` | categorical day name |
| `adr_per_guest` | ADR / total guests |

---

### 📊 Statistical Summary

| Variable | Mean | Std | Min | 25% | 50% | 75% | Max |
|-----------|------|------|------|------|------|------|------|
| Lead time | 140.6 | 134.1 | 0 | 28 | 99 | 230 | 629 |
| ADR (€) | 94.8 | 33.5 | 1 | 67.5 | 90 | 110 | 352.5 |
| ADR/guest (€) | 58.0 | 28.8 | 0.5 | 37.5 | 50 | 68.5 | 289.9 |
| Nights | 2.57 | 1.55 | 1 | 2 | 2 | 3 | 34 |
| Guests | 1.8 | 0.55 | 1 | 1 | 2 | 2 | 5 |

Detected outliers: ADR ≈ 1€ and ADR/guest ≈ 0.5–290€.

---

### 💡 Research Questions
1. Do early bookings (high lead time) lead to lower average daily rates (ADR)?
2. Are early bookings more likely to be canceled?
3. Do returning guests pay lower nightly rates?
4. Does ADR/guest increase over time?

---

### 🧠 Insights
- Slight negative correlation between lead time and ADR.  
- Early bookings more often canceled.  
- Returning guests show slightly lower ADR.  
- Seasonal peak in summer (June–August).  

---

# 📈 Project 2 — Estimation and Hypothesis Testing

### 🎯 Objective
To formally test whether **returning guests** pay a significantly different average nightly rate compared to **first-time guests** using classical inference.

---

## 🧩 Research Question

\[
H_0: \mu_{\text{first-time}} - \mu_{\text{returning}} = 0 \quad
H_1: \mu_{\text{first-time}} - \mu_{\text{returning}} \neq 0
\]

---

### 🧮 Estimation

| Group | Mean (µ̂) | 95% CI |
|--------|-----------|---------|
| First-time guests | 95.72 | [95.23, 96.21] |
| Returning guests | 76.61 | [75.06, 78.16] |

Intervals do not overlap → strong evidence of difference.

---

### ⚗️ Statistical Tests

| Test | Statistic | p-value | Result |
|------|------------|----------|---------|
| F-Test (variance equality) | 2.04 | 1.1×10⁻¹⁶ | Reject equal variances |
| t-Test | 17.05 | 1.0×10⁻⁶⁴ | Reject H₀ |
| Wald Test | 57.82 | < 0.001 | Reject H₀ |
| Likelihood Ratio Test | 288.62 | < 0.001 | Reject H₀ |

✅ Conclusion: first-time guests pay significantly higher rates than returning guests.

---

### 📏 Sample Size Experiment

| n | CI (First-time) | CI (Returning) | Wald p-value | Reject H₀ |
|----|----------------|----------------|---------------|-------------|
| 30 | [79.9, 114.7] | [63.3, 81.4] | 0.60 | ❌ |
| 50 | [78.3, 105.3] | [68.4, 87.9] | 0.70 | ❌ |
| 100 | [86.5, 105.6] | [69.1, 90.7] | 0.55 | ❌ |
| 500 | [89.0, 97.5] | [74.9, 80.8] | 1.1×10⁻⁵ | ✅ |

Increasing n → smaller CI length and higher power.

---

### 📉 Simulation (100 repetitions)
- p-values converge toward 0 as n increases.
- CI coverage approaches theoretical 95%.

---

# 📊 Project 3 — Linear & Logistic Regression

### 🎯 Objectives
1. Predict **average cost per guest (adr_per_guest)** with **multiple linear regression**.  
2. Predict **cancellation likelihood** with **logistic regression**.

---

## 🧮 Linear Regression

\[
\hat{y} = β_0 + β_1 x_{\text{guests}} + β_2 x_{\text{nights}} + β_3 x_{\text{weekend}} + β_4 x_{\text{leadtime}}
\]

| β | Estimate | CI | Significance |
|----|-----------|---------------|---------------|
| β₀ | 115.83 | [106.07, 125.58] | ✅ |
| β₁ (guests) | −33.06 | [−37.9, −28.3] | ✅ |
| β₂ (nights) | +3.45 | [1.18, 5.73] | ✅ |
| β₃ (weekend) | −4.25 | [−10.1, 1.6] | ❌ |
| β₄ (lead_time) | −0.045 | [−0.063, −0.028] | ✅ |

Model fit:  
- R² = 0.546, F(4,195) = 58.55 (p < 0.001)

✅ Interpretation:  
More guests → lower ADR/guest; longer stays slightly increase it.

---

## 🤖 Logistic Regression

\[
\log\frac{p}{1-p} = β_0 + β_1 adr\_per\_guest + β_2 total\_guests + β_3 lead\_time
\]

| β | Estimate | exp(β) | Meaning |
|----|-----------|---------|----------|
| β₀ | −2.08 | — | Base log-odds |
| β₁ (adr_per_guest) | 0.0068 | 1.007 | Slightly higher ADR → lower cancel prob |
| β₂ (total_guests) | 0.659 | 1.93 | More guests → more likely to cancel |
| β₃ (lead_time) | 0.0093 | 1.009 | Longer lead time → higher cancel prob |

Model selection via AIC/BIC → simple model preferred.

---

### 📈 Insights
- Price per guest primarily depends on group size and lead time.  
- Larger groups tend to cancel more frequently.  
- Simple models generalize better; interactions add little gain.  

---

# 🧪 Project 4 — Parametric & Non-Parametric Tests

### 🎯 Objective
Compare **parametric (t, Wald)** and **non-parametric (permutation, rank-sum)** approaches to verify whether non-returning guests pay stochastically higher rates than returning guests.

---

## 🧩 Hypotheses

\[
H_0: \text{Distributions of ADR (first-time, repeated) are equal}
\]
\[
H_1: \text{ADR of non-returning guests is stochastically higher}
\]

Balanced samples: 100 returning + 100 first-time guests.

---

## 📊 Results Summary

| Metric | First-time | Returning | Δ (µ₁–µ₂) |
|----------|-------------|------------|-------------|
| Mean ADR | 94.72 | 77.25 | **17.47** |
| 95% CI (Δ) | [−56.15, 91.09] | — | — |
| Variance equality (F-test) | F = 0.74 | **Do not reject equality** |
| Wald Test | p = 0.64 | **Fail to reject H₀** |
| Permutation Mean Test | p = 0.00 | **Reject H₀** |

---

## 📏 Median Comparison

| Metric | First-time | Returning | Δ (Median₁–Median₂) |
|----------|-------------|------------|-------------|
| Median ADR | 97.5 | 67.0 | **30.5** |
| CI (Δ median) | [−56.15, 91.09] | — |
| Wald Test (median) | p = 0.00 | ✅ Reject H₀ |

Since normality is assumed and F-test confirmed equal variances → t-test assumptions valid.

---

## 🔁 Non-Parametric Rank Test

**Permutation of ranks (400 iterations)**

| Test | Statistic | p-value | Result |
|-------|-------------|----------|----------|
| Sum of ranks | 8114.0 | 1.0 | ❌ Fail to reject H₀ |
| Normal Approximation | z < 1.65 | ❌ Fail to reject H₀ |

→ Non-parametric results contrast with parametric ones.

---

## 📈 Visual Diagnostics
- Histograms show denser low-ADR frequencies for returning guests.  
- QQ-plots indicate approximate normality in both groups.  
- Conflicting test outcomes highlight sensitivity to distributional assumptions.

---

## 💡 Conclusions
- Parametric tests (mean, median) suggest a **significant difference**.  
- Non-parametric tests (permutation, rank-sum) **do not confirm** this difference.  
- Overall, returning guests still tend to pay lower ADRs, but evidence strength depends on test type.  

---

# 🧠 Theoretical Framework Across Projects

| Concept | Application |
|----------|--------------|
| Descriptive & Inferential Statistics | EDA → Estimation → Testing |
| Maximum Likelihood Estimation | Used for means, medians, and deltas |
| Confidence Intervals | Analytical and simulation-based |
| Hypothesis Testing | t-test, F-test, Wald, LRT, Permutation |
| Regression | Linear (continuous) and Logistic (binary) |
| Power & Sample Size | Studied via resampling and simulation |
| Model Selection | AIC, BIC, Stepwise methods |
| Non-Parametric Methods | Rank and permutation-based inference |

---

# 🧰 Stack
- **Python 3.11**
- **Libraries:** `pandas`, `numpy`, `scipy`, `statsmodels`, `matplotlib`, `seaborn`
- **Environment:** Jupyter Notebook / JupyterLab

---

# 🧾 References
- Khairullah Hamsafar, *Hotels Booking Data (Cleaned Version)* — Kaggle (2021)  
- A. Field, *Discovering Statistics Using Python* (Sage, 2018)  
- J. F. Hair et al., *Multivariate Data Analysis* (Pearson, 2019)  
- G. Casella & R. Berger, *Statistical Inference* (2002)

---

# 🏁 Summary

Over these four projects, we built a complete statistical workflow:
1. **Project 1:** Data cleaning and descriptive exploration  
2. **Project 2:** Estimation and inference on means  
3. **Project 3:** Regression modeling for prediction and classification  
4. **Project 4:** Comparison between parametric and non-parametric inference  

---
