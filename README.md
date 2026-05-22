# Black-Scholes-Model-Option-Fair-Value-Computation-and-Choosing-Option

# NDX Call Option Selector — Black-Scholes Model

This project applies the **Black-Scholes model** to evaluate and select the most suitable European call option on the **NASDAQ-100 Index (^NDX)** based on consumer's preference (Strike price as close as current price), based on real market data fetched via `yfinance`.

-----

## What It Does

1. **Downloads 1 year of NDX historical price data** to compute annualised historical volatility.
1. **Fetches all available option expiry dates** from Yahoo Finance, filtering out expired contracts.
1. **Constructs a composite risk-free interest rate** for each expiry by combining US Treasury bond rates across multiple tenors (1M to 3Y), weighted by the number of bond periods that fit within the time to expiration.
1. **Applies the Black-Scholes formula** to compute the theoretical fair value of the at-the-money (ATM) call for each expiry.
1. **Computes DC** (market premium minus fair value) to identify overpriced vs underpriced options.
1. **Plots DC and total investment cost** (Premium + strike price) across all expiries to help select the best contract.

-----

## Assumptions

- **Lognormal distribution**: NDX price follows a lognormal distribution based on the principle that asset prices cannot take a negative value; they are bounded by zero.
- NDX market follows **random walk**, **frictionless market**
- NDX return follows **normal distribution**,implying constant market  volatility constant over time.
- **European-style options** (exercise at maturity date only)
- **No dividends** paid on the underlying
-  **No arbitrage**
- Volatility estimated from 1 year of daily log returns (historical, not implied)
- Risk-free rate constructed from US Treasury nominal rates as of **19 May 2026**
- Buyer wishes **strike price** of chosen call option to be **as close as current price as possible**

-----

## Requirements

```
yfinance
numpy
scipy
pandas
matplotlib
```

Install with:

```bash
pip install yfinance numpy scipy pandas matplotlib
```

-----

## Usage

```bash
python ndx_black_scholes.py
```

The script will print each expiry’s theoretical call price vs market last price, then display a plot.

-----

## Output Interpretation

|Variable|Meaning                                                                    |
|--------|---------------------------------------------------------------------------|
|`DC > 0`|Market premium exceeds fair value → option is **overpriced**               |
|`DC < 0`|Market premium is below fair value → option is **underpriced**             |
|`Cost`  |`Premium + Strike` — a combined measure of total outlay; **prefer lower values**|

-----

## Risk-Free Rate Construction

Treasury rates (as of 19/05/2026) are mapped to the following tenors:

|Tenor (days)|Rate |
|------------|-----|
|31          |3.65%|
|46          |3.65%|
|61          |3.65%|
|92          |3.65%|
|122         |3.74%|
|183         |3.75%|
|365         |3.79%|
|730         |4.04%|
|1096        |4.11%|

For each option expiry, the composite rate is computed as the product of `(1 + r_i)^n_i` across all tenors that fit within the time to expiration, then converted to a single effective rate.
