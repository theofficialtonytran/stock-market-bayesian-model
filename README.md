# Bayesian Hierarchical Stock Return Analysis

Estimating the probability of positive stock returns using a random-effects hierarchical model with MCMC sampling via JAGS.

---

## Overview

This project applies Bayesian hierarchical inference to estimate the probability that any given stock returns a net positive in a single period. Because companies within the same sector are economically interdependent, a flat model would underfit the structure. We instead build a random-effects hierarchical model that pools information across companies within each sector while still allowing each company its own return probability.

The model is fit using JAGS (Just Another Gibbs Sampler) with 60,000 MCMC iterations, 20,000 burn-in, and 20,000 adaptation steps.

---

## Result

Each company's probability of a positive return, ($\theta_{sc}$), is estimated on the logit scale ($\eta_{sc}$) and then back-transformed. Posterior means for $\eta_{sc}$ stabilize around 0 (logit), corresponding roughly to a 50% return probability, which is consistent with efficient market intuitions for diversified cross-sector portfolios.

Sector-level mean log-odds ($\mu_s$) provide a view of which sectors trend above or below neutral, enabling comparative investment screening across all five business sectors.

---

## Key Statistics

| Parameter | Description | Posterior Behavior |
|---|---|---|
| $\eta_{sc}$ | Log-odds of positive return for company $c$ in sector $s$ | Converges near 0 +/- 0.5 logit |
| $\theta_{sc}$ | Probability of positive return (back-transformed) | ~45-55% range across companies |
| $\$mu_s$ | Sector mean log-odds | Varies by sector; quantifies sector-level trend |
| $\tau_s$ | Precision of log-odds within a sector | High values mean companies cluster tightly around sector mean |
| ESS | Effective Sample Size per parameter | ~15,000-22,000 (efficient sampling) |
| Iterations | Total MCMC draws | 60,000 (post burn-in and adapt: ~40,000 effective) |

---

## Data Quality

| Dimension | Detail |
|---|---|
| Dataset size | 1,500 observations |
| Structure | 5 sectors x 10 companies x 30 flip observations |
| Response variable | Binary: $1$ = net positive return, $0$ = net negative |
| Assumed distribution | Bernoulli within-company; Normal on logit scale across companies |
| Independence assumption | Flips are iid within a stock; companies iid within sector |

**Known limitations and potential biases:**

- **Undated data** - The collection period is unspecified, so findings may not reflect current market conditions.
- **Market volatility** - Stock return probabilities are non-stationary, and economic shocks can shift baselines dramatically.
- **Fixed 30 flips per stock** - Equal observation counts may mask stocks with longer or more informative histories.
- **Sector definitions** - Sectors are encoded 1-5 with no domain labels, which limits direct business interpretation.

---

## Visualizations

The following diagnostics were produced to validate MCMC convergence and posterior quality:

| Figure | Description |
|---|---|
| 1.3 Traceplots | Show $\eta_{sc}$, $\mu_s$, $\tau_s$ chains stabilizing; confirms convergence |
| 1.4 Boxplots | Posterior distributions of $\eta_{sc}$ per company, grouped by sector |
| 1.5 Initial values plot | Confirms burn-in and adaptation were effective; chains are stationary |
| 1.6 ESS table | Effective Sample Sizes of 15,000-22,000 indicate efficient posterior sampling |
| 1.7 ACF plots | Autocorrelation drops steeply at low lags, indicating near-independent posterior draws |

All diagnostics indicate a well-converged, low-autocorrelation chain with no evidence of pathological mixing.

---

## Repository Structure

bayesian-stock-returns/

- data/
    - stock_flips.csv         (1,500 obs: sector, stock, flip)
- diagnostics/
    - acf_plots.pdf           (All ACF Plots)
    - all_boxplots.pdf        (All boxplots)
    - effective_sameple_sizes.csv (All ESS values)
    - model_summary.txt       (JAGS Model output)
    - traceplots.pdf          (All Traceplots)
- FinalProject.Rmd            (Full Project including Overview, Description of Data, Hierarchial Model, Diagnostics, and Results)   
 - results/
    - EtaSectorCompanyReturns.png         (Contains graphs of Log-odds of positive return for company $c$ in sector $s$)  
    - MuSectorReturns.png       (Contains graphs of Sector mean log-odds)
    - PrecisionSectorReturns.png (Contains graphs of Precision of log-odds within a sector)
- README.md
- stock_market_memo_md        # Business memo 

---

## Why This Result Matters

Classical frequentist models treat each company in isolation, discarding the shared information that neighboring stocks within a sector carry. The hierarchical Bayesian approach solves this through partial pooling: companies with sparse data borrow strength from their sector, while companies with informative data retain their own identity.

This matters for investment because:

- **Small-sample robustness**: A company with only 30 observations is susceptible to noise. Partial pooling from sector-level priors regularizes extreme estimates toward the sector mean, reducing overfitting.
- **Full uncertainty quantification**: Every parameter comes with a posterior distribution, not just a point estimate. This enables principled risk management and Bayesian decision theory such as expected utility maximization.
- **Scalable framework**: The model structure generalizes naturally to more sectors, longer flip histories, and additional covariates like macroeconomic indicators or earnings data via extensions to the hyperprior layer.

In short, this framework moves portfolio analysis from "which stocks went up?" to "what is the probability any given stock goes up, and how confident are we?" That is the question that actually drives allocation decisions.

---

*Model fit with JAGS · 60k iterations · 20k burn-in · 20k adapt · ESS ~15,000-22,000*