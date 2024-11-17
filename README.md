# Portfolio Risk Modeling - VaR and CVaR

This project implements risk modeling for a stock portfolio using three different methods: Historical VaR & CVaR, Parametric VaR & CVaR, and Monte Carlo Simulation. It also provides a detailed analysis of the portfolio's returns distribution and evaluates risk at a 95% confidence level.

## Introduction

Risk management is an essential aspect of portfolio management, and this project focuses on calculating and visualizing risk using various techniques:

- **Value at Risk (VaR)**: Measures the maximum potential loss in value of the portfolio within a given confidence level.
- **Conditional Value at Risk (CVaR)**: Estimates the average loss beyond the VaR threshold.
- **Monte Carlo Simulation**: Uses random sampling to simulate potential future portfolio returns, allowing us to assess risk based on a large number of scenarios.

The stock portfolio includes four assets: Apple (AAPL), Meta (META), Citigroup (C), and Disney (DIS). The risk metrics are calculated at a 95% confidence level.

## Methodology

### Historical VaR & CVaR

The **Historical VaR** is calculated by finding the 5th percentile of the portfolio’s returns:  
<br>![Historical VaR](https://quicklatex.com/cache3/f9/ql_1afa065f04c23f761bc3f5a87476a3f9_l3.png)

The **Conditional Value at Risk (CVaR)** is the average loss in the tail of the distribution beyond the VaR threshold:   
<br>![Conditional VaR](https://quicklatex.com/cache3/a1/ql_b4828e6c66207dad845dccb393ad12a1_l3.png)

### Parametric VaR & CVaR

The **Parametric VaR** is based on the assumption that returns follow a normal distribution. It is calculated using the mean ![](https://quicklatex.com/cache3/24/ql_cda21d70178580e8ac56e025bcc79824_l3.png) and standard deviation ![](https://quicklatex.com/cache3/51/ql_3fe3e08ba272c43c8b7617c6fe6e7351_l3.png) of the portfolio’s returns, along with the Z-score for the given confidence level ![](https://quicklatex.com/cache3/9e/ql_fba77544fc998e494bf388b1a3f6b99e_l3.png):  
<br>![Parametric VaR](https://quicklatex.com/cache3/d0/ql_d3027e046e3a8abdd4accfdc7ef729d0_l3.png)

Where ![](https://quicklatex.com/cache3/9e/ql_fba77544fc998e494bf388b1a3f6b99e_l3.png) is the critical value corresponding to the confidence level. For a 95% confidence level, ![](https://quicklatex.com/cache3/9e/ql_fba77544fc998e494bf388b1a3f6b99e_l3.png) =  1.645

The **CVaR** is calculated by averaging the expected returns below the VaR level:  
<br>![CVaR](https://quicklatex.com/cache3/a7/ql_5c2250755472fa374c38a94ce06998a7_l3.png)

### Monte Carlo Simulation

The Monte Carlo Simulation models potential future outcomes of the portfolio by randomly sampling returns from a normal distribution and simulating a large number of scenarios. The portfolio returns at each time step are calculated as a weighted sum of the returns of the individual assets:  
<br>![](https://quicklatex.com/cache3/90/ql_c01f9e925e7c962e6dc5eb275bb75190_l3.png)

Simulated VaR and CVaR are calculated from the portfolio returns in the simulated scenarios.  
<br>![](https://quicklatex.com/cache3/66/ql_55567a879acd702d748187e4b6f2d366_l3.png)

## Data Acquisition

Data for this project was retrieved from Yahoo Finance for the following tickers: AAPL, META, C, and DIS. The data covers the past year and includes the adjusted closing price for each asset.

```python
df = yf.download(['AAPL','META', 'C', 'DIS'], start=start_date, end=end_date)['Adj Close']
```

The data is then cleaned by dropping any missing values and calculating the daily percentage change in the prices.

## Approach

### Historical VaR & CVaR

The historical VaR is calculated by finding the 5th percentile of the portfolio’s returns.

```python
var = port_returns.quantile(q=1-confidence_level)
cvar = port_returns[port_returns <= var].mean()
```

The results are displayed as follows:

```plaintext
Historical VaR at 0.95 confidence level: -1578.54 (-1.58%)
Historical CVaR at 0.95 confidence level: -2283.19 (-2.28%)
```

### Parametric VaR & CVaR

The parametric VaR is calculated using the Z-score for a 95% confidence level and the portfolio's standard deviation and mean returns.

```python
z_score = norm.ppf(q=1-confidence_level)
var = - (norm.ppf(confidence_level)*port_std_dev - port_mean_return)
cvar = 1 * (port_mean_return - port_std_dev * (norm.pdf(z_score)/(1-confidence_level)))
```

The parametric VaR and CVaR results are:

```plaintext
Parametric VaR at 0.95 confidence level: -1597.35 (-1.60%)
Parametric CVaR at 0.95 confidence level: -2041.27 (-2.04%)
```

### Monte Carlo Simulation

In the Monte Carlo Simulation, 400 scenarios are simulated to predict the portfolio’s future returns. The simulations use a Cholesky decomposition of the covariance matrix to generate correlated returns for each stock in the portfolio.

```python
L = np.linalg.cholesky(cov_matrix)
daily_pct_change = meanM + np.inner(L, Z)
```

Monte Carlo VaR and CVaR are calculated from the simulated portfolio returns:

```plaintext
Monte Carlo VAR at 95% confidence level: $108473.76 (-1.57%)
Monte Carlo CVA at 95% confidence level: $102057.64 (-2.02%)
```

## Results

The risk measures for the portfolio were calculated using three different methods:

- **Historical VaR and CVaR**: Estimates the risk based on actual historical data.
- **Parametric VaR and CVaR**: Assumes returns are normally distributed and calculates risk using statistical parameters.
- **Monte Carlo Simulation**: Simulates future portfolio outcomes based on random sampling and estimates risk from simulated scenarios.

```plaintext
Historical VaR: -1578.54
Parametric VaR: -1597.35
Monte Carlo VaR: -108473.76
```

## Visualizations

1. **Adjusted Close Price for Sample Stock Tickers**
   - Displays the adjusted closing price for AAPL, META, C, and DIS over the past year.
   
   ![Stock Price Plot](https://github.com/1Aditya7/VaR-and-CVaR-Modelling/blob/main/VaRandCVaRMedia/adjclose.png)

2. **Apple Percentage Change Histogram**
   - A histogram of daily percentage changes for Apple.

   ![Apple Change Histogram](https://github.com/1Aditya7/VaR-and-CVaR-Modelling/blob/main/VaRandCVaRMedia/appleHist.png)

3. **Portfolio Returns Distribution**
   - A histogram showing the distribution of portfolio returns, with VaR and CVaR marked.

   ![Portfolio Returns Distribution](https://github.com/1Aditya7/VaR-and-CVaR-Modelling/blob/main/VaRandCVaRMedia/ptReturns.png)

4. **Monte Carlo Simulation of Portfolio Change**
   - A plot showing the simulated portfolio percentage changes, with VaR and CVaR levels.

   ![Monte Carlo Simulation](https://github.com/1Aditya7/VaR-and-CVaR-Modelling/blob/main/VaRandCVaRMedia/mcSims.png)

## Limitations and Future Scope

- **Assumption of Normality**: The parametric VaR assumes that returns follow a normal distribution, which may not capture extreme market events.
- **Monte Carlo Assumptions**: While Monte Carlo simulations are powerful, they rely on the assumption that historical data and volatility patterns will continue into the future.
- **Data Quality**: The results are sensitive to the data used. More granular data and longer time frames could provide a more accurate risk analysis.

### Future Scope:
- Implementing more sophisticated methods like GARCH models for volatility forecasting.
- Enhancing Monte Carlo simulations with more realistic models for asset returns.
- Extending the analysis to include more assets and test the portfolio under different market conditions.

## Conclusions

This project demonstrates how to calculate and visualize key risk metrics for a stock portfolio using historical, parametric, and Monte Carlo simulation methods. The results provide a comprehensive view of the potential risks faced by the portfolio and can be used by investors to manage exposure to market fluctuations.
