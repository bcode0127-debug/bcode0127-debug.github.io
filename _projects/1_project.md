---
layout: page
title: Energy Consumption Forecasting Breaking the Linear Barrier
description: Systematic evaluation of linear vs ensemble methods on 167M smart meter records
img: assets/img/ensemble_diagnostics.png
importance: 2
category: Research
github: https://github.com/bcode0127-debug/energy-forecasting
related_publications: false
---

## Overview

Forecasting energy demand at grid scale reveals fundamental limits of interpretable linear models. Using 167 million smart meter records from the London Grid, this work systematically evaluates whether the performance gap between linear regression and ensemble methods is quantifiable, and what trade-offs exist for production deployment.

## Research Question

Can interpretable linear models achieve competitive performance in production energy forecasting systems, or are non-linear ensemble methods necessary? This question is critical for grid operators who require explainable predictions for regulatory compliance while maintaining forecasting accuracy.

---

## Key Findings

### Finding 1: Linear Models Hit a Hard Performance Ceiling

Despite optimal feature engineering, linear models plateau around 8% improvement over baseline. Gradient boosting reaches 15%—a 7 percentage point gap representing the cost of interpretability. More critically, linear models collapse below baseline at longer horizons (96-hour forecasts), while ensemble methods remain stable.

### Finding 2: Heteroscedasticity Reveals Structural Non-Linearity

Peak-hour variance (6-9 PM) is 2.9x higher than off-peak hours (3-6 AM). Even Weighted Least Squares failed to correct this, indicating the problem is driven by structural non-linearity rather than simple variance weighting issues.

### Finding 3: Lag Configuration Sensitivity

Linear models experience 10% performance degradation with suboptimal lag configurations, compared to only 6% for gradient boosting. Models using 30-minute and 24-hour lags achieve optimal performance, while 48-hour and 96-hour lags cause collapse. This sensitivity indicates linear models lack robustness to feature engineering choices.

---

## Comparative Results

| Model | lag_30m/24h | lag_48h/96h | Performance Swing |
| :--- | :---: | :---: | :---: |
| Naive Baseline | 0.0% | 0.0% | - |
| OLS | +7.81% | -2.05% | -9.86% |
| Elastic Net | +8.07% | -1.72% | -9.79% |
| Huber | +7.41% | -2.59% | -10.00% |
| WLS | +4.44% | -7.11% | -11.55% |
| **Gradient Boosting** | **+15.44%** | **+9.56%** | **-5.88%** |
{: .table .table-sm .table-borderless}

---

## Technical Approach

**Data Engineering:** Processed 167M smart meter records on Google Cloud Platform using BigQuery for distributed SQL aggregations and temporal feature alignment.

**Feature Engineering:** Fourier harmonics (3 harmonics), sine/cosine encoding for cyclical patterns, lag configurations (tested 30m/24h and 48h/96h), weather features (temperature, humidity, HDD/CDD), interaction terms, and rolling statistics (4-hour moving average and standard deviation).

**Models Evaluated:** OLS, Elastic Net, Huber Regression, Weighted Least Squares, Histogram Gradient Boosting Regressor.

**Evaluation:** 80/20 temporal split with strict chronological ordering, 48-hour ahead forecasting target, leakage prevention via temporal validation.

---

## Production Implications

### Hybrid Deployment Strategy

Grid operators should consider a differentiated approach based on time-of-day:

**Off-Peak Hours (10 PM - 6 AM):**
- Deploy linear models (Elastic Net or Ridge regression)
- Rationale: 7-8% improvement sufficient, interpretable coefficients for regulatory compliance
- Lower stakes: errors during low-demand periods are less costly

**Peak Hours (6 AM - 10 PM):**
- Deploy Gradient Boosting ensembles
- Rationale: 15% improvement critical during high-demand periods
- Higher stakes: errors impact grid stability and market costs

**Monitoring:** Track variance in real-time; auto-switch to ensemble when variance exceeds threshold. Log model decisions for regulatory audit trails.

---

## Repository

Access the full code, datasets, and reproducibility instructions on [GitHub](https://github.com/bcode0127-debug/energy-forecasting).

## Discussion

The interpretability-performance trade-off is quantifiable: a 7-10% accuracy gap with real consequences in production deployment. This work demonstrates that ensemble methods not only achieve higher peak accuracy but also exhibit greater robustness to feature engineering choices and maintain stability across forecast horizons.

For high-stakes systems like power grids, this suggests the interpretability premium may be worth paying during off-peak periods where accuracy demands are lower, while reserving ensemble methods for critical peak hours where grid stability is paramount. This tension between transparency and performance connects to broader questions in ML deployment around model accountability and reliability in regulated industries.