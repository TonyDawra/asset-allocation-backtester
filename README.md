# Dynamic Asset Allocation Backtester

**A Python-based quantitative finance project for constructing, backtesting, and comparing multi-asset investment strategies.**

This project implements a monthly, out-of-sample backtesting framework to explore whether portfolio optimization can improve risk-adjusted performance over traditional allocation methods. It compares **Markowitz mean-variance optimization**, **Michaud resampling**, and a **regime-sensitive Black–Litterman model** against simple investment benchmarks, while accounting for transaction costs and changing market conditions.

Developed as a **Financial Markets Analytics** final project.

## Project Highlights

- **Six portfolio strategies** evaluated in a consistent backtesting framework.
- **17 risky ETF tickers** spanning equities, bonds, commodities, real estate, and frontier markets, plus **SHV** as a cash/short-Treasury proxy.
- **60-month rolling estimation window**, with decisions based only on information available before each test month.
- **Long-only optimization** with a 30% maximum allocation per risky asset and a 50% maximum per asset group.
- **Dynamic allocation between risky portfolios and SHV** using a capital-allocation formula based on expected excess return, portfolio variance, and risk aversion.
- **Asset-specific transaction costs**, turnover tracking, performance metrics, and portfolio visualizations.
- **Sensitivity analysis** for the Black–Litterman model and its exposure to Pakistan equities.

## Strategies Compared

| Strategy | Description |
| --- | --- |
| **SPY Buy-and-Hold** | Passive S&P 500 ETF benchmark. |
| **60/40 SPY/TLT** | Monthly-rebalanced portfolio of 60% U.S. equities and 40% long-term U.S. Treasuries. |
| **Equal Weight** | Equally allocates across eligible risky assets before the cash-allocation step. |
| **Markowitz Max Sharpe** | Finds the constrained risky-asset portfolio with the highest estimated Sharpe ratio. |
| **Michaud Resampled** | Averages optimal weights across 1,000 simulated return samples per rebalance to reduce estimation sensitivity. |
| **Black–Litterman** | Combines equilibrium expected returns with investor views, including a volatility-dependent confidence adjustment for Pakistan equities. |

The optimized strategies and equal-weight portfolio use a dynamic SHV cash allocation. **SPY Buy-and-Hold and 60/40 are kept as direct benchmarks without that cash overlay.**

## Data and Methodology

Monthly adjusted-price data are downloaded from **Yahoo Finance** with `yfinance`.

| Parameter | Setting |
| --- | --- |
| Return data | January 2005 – June 2025 |
| Out-of-sample backtest | January 2010 – June 2025 |
| Rebalancing frequency | Monthly |
| Estimation window | Previous 60 months |
| Minimum asset history | 24 monthly observations |
| Maximum missing data per rolling window | 20% |
| Risk-free/cash proxy | SHV, using a lagged trailing 12-month return estimate |
| Michaud simulations | 1,000 per rebalance |
| Black–Litterman prior uncertainty (`tau`) | 0.025 |
| One-way transaction cost assumptions | 0.05%–0.15%, depending on asset |

The investable universe is determined **point in time**, so newly launched ETFs do not need a full history across the entire sample. Missing prices are handled within each asset's available trading history. To avoid look-ahead bias, portfolio weights for month *t* are estimated using returns through month *t − 1*.

### Frontier-market component

The project includes **Pakistan equity exposure** through `XBAK.L` as its frontier-market case study. Black–Litterman incorporates three relative views:

1. Emerging-market equities outperform developed ex-U.S. equities by **3% annualized**.
2. SPY outperforms TLT by **4% annualized**.
3. Pakistan equities outperform the EEM emerging-markets ETF by **4% annualized**, when the relevant assets are eligible.

Confidence in the Pakistan view changes with trailing SPY volatility: it increases when annualized volatility is below **15%** and decreases when it exceeds **20%**.

**Data note:** If Yahoo Finance does not return `XBAK.L`, the notebook attempts to download `PAK` as a fallback and retains the `XBAK.L` display label. The source is recorded in the notebook's data-quality report; results should be interpreted with this substitution in mind.

## Backtest Results

The table below summarizes the **net-of-transaction-cost** results saved in the notebook for January 2010–June 2025.

| Strategy | Annualized Return | Annualized Volatility | Sharpe Ratio | Max Drawdown |
| --- | ---: | ---: | ---: | ---: |
| SPY Buy-and-Hold | **13.72%** | 14.55% | 0.89 | −23.93% |
| 60/40 SPY/TLT | 9.74% | 9.86% | 0.89 | −26.22% |
| Equal Weight | 3.87% | 7.64% | 0.39 | −19.82% |
| **Markowitz Max Sharpe** | 8.46% | 6.71% | **1.10** | **−13.72%** |
| Michaud Resampled | 7.26% | **6.37%** | 0.98 | −14.29% |
| Black–Litterman | 3.87% | 9.24% | 0.34 | −27.34% |

**Key findings:** Markowitz achieved the highest risk-adjusted return and smallest maximum drawdown in this historical test. SPY Buy-and-Hold delivered the highest absolute annualized return but with higher volatility. Michaud resampling produced the lowest volatility, while the implemented Black–Litterman views underperformed over the test period. These are historical backtest observations, not guarantees of future performance.

## Visualizations and Analysis

The notebook generates:

- Gross versus net cumulative portfolio returns.
- Portfolio drawdowns over time.
- Dynamic risky-asset versus SHV allocations.
- Black–Litterman portfolio weights through time.
- Correlations across the ETF universe.
- A Capital Market Line illustration.
- Pakistan equity allocation versus market-volatility regimes.
- Sensitivity analyses for estimation windows, risk aversion, and `tau`.

## Getting Started

**1. Clone the repository**

```bash
git clone https://github.com/TonyDawra/Dynamic_Asset_Allocation_Backtester.git
cd Dynamic_Asset_Allocation_Backtester
```

**2. Install the dependencies**

Use Python 3.10+ with Jupyter Notebook:

```bash
pip install numpy pandas scipy matplotlib seaborn yfinance tqdm notebook
```

`PyPortfolioOpt` is optional; the notebook checks whether it is installed.

**3. Launch the notebook**

```bash
jupyter notebook Dynamic_Asset_Allocation_Backtester.ipynb
```

Run the cells in order to download the data, execute the monthly backtest, and reproduce the charts and tables. **Note:** The 1,000-simulation Michaud strategy is computationally intensive, and fresh Yahoo Finance downloads can change the reproduced results.

## Repository Structure

```text
Dynamic_Asset_Allocation_Backtester/
├── Dynamic_Asset_Allocation_Backtester.ipynb  # Analysis, models, backtest, and charts
└── README.md                                 # Project documentation
```

## Limitations

- The analysis relies on historical ETF data and the availability and consistency of Yahoo Finance prices.
- SHV is an **ETF-based proxy**, not a directly observed risk-free borrowing/lending rate.
- Portfolio returns use modeled transaction costs; they do not fully capture market impact, taxes, or live execution conditions.
- Black–Litterman results depend on subjective return views and confidence assumptions, particularly for Pakistan equities.
- Backtested results may not generalize to future market regimes.

## Author

**Tony Dawra**  
[GitHub Profile](https://github.com/TonyDawra)

---

*For educational and research purposes only. Nothing in this repository constitutes financial or investment advice.*
