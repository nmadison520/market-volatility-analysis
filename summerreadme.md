# AI and Semiconductor Portfolio Research

**Madison Nitti | Virginia Tech | Summer Research Project**

This project examines how portfolio weighting changes risk, returns, and factor exposures for five AI and semiconductor stocks. It compares equal-weighted, inverse-variance, and long-only minimum-variance portfolios, then uses CAPM and Fama–French three-factor regressions to explain their monthly excess returns.

## Research Questions

- Does adding size and value factors improve explanatory power compared with market risk alone?
- How do different weighting strategies affect historical portfolio volatility, average returns, and risk-adjusted performance?

## Data

The portfolio consists of NVIDIA (`NVDA`), AMD (`AMD`), Broadcom (`AVGO`), Micron (`MU`), and Intel (`INTC`).

- **Stock prices:** adjusted daily closing prices from Yahoo Finance, downloaded with `yfinance`.
- **Price window:** June 2020 through May 2026.
- **Monthly return sample:** July 2020 through May 2026, with 71 observations in the saved notebook run.
- **Factor data:** monthly market excess return (`Mkt-RF`), size (`SMB`), value (`HML`), and risk-free rate (`RF`) from the [Kenneth French Data Library](https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html).
- **Benchmark:** S&P 500 (`^GSPC`), downloaded for comparison. The regressions use the Kenneth French market factor rather than the S&P 500 return series.

## Methods

### Return Preparation

Daily adjusted prices are resampled to month-end prices. Monthly simple returns are calculated as the percentage change from the previous month. Portfolio excess returns subtract the matching monthly risk-free rate.

### Portfolio Construction

1. **Equal weight:** each stock receives a 20% allocation.
2. **Inverse variance:** weights are proportional to the inverse of each stock's historical monthly return variance.
3. **Minimum variance:** SciPy's SLSQP optimizer minimizes portfolio variance using the full covariance matrix. Weights sum to 100% and each weight is bounded between 0% and 100%, preventing short selling.

These calculations apply fixed target weights to each month's stock returns, representing monthly rebalancing without transaction costs. Risk-based weights are estimated using the same full sample over which performance is measured.

### Factor Models

Ordinary least squares regressions are estimated with `statsmodels`:

- **CAPM:** portfolio excess return explained by market excess return, with an intercept.
- **Fama–French three-factor model:** portfolio excess return explained by market, size, and value factors, with an intercept.

The analysis compares alpha, market beta, R-squared, and factor p-values. It also calculates mean monthly returns, monthly volatility, and annualized Sharpe ratios using monthly excess returns and square-root-of-12 scaling.

## Results

The following values come from the saved notebook outputs. They describe historical, in-sample results, rather than forecasts.

### Portfolio Performance

| Portfolio | Mean monthly return | Monthly volatility | Annualized Sharpe ratio |
| --- | ---: | ---: | ---: |
| Equal weight | 4.56% | 12.25% | 1.223 |
| Inverse variance | 4.64% | 10.91% | 1.400 |
| Minimum variance | 4.70% | 10.04% | 1.542 |

Inverse-variance weighting reduced monthly volatility by approximately 11% relative to equal weighting. Minimum-variance weighting reduced it by approximately 18%. Both risk-based portfolios had slightly higher average monthly returns in this sample, although minimizing variance does not guarantee higher returns.

### Model Comparison

| Portfolio | CAPM R-squared | Three-factor R-squared | CAPM market beta | Three-factor market beta |
| --- | ---: | ---: | ---: | ---: |
| Equal weight | 0.4940 | 0.5245 | 1.8353 | 1.7867 |
| Inverse variance | 0.5282 | 0.5819 | 1.6893 | 1.6193 |
| Minimum variance | 0.4767 | 0.5476 | 1.4759 | 1.3872 |

- The three-factor model had higher in-sample R-squared for all three portfolios. R-squared measures model fit, not investment performance, and adding predictors can increase it without establishing a statistically significant improvement.
- Market exposure was statistically significant across all three portfolios, with estimated market betas above one.
- HML coefficients were negative for every portfolio. The value factor was statistically significant at the 5% level for the inverse-variance and minimum-variance portfolios, with p-values of 0.0069 and 0.0024. It was not significant at that level for the equal-weight portfolio (p = 0.0600).
- SMB was not statistically significant at the 5% level for any portfolio.
- Three-factor monthly alphas were positive and statistically significant under the notebook's conventional OLS standard errors: approximately 2.41%, 2.76%, and 3.15%, respectively. These estimates are sample-dependent and do not establish future outperformance.

### Minimum-Variance Allocation

| Stock | Weight |
| --- | ---: |
| NVIDIA | 22.59% |
| AMD | 0.00% |
| Broadcom | 61.58% |
| Micron | 9.59% |
| Intel | 6.24% |

The optimized allocation illustrates a tradeoff: reducing estimated volatility can produce substantial concentration in a single stock.

## Files

- **`main.ipynb`** — research notebook containing data collection, portfolio construction, regression analysis, saved results, and an additional NVIDIA/S&P 500 daily-return exploration.
- **`monthly_returns.csv`** — monthly returns exported by the notebook. The current export occurs before the later strategy comparison, so it contains individual stock returns, the S&P 500, and the initial equal-weight portfolio rather than all final portfolio series.

## How to Run

1. Download or clone this repository.
2. Install the notebook dependencies in your Python environment:

   ```bash
   python -m pip install yfinance pandas numpy matplotlib statsmodels scipy jupyter
   ```

3. Open `main.ipynb` in VS Code with Jupyter support, or launch Jupyter:

   ```bash
   jupyter notebook
   ```

4. Select the Python environment where the dependencies are installed, then run the notebook cells from top to bottom.

An internet connection is required to download prices and factor data. Running the notebook writes `monthly_returns.csv` in the current working directory. Updated source data or package versions may produce results that differ from the saved outputs. Pandas must support the `ME` month-end resampling alias used in the notebook.

## Limitations and Future Work

- The analysis uses five selected stocks from one sector and does not represent a broadly diversified market portfolio.
- Full-sample weight estimation and evaluation introduce look-ahead bias. The results are descriptive comparisons, not an out-of-sample trading backtest.
- Transaction costs, taxes, and trading constraints beyond long-only weights are excluded.
- Regressions use conventional, nonrobust standard errors. Robust inference and residual diagnostics would strengthen the statistical analysis.
- Future work could use rolling estimation windows, out-of-sample evaluation, cumulative returns, and maximum drawdown comparisons.

This repository presents academic research and does not provide investment recommendations.
