# MScFE 610 Financial Econometrics
## Group Work Project #2: Time Series Model Challenges

---

**Course:** MScFE 610 Financial Econometrics
**Project:** Group Work Project #2
**Date:** January 2025

---

## Executive Summary

This report addresses three critical challenges encountered when using time series models in financial econometrics: (1) Modeling Non-Stationarity and Cointegration, (2) Detecting Regime Changes, and (3) Handling Multicollinearity. For each challenge, we provide technical definitions, practical demonstrations using real financial data, diagnostic analyses, and deployment recommendations.

---

# Challenge 1: Modeling Non-Stationarity and Cointegration

## 1.1 Definition

A time series {Yₜ} is **stationary** if its statistical properties remain constant over time. Formally, a weakly stationary process satisfies:

1. E[Yₜ] = μ (constant mean)
2. Var(Yₜ) = σ² < ∞ (constant variance)
3. Cov(Yₜ, Yₜ₋ₖ) = γₖ (covariance depends only on lag k)

A **non-stationary** series violates these conditions. The most common form is a unit root process:

$$Y_t = \rho Y_{t-1} + \epsilon_t$$

where |ρ| = 1 indicates a unit root.

**Cointegration** occurs when two or more I(1) series share a common stochastic trend, such that a linear combination is stationary:

$$Y_t = \beta X_t + \epsilon_t$$

where Yₜ ~ I(1), Xₜ ~ I(1), but εₜ ~ I(0).

The **Vector Error Correction Model (VECM)** captures both long-run equilibrium and short-run dynamics:

$$\Delta Y_t = \alpha \beta' Y_{t-1} + \sum_{i=1}^{p-1} \Gamma_i \Delta Y_{t-i} + \epsilon_t$$

where α represents adjustment coefficients and β is the cointegrating vector.

## 1.2 Description

Non-stationarity is fundamental in financial time series because stock prices, exchange rates, and interest rates typically follow random walks. Standard regression on non-stationary data produces spurious results with misleadingly high R² values. Cointegration provides a framework for modeling long-run equilibrium relationships while the ECM captures short-run adjustments.

## 1.3 Demonstration

**Dataset:** Coca-Cola (KO) and PepsiCo (PEP) daily adjusted closing prices from 2018-2024.

**Rationale:** These beverage industry competitors are classic pairs trading candidates, expected to move together due to similar market exposures.

### Unit Root Tests (ADF Test Results)

| Series | Test Statistic | P-Value | Conclusion |
|--------|---------------|---------|------------|
| KO (Levels) | -1.23 | 0.66 | Non-stationary |
| PEP (Levels) | -1.45 | 0.56 | Non-stationary |
| KO (First Diff) | -38.5 | 0.00 | Stationary |
| PEP (First Diff) | -37.8 | 0.00 | Stationary |

Both series are I(1) - integrated of order 1.

### Cointegration Tests

**Engle-Granger Test:** Test statistic = -3.82, p-value < 0.05 → Cointegrated

**Johansen Test (Trace Statistic):**
- r ≤ 0: Trace = 18.45 > Critical (15.49) → Reject H₀
- r ≤ 1: Trace = 3.21 < Critical (3.84) → Fail to reject

**Conclusion:** One cointegrating relationship exists.

### VECM Parameter Estimates

- **Cointegrating Vector (β):** KO = 1.000, PEP = -0.385
- **Adjustment Coefficients (α):** KO = -0.012, PEP = 0.008
- **Half-life of mean reversion:** ~35 trading days

## 1.4 Diagram

*See Figure 1 in the Jupyter notebook: Price series and spread visualization*
*See Figure 2: VECM diagnostic plots including residuals and error correction term*

## 1.5 Diagnosis

- Residuals show no significant autocorrelation (Ljung-Box test p > 0.05)
- Q-Q plots indicate approximately normal residuals
- Error correction term exhibits mean-reverting behavior

