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
- Risk-free rate constructed from US Treasury nominal rates as of **19 May 2026** from [2]
- Buyer wishes **strike price** of chosen call option to be **as close as current price as possible**
- Buyer has positive outlook towards NDX performance in 5 years

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

|i |Tenor (days)|Rate |
|--|------------|-----|
|1 |31          |3.65%|
|2 |46          |3.65%|
|3 |61          |3.65%|
|4 |92          |3.65%|
|5 |122         |3.74%|
|6 |183         |3.75%|
|7 |365         |3.79%|
|8 |730         |4.04%|
|9 |1096        |4.11%|

For each option expiry, the composite rate is computed as the product of `(1 + r_i)^n_i` across all tenors that fit within the time to expiration, then converted to a single effective rate. r_i is the rate of treasury bond corresponds to ith tenor. n_i is number of treasury bond with ith tenor.

-----

## Results

1) Risk Free Rate, r
   
| NDX Call Option Maturity Date | Risk Free Rate |
|-------------------------------|---------------:|
| 2026-05-26 | 0.000000 |
| 2026-05-27 | 0.000000 |
| 2026-05-28 | 0.000000 |
| 2026-05-29 | 0.000000 |
| 2026-06-01 | 0.000000 |
| 2026-06-02 | 0.000000 |
| 2026-06-03 | 0.000000 |
| 2026-06-04 | 0.000000 |
| 2026-06-05 | 0.000000 |
| 2026-06-08 | 0.000000 |
| 2026-06-09 | 0.000000 |
| 2026-06-10 | 0.000000 |
| 2026-06-11 | 0.000000 |
| 2026-06-12 | 0.000000 |
| 2026-06-15 | 0.000000 |
| 2026-06-16 | 0.000000 |
| 2026-06-17 | 0.000000 |
| 2026-06-18 | 0.000000 |
| 2026-06-22 | 0.000000 |
| 2026-06-26 | 0.036500 |
| 2026-06-30 | 0.036500 |
| 2026-07-10 | 0.074332 |
| 2026-07-17 | 0.074332 |
| 2026-07-31 | 0.154190 |
| 2026-08-21 | 0.154190 |
| 2026-08-31 | 0.285243 |
| 2026-09-18 | 0.285243 |
| 2026-09-30 | 0.432419 |
| 2026-10-16 | 0.484702 |
| 2026-11-20 | 0.538894 |
| 2026-12-18 | 0.842782 |
| 2026-12-31 | 0.910043 |
| 2027-01-15 | 0.979760 |
| 2027-02-19 | 1.206467 |
| 2027-03-19 | 1.457001 |
| 2027-04-16 | 1.735982 |
| 2027-06-17 | 2.656366 |
| 2027-09-17 | 3.699318 |
| 2027-12-17 | 5.737885 |
| 2028-12-15 | 23.728870 |
| 2029-12-21 | 89.819073 |

2) NDX Call Option Information
<img width="1189" height="590" alt="image" src="https://github.com/user-attachments/assets/f1a266c9-554e-4187-9838-59882c58e3a0" />
Figure 1: Black-Scholes Call Option Price (Fair Value), Strike Price and Premium of NDX

<img width="1189" height="590" alt="image" src="https://github.com/user-attachments/assets/9e462e32-deaf-4957-a643-cc769f7500f2" />
Figure 2: In this figure, it is typically desirable to choose the call option with maturity date on 2027-06-17 due to its lowest overall cost and negative DC (undervalued premium), despite not being the lowest,  which potrays potential opportunities for higher returns, by assuming it will rise beyond the strike price. Besides, the buyer may lose its premium relatively lower than fair value if he decided not to exercise the option in the future. 

-----

## References

[1]https://corporatefinanceinstitute.com/resources/derivatives/black-scholes-merton-model/

[2]https://tradingeconomics.com/united-states/government-bond-yield

[3]https://www.asx.com.au/content/dam/asx/investors/investment-tools-and-resources/online-courses/options/options-course-3.pdf

[4]https://www.columbia.edu/~mh2078/FoundationsFE/BlackScholes.pdf

