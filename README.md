# Sicily Smoking Ban Impact Analysis: Interrupted Time Series with Bayesian Methods

## Overview

This project implements a **Bayesian Interrupted Time Series (ITS) analysis** using PyMC to estimate the causal impact of Sicily's smoking ban on acute coronary event (ACE) rates. The analysis employs a Poisson regression model with population offset to account for exposure and includes a counterfactual comparison to quantify prevented cases.

## Research Question

**What was the impact of the smoking ban introduction on ACE incidence rates in Sicily?**

The analysis compares:
- **Actual model**: Observed data with ban effect
- **Counterfactual**: Predicted cases had the ban not occurred
- **Effect estimate**: Cumulative prevented cases with 94% credible intervals

## Dataset

- **Source**: `data/sicily.csv`
- **Key variables**:
  - `time`: Month index
  - `aces`: Count of acute coronary events
  - `smokban`: Binary indicator (0=pre-ban, 1=post-ban)
  - `month`: Monthly seasonality indicator
  - `stdpop`: Standard population for offset adjustment

## Methodology

### 1. Exploratory Data Analysis (EDA)
- Time series visualization of ACE cases
- Distribution analysis (pre vs post-ban)
- Autocorrelation and partial autocorrelation plots (ACF/PACF)

### 2. Model Specification

**Poisson ITS Model with Log-Linear Offset:**

$$\log(E[Y_t]) = \beta_0 + \beta_1 \cdot \text{time}_t + \beta_2 \cdot \text{smokban}_t + \beta_3 \cdot \text{time\_after\_ban}_t + \sum \beta_{s} \cdot \text{season}_s + \log(\text{pop}_t)$$

**Priors:**
- $\beta_0 \sim N(0, 10)$ (intercept)
- $\boldsymbol{\beta} \sim N(0, 2)$ (coefficients)

**Model Terms:**
- `time`: Baseline trend (pre-ban slope)
- `smokban`: Level change at intervention
- `time_after_ban`: Slope change post-intervention
- Seasonal dummies: Monthly effects
- Population offset: Adjusts for exposure

### 3. Bayesian Inference

**Posterior Sampling:**
- 3,000 draws with 1,000 tune iterations
- NUTS sampler (NumPyro backend)
- Target acceptance rate: 0.95

**Diagnostics:**
- Trace plots for convergence assessment
- Summary statistics of posterior distribution
- Energy plots for HMC diagnostics
- Effective sample size (ESS) evolution
- Posterior predictive checks

### 4. Causal Inference

**Counterfactual Scenario:**
- Set intervention variables to zero (no ban)
- Draw from posterior predictive distribution
- Compare against observed outcomes

**Effect Estimation:**
- Prevented cases per time point
- Cumulative prevented cases post-ban
- 94% Highest Density Interval (HDI)

## Key Results

The analysis produces:
1. **Actual vs Counterfactual Predictions**: Overlaid on observed data
2. **Cumulative Prevented Cases Plot**: Shows total impact accumulation
3. **Summary Statistics**: Mean and 94% CI for total prevented cases

## Files

- **`project.ipynb`**: Main analysis notebook with 24 cells
  - Data loading & preprocessing
  - Exploratory visualizations
  - PyMC model definition
  - Posterior inference & diagnostics
  - Counterfactual analysis
  - Impact visualization

- **`data/sicily.csv`**: Input dataset (ACE cases in Sicily)
- **`README.md`**: This documentation

## Installation & Requirements

See `pyproject.toml` for dependencies. Key packages:
- `pymc` ≥ 4.0
- `arviz` ≥ 0.13
- `pandas`, `numpy`
- `matplotlib`, `seaborn`
- `pytensor`
- `statsmodels`

Install with:
```bash
uv sync
```

## Running the Analysis

1. **Start Jupyter notebook:**
   ```bash
   jupyter notebook project.ipynb
   ```

2. **Execute cells sequentially** from top to bottom:
   - Cell 1-4: Setup & data loading
   - Cell 5-9: EDA visualizations
   - Cell 10-12: Model building & prior predictive checks
   - Cell 14-22: Posterior sampling & diagnostics
   - Cell 23: **Main causal inference** (counterfactual + prevented cases)

3. **Interpret visualizations**:
   - Top subplot: Model predictions vs observed
   - Bottom subplot: Cumulative prevented cases

## Model Assumptions

1. **Poisson likelihood** appropriate for count data
2. **Linear trends** in log-scale
3. **Additive seasonality** (monthly patterns)
4. **Population offset** correctly captures exposure
5. **No unmeasured confounding** (ITS assumption)
6. **Intervention effect starts immediately** at ban date

## Interpretation Guide

### Posterior Summary
- `beta0`: Baseline log-rate of ACE
- `betas[0]`: Time trend coefficient
- `betas[1]`: Level change at ban
- `betas[2]`: Slope change post-ban (intervention effect on trend)
- `betas[3+]`: Seasonal coefficients

### Causal Inference

**Prevented Cases Estimate:**
- Point estimate: Mean over posterior samples
- 94% CI: Credible interval from HDI

**Interpretation:**
- **Positive**: Ban reduced ACE rates
- **Negative**: Ban increased ACE rates  
- **CI crossing 0**: Uncertainty in effect direction

## Key Visualizations

1. **Time Series**: Pre-ban and post-ban trends
2. **Distribution Plots**: Pre vs post-ban ACE distributions
3. **ACF/PACF**: Temporal dependence structure
4. **Trace Plots**: MCMC convergence diagnostics
5. **Forest Plot**: Posterior distributions of coefficients
6. **PPC (Posterior Predictive Check)**: Model fit assessment
7. **Actual vs Counterfactual**: Side-by-side model predictions
8. **Cumulative Effect**: Total prevented cases accumulation

## References

- Bernal, J. L., Cummins, S., & Gasparrini, A. (2017). Interrupted time series regression for health policy evaluation. *Statistical Methods in Medical Research*, 26(6), 2515-2528.
- McElreath, R. (2020). *Statistical Rethinking*, 2nd Edition. Chapman and Hall/CRC.
- Gelman, A., et al. (2013). *Bayesian Data Analysis*, 3rd Edition. Chapman and Hall/CRC.

## Notes

- The analysis uses **Bayesian ITS** rather than frequentist approaches for full posterior inference and uncertainty quantification
- **Population offset** via log-standardized population ensures rate comparisons
- **Counterfactual approach** allows direct causal estimates under ITS assumptions
- Results depend on model specification and prior choices

---

**Repository**: ori112/sicily_smoking_ban_ITS_bayesian  
**Last Updated**: February 2026