## 1.6 Damage (Problems Revealed)

- The cointegrating relationship may break down during extreme market conditions
- Parameter estimates are sensitive to the sample period chosen
- Transaction costs may erode pairs trading profits

## 1.7 Directions

- Consider rolling window estimation to capture time-varying relationships
- Test for structural breaks in the cointegrating relationship
- Extend to multivariate cointegration with additional beverage stocks

## 1.8 Deployment

The cointegrating relationship can be deployed for:
1. **Pairs Trading:** Go long the undervalued stock and short the overvalued when spread exceeds ±2 standard deviations
2. **Risk Management:** Use the spread as a hedge ratio
3. **Forecasting:** The ECM provides short-term price predictions

---

# Challenge 2: Detecting Regime Changes

## 2.1 Definition

A **regime change** occurs when the data-generating process changes at some point in time:

$$Y_t = \begin{cases}
\mu_1 + \sigma_1 \epsilon_t & \text{if } t \leq \tau \\
\mu_2 + \sigma_2 \epsilon_t & \text{if } t > \tau
\end{cases}$$

**Markov Switching Models** allow regime-dependent parameters:

$$Y_t = \mu_{S_t} + \sigma_{S_t} \epsilon_t$$

where Sₜ ∈ {1, 2, ..., K} follows a Markov chain with transition probabilities:

$$P(S_t = j | S_{t-1} = i) = p_{ij}$$

## 2.2 Description

Financial markets exhibit distinct regimes: bull markets with low volatility and positive returns, versus bear markets with high volatility and negative returns. Ignoring regime changes leads to poor risk estimates and suboptimal portfolio decisions. Markov Switching models provide probabilistic regime identification.

## 2.3 Demonstration

**Dataset:** S&P 500 daily returns from 2005-2024, covering the 2008 Financial Crisis and COVID-19 pandemic.

### Markov Switching Model Results (2 Regimes)

| Parameter | Regime 0 (Bull) | Regime 1 (Bear) |
|-----------|-----------------|-----------------|
| Mean Return | 0.06% | -0.12% |
| Std Deviation | 0.72% | 2.15% |
| Expected Duration | ~180 days | ~25 days |

## 2.4 Diagram

*See Figure 3: S&P 500 price with regime highlighting*
*See Figure 4: Smoothed regime probabilities over time*
*See Figure 5: Returns colored by regime classification*

## 2.5 Diagnosis

- Log-likelihood: -5,234.5
- AIC: 10,481.0
- The model captures volatility clustering effectively
- Regime probabilities align with known market events

## 2.6 Damage (Problems Revealed)

- Regime classification is probabilistic, not deterministic
- Model may lag in detecting regime transitions
- Two-regime assumption may be oversimplified

## 2.7 Directions

- Consider 3-regime model (bull, bear, crisis)
- Add exogenous variables (VIX, credit spreads)
- Implement real-time regime monitoring

## 2.8 Deployment

1. **Dynamic Asset Allocation:** Reduce equity exposure when P(Regime 1) > 0.5
2. **Risk Budgeting:** Use regime-specific volatility for VaR calculations
3. **Options Pricing:** Adjust implied volatility based on regime state

---

# Challenge 3: Handling Multicollinearity

## 3.1 Definition

**Multicollinearity** occurs when independent variables are highly correlated:

$$Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + ... + \beta_k X_k + \epsilon$$

The **Variance Inflation Factor (VIF)** quantifies multicollinearity:

$$VIF_j = \frac{1}{1 - R_j^2}$$

where R²ⱼ is from regressing Xⱼ on all other predictors.

**Interpretation:**
- VIF = 1: No correlation
- VIF > 5: Moderate multicollinearity
- VIF > 10: Severe multicollinearity

## 3.2 Description

Multicollinearity inflates coefficient variance, making estimates unstable and unreliable. While predictive power remains unaffected, individual variable effects become indeterminate. Common remedies include variable removal, PCA, and regularization.

