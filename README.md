Insurance Pricing GLM: Frequency–Severity Pure Premium Model

An actuarial-style GLM pipeline that predicts auto insurance pure premium by separately modeling claim frequency and claim severity, then combining them — the classic two-part approach used in P&C ratemaking.

Overview

This project builds a Generalized Linear Model (GLM) pricing engine on the French Motor Third-Party Liability dataset, a standard public benchmark for auto insurance pricing research. The goal is to estimate a per-policy pure premium (expected annual claim cost) by combining:


Frequency model — how often a policy is expected to file a claim
Severity model — how large each claim is expected to be, given that one occurs


Pure Premium = Predicted Frequency × Predicted Severity

Data


Source: OpenML freMTPL2freq (678,013 policies) and freMTPL2sev (26,639 individual claims)
Claims aggregated to the policy level and merged onto the policy table
Cleaning: capped Exposure at 1 (data-entry errors), capped ClaimNb at 4 and ClaimAmount at its 99.99th percentile (~$65K) so a handful of extreme claims don't dominate the fit


Methodology

ComponentModelSpecificationFrequencyPoisson GLMClaimNb ~ BonusMalus + Area + VehPower + VehAge + DrivAge + VehBrand + VehGas + Region, offset = log(Exposure)SeverityGamma GLM (log link)Same predictors, fit only on policies with ≥1 claim, weighted by claim count

Train/test split: 80/20.

Results


Poisson deviance improved from a baseline (intercept-only) model of 0.6157 to 0.5976 with the full GLM — a 2.9% reduction, showing the rating factors add real explanatory power beyond the average frequency alone.
Lift chart (lift_chart.png): test-set policies were sorted into 10 risk deciles by predicted pure premium, then predicted vs. actual average claim cost was compared per decile. This is the standard actuarial check for whether a pricing model correctly rank-orders risk — not just its aggregate accuracy.


Tech Stack

Python · pandas · statsmodels (GLM) · scikit-learn (data loading, metrics) · matplotlib

Repository Structure

├── Untitled.ipynb     # full analysis: data prep → frequency model → severity model → pure premium → evaluation → lift chart
├── lift_chart.png      # decile lift chart validating risk ranking
└── README.md

Notes

Built on a public benchmark dataset (not proprietary insurer data) to demonstrate the frequency-severity GLM methodology used in real-world P&C ratemaking.# insurance_pricing_plm
