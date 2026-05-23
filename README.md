# Black -Scholes Model: NASDAQ-100 (^NDX or NDX) European Call Option Price Evaluation 

This project applies the **Black-Scholes model** to evaluate and select the most suitable European call option on the **NASDAQ-100 Index (^NDX)** based on consumer's preference (Strike price as close as current price), based on real market data fetched via `yfinance`.

-----

## What It Does

1. **Downloads 1 year of NDX historical price data starting from 2026-05-19** to compute annualised historical volatility (standard deviation of daily returns based on closing price).
1. **Fetches all available ^NDX call option expiry dates** from Yahoo Finance, filtering out expired contracts.
1. **Constructs a composite risk-free interest rate** for each expiry weighted by the number of bond periods that fit within the time to expiration. Uses US Treasury bond rates across multiple tenors (1month to 3years) if call option's days till exercisable date falling in range of the stated bond's tenor and yield from holding cash (0%) if call option's  days till exercisable date is less than 31 days. 
1. **Applies the Black-Scholes formula** to compute the theoretical fair value of the at-the-money (ATM) call for each expiry.
1. **Computes DC** (market premium - fair value) to identify overpriced vs underpriced options.
1. **Plots DC and total investment cost** (market premium + strike price) across all expiries to help select the best contract.

-----

## Assumptions

- **Lognormal distribution**: NDX price follows a lognormal distribution based on the principle that asset prices cannot take a negative value; they are bounded by zero.
- NDX market follows **random walk**, **frictionless market**
- NDX return follows **normal distribution**,implying constant market  volatility constant over time.
- **European-style options** (exercise at maturity date only)
- **No dividends** paid on the underlying
-  **No arbitrage**
- Volatility estimated from 1 year of daily log returns (historical, not implied)
- Risk-free rate constructed from US Treasury nominal rates as of **19 May 2026** from [2] does not change overtime.
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

## Output Interpretation

|Variable|Meaning                                                                    |
|--------|---------------------------------------------------------------------------|
|`DC > 0`|Market premium exceeds fair value → option is **overpriced**               |
|`DC < 0`|Market premium is below fair value → option is **underpriced**             |
|`Actual Cost`  |`Premium + Strike Price` — a combined measure of total outlay; **prefer lower values**|

-----

## Black Scholes Model
<img width="701" height="682" alt="image" src="https://github.com/user-attachments/assets/1452f0c8-f408-4bc2-a884-e46a20f4ec6c" />

Sigma = Volatility of ^NDX daily returns in past 1 year 

-----

## Risk-Free Rate (r) Construction

Treasury rates (as of 19/05/2026) and cash holding yield (0%) are mapped to the following tenors:

|i |US Treasury Bonds Tenor (days)|Rate |
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

Example:
Total days from purchasing date till exercisable date = 1000 days
Starting date: 2026-05-19
1000 = [1096(0) + 730(1)+ 365(0) +183(1) + 122(0) + 92(0) + 61(1) + 46(0) + 31(0)]+ 26

So first 974 days will be entitled with (1+0.0411)^1][(1+0.0375)^1][(1+0.0365)^1 -1 of risk free yield, while the last 26 days wull be entitled with 0% of risk free yield. Therefore, the total risk free rate for the call option with days till exercisable date of 1000 days is [(1)(1)(1+0.0411)(1+0.0375)(1+0.0365)]-1

Risk Free Rate, r 
| NDX Call Option Maturity Date | Risk Free Rate |
|-------------------------------|---------------:|
| 2026-05-26 | 0 % |
| 2026-05-27 | 0 %|
| 2026-05-28 | 0 %  |
| 2026-05-29 | 0 %  |
| 2026-06-01 | 0 %  |
| 2026-06-02 | 0 %  |
| 2026-06-03 | 0 %  |
| 2026-06-04 | 0 %  |
| 2026-06-05 | 0 %  |
| 2026-06-08 | 0 %  |
| 2026-06-09 | 0 %  |
| 2026-06-10 | 0 %  |
| 2026-06-11 | 0 %  |
| 2026-06-12 | 0 %  |
| 2026-06-15 | 0 %  |
| 2026-06-16 | 0 %  |
| 2026-06-17 | 0 %  |
| 2026-06-18 | 0 %  |
| 2026-06-22 | 0 % |
| 2026-06-26 | 3.65 % |
| 2026-06-30 | 3.65 % |
| 2026-07-10 | 3.65 % |
| 2026-07-17 | 3.65 % |
| 2026-07-31 | 3.65 % |
| 2026-08-21 | 3.65 % |
| 2026-08-31 | 3.65 % |
| 2026-09-18 | 3.65 % |
| 2026-09-30 | 3.74 % |
| 2026-10-16 | 3.74 %  |
| 2026-11-20 | 7.52651 % |
| 2026-12-18 | 3.75 % |
| 2026-12-31 | 7.536875 % |
| 2027-01-15 | 7.536875 % |
| 2027-02-19 | 7.536875 % |
| 2027-03-19 | 7.536875 % |
| 2027-04-16 | 7.63025 % |
| 2027-06-17 | 3.79 % |
| 2027-09-17 | 7.578335 % |
| 2027-12-17 | 7.682125 % |
| 2028-12-15 | 7.9415 % |
| 2029-12-21 | 8.014125 % |

-----

## Results
   
<img width="1189" height="590" alt="image" src="https://github.com/user-attachments/assets/9d0f2d0a-4d6b-4ce7-80ca-07a6a02fa9c2" />
Figure 1: Black-Scholes Call Option Price (Fair Value), Strike Price and Market Premium of NDX

<img width="1186" height="590" alt="image" src="https://github.com/user-attachments/assets/ed27afa1-2506-4958-a66c-5cdeb09a83da" />
Figure 2: In this figure, it is typically desirable to choose the call option with maturity date on 2027-06-17 due to its lowest overall cost and negative DC (undervalued premium), despite not being the lowest,  which potrays potential opportunities for higher returns, by assuming it will rise beyond the strike price. Besides, the buyer may lose its premium relatively lower than fair value if he decided not to exercise the option in the future. 

-----

## Discussion

🔎 Based on fundamental economics assumption which includes non-satiation, investors prefer enjoying high profit if they can. Therefore, from the assumption of buyers's positive outlook on NDX actual value growth in 5 years, the buyer will likely be benefitted from purchasing the option with matirity date on "2026-07-29", which costs the least among all call options with maturity date displayed in figure figure 1&2 along with the lowest strike price it allows the option buyer to exercise from the, gaining opportunities for greater profit if the buyer intend to sell it after the shorting it. Besides, its negative DC value also indicates that as an opportunity to invest as the actual premium is actually lower than the fair value ohtained from Black Scholes call otion pricing model. 


---

## References

[1]https://corporatefinanceinstitute.com/resources/derivatives/black-scholes-merton-model/

[2]https://tradingeconomics.com/united-states/government-bond-yield

[3]https://www.asx.com.au/content/dam/asx/investors/investment-tools-and-resources/online-courses/options/options-course-3.pdf

[4]https://www.columbia.edu/~mh2078/FoundationsFE/BlackScholes.pdf

