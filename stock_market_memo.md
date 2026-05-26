# Stock Market Memo

**To:** Investment Strategy Team
**From:** Tony Tran, Data Analyst
**Date:** May 2026
**Re:** Bayesian Screening of Stock Return Probabilities

---

## Purpose

This memo summarizes findings from a Bayesian hierarchical analysis of 50 company stocks across 5 business sectors. The goal was to estimate each company's probability of returning a net positive in any given period, while accounting for the natural correlation that exists between companies operating in the same sector.

---

## What We Did

We modeled 1,500 binary stock return observations using a random-effects hierarchical model. Rather than treating each company independently, the model allows companies within the same sector to share statistical information. This produces more reliable estimates, especially for companies where individual data is limited.

The model was validated using standard MCMC diagnostics. Chains converged cleanly, autocorrelation was low, and effective sample sizes ranged from 15,000 to 22,000, all of which indicate that the estimates can be trusted.

---

## Key Takeaways

**Sector-level trends matter.** Each sector produces a mean log-odds estimate ($\mu_s$) that reflects whether companies in that sector tend to return positively or negatively on average. Sectors with a positive posterior mean are structurally more favorable and may warrant a higher allocation weight in the overall portfolio.

**Not all companies behave like their sector.** Within each sector, individual companies have their own return probability ($\theta_{sc}$). Companies whose credible interval sits entirely above 50% are strong candidates for individual investment. Companies tightly clustered around the sector mean offer less differentiation, making sector-level instruments like ETFs a more efficient choice there.

**Precision tells you how much company selection matters.** The precision parameter ($\tau_s$) measures how tightly companies cluster around their sector mean. In high-precision sectors, most companies behave similarly, so picking individual names adds little value. In low-precision sectors, there is more spread between companies, which means careful stock selection has a real payoff.

**Uncertainty is part of the output.** Unlike a traditional analysis that returns a single probability estimate, this model returns a full distribution for each parameter. That means every recommendation comes with a measure of confidence, which can be used directly in position sizing and risk management.

---

## Recommendations

1. Prioritize sectors where the posterior mean log-odds ($\mu_s$) is clearly positive. These sectors show a structural lean toward positive returns across their companies.
2. Within those sectors, focus individual stock selection on companies where the 95% credible interval for $\theta_{sc}$ is bounded above 0.5. These are high-conviction positions.
3. In sectors with high precision, consider broad sector exposure rather than individual stock picks. The differentiation between companies is minimal, so concentration risk is not rewarded.
4. Use the posterior distributions directly in portfolio construction. Position sizes should reflect both the direction and the confidence of each estimate.

---

## Limitations to Keep in Mind

The data used in this analysis has no specified collection period, so it is unclear how current these findings are. Stock return probabilities are not stable over time, and shifts in macroeconomic conditions can move baselines significantly. Additionally, sector labels are numeric rather than named, which requires mapping back to actual industries before acting on sector-level findings.

These results are best used as a screening and prioritization tool, not as a standalone decision system.

---

*Analysis conducted using a Bayesian hierarchical model fit in JAGS. Full technical documentation and model diagnostics are available in the project repository.*