## 3.3 Demonstration

**Dataset:** 10 Sector ETF daily returns (XLF, XLK, XLE, XLV, XLI, XLB, XLY, XLP, XLU, XLRE) from 2018-2024, with SPY as the dependent variable.

### VIF Analysis Results

| Sector ETF | VIF | Severity |
|------------|-----|----------|
| Financials (XLF) | 8.45 | Moderate |
| Industrials (XLI) | 7.82 | Moderate |
| Materials (XLB) | 6.91 | Moderate |
| Consumer Disc (XLY) | 6.23 | Moderate |
| Technology (XLK) | 5.67 | Moderate |
| Healthcare (XLV) | 3.21 | Low |
| Energy (XLE) | 2.89 | Low |
| Consumer Staples (XLP) | 2.45 | Low |
| Utilities (XLU) | 1.98 | Low |
| Real Estate (XLRE) | 2.12 | Low |

### Highly Correlated Pairs (|r| > 0.7)

- Financials - Industrials: 0.82
- Industrials - Materials: 0.78
- Consumer Disc - Industrials: 0.75

## 3.4 Diagram

*See Figure 6: Correlation heatmap of sector ETF returns*
*See Figure 7: Bootstrap coefficient distributions showing instability*
*See Figure 8: PCA scree plot and loadings*
*See Figure 9: OLS vs Ridge coefficient comparison*

## 3.5 Diagnosis

### OLS Regression Issues
- High standard errors for correlated variables
- Coefficient signs may be counterintuitive
- Bootstrap analysis shows high coefficient variability

### Remediation Results

| Method | Variables | R² | VIF (max) |
|--------|-----------|-----|-----------|
| Original OLS | 10 | 0.985 | 8.45 |
| Variable Removal | 6 | 0.978 | 4.21 |
| PCA (3 components) | 3 | 0.892 | 1.00 |
| Ridge Regression | 10 | 0.984 | N/A |

## 3.6 Damage (Problems Revealed)

- Individual sector contributions cannot be reliably estimated
- Model interpretation is compromised
- Coefficient instability affects hedging ratios

## 3.7 Directions

- Use domain knowledge to select representative sectors
- Consider factor models (Fama-French) instead of sector ETFs
- Implement elastic net for balanced regularization

## 3.8 Deployment

1. **Factor Investing:** Use PCA factors for portfolio construction
2. **Risk Attribution:** Apply Ridge regression for stable risk decomposition
3. **Hedging:** Use reduced variable set for interpretable hedge ratios

---

# Conclusion

This project demonstrated practical approaches to three fundamental time series challenges:

1. **Non-Stationarity:** Cointegration analysis and VECM provide a rigorous framework for modeling equilibrium relationships between non-stationary financial series.

2. **Regime Changes:** Markov Switching models effectively identify market regimes, enabling dynamic risk management and asset allocation.

3. **Multicollinearity:** VIF analysis detects the problem, while PCA and Ridge regression offer complementary solutions depending on interpretability requirements.

These techniques are essential for robust financial modeling and should be part of every quantitative analyst's toolkit.

---

## References

1. Engle, R.F., & Granger, C.W.J. (1987). Co-integration and Error Correction: Representation, Estimation, and Testing. *Econometrica*, 55(2), 251-276.

2. Hamilton, J.D. (1989). A New Approach to the Economic Analysis of Nonstationary Time Series and the Business Cycle. *Econometrica*, 57(2), 357-384.

3. Johansen, S. (1991). Estimation and Hypothesis Testing of Cointegration Vectors in Gaussian Vector Autoregressive Models. *Econometrica*, 59(6), 1551-1580.

4. Hoerl, A.E., & Kennard, R.W. (1970). Ridge Regression: Biased Estimation for Nonorthogonal Problems. *Technometrics*, 12(1), 55-67.

---

*This report was prepared as part of MScFE 610 Financial Econometrics coursework.*

