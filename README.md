# US Macro VAR: GDP, Inflation, and Unemployment

A three-variable Vector Autoregression (VAR) model estimated in Gretl/hansl to analyze the dynamic relationships between US economic growth, inflation, and unemployment.

## Economic Question

How do GDP growth, inflation, and unemployment interact dynamically in the United States? Specifically:
- Does unemployment Granger-cause GDP growth?
- How does a shock to inflation affect unemployment over time?
- Is there evidence of Okun's Law in the impulse responses?

## Data

All data is publicly available from the Federal Reserve Economic Data (FRED) database:

| Variable | FRED Series | Description | Frequency |
|----------|-------------|-------------|-----------|
| Real GDP | [GDPC1](https://fred.stlouisfed.org/series/GDPC1) | Real Gross Domestic Product | Quarterly |
| Inflation | [CPIAUCSL](https://fred.stlouisfed.org/series/CPIAUCSL) | Consumer Price Index (All Urban Consumers) | Quarterly |
| Unemployment | [UNRATE](https://fred.stlouisfed.org/series/UNRATE) | Civilian Unemployment Rate | Quarterly |

- **Sample period:** 1970Q1 – 2026Q2
- **Observations:** 221 (after differencing)
- **Transformations:**
  - GDP growth: `g_gdp = diff(log(GDPC1))`
  - Inflation: `infl = diff(log(CPIAUCSL))`
  - Unemployment: used in levels (already a rate)

## Method

1. **Stationarity testing:** Augmented Dickey-Fuller (ADF) tests with constant, automatic lag selection (AIC).
2. **Lag order selection:** VAR lag selection using AIC, BIC, and HQC criteria (max 8 lags).
3. **VAR estimation:** VAR(4) with constant term, estimated via OLS.
4. **Granger causality:** F-tests of joint significance of lagged variables.
5. **Impulse Response Functions (IRF):** Cholesky decomposition with ordering `g_gdp → infl → UNRATE`, 12-quarter horizon, 95% confidence intervals.

## Key Findings

### Stationarity (ADF tests)
All three variables are stationary at the 5% level:
- `g_gdp`: p < 0.001
- `infl`: p = 0.016
- `UNRATE`: p = 0.008

### Granger Causality
- **Unemployment → other variables:** F(4, 208) = 11.70, p < 0.001 
- **GDP growth → other variables:** F(4, 208) = 6.09, p = 0.0001 
- **Inflation → other variables:** F(4, 208) = 2.33, p = 0.057 (marginally insignificant)

### Impulse Responses
- A **positive shock to GDP growth significantly reduces unemployment** over a 12-quarter horizon — consistent with Okun's Law. The effect is statistically significant (confidence bands exclude zero).
- A **shock to inflation** raises unemployment in the short run but the effect fades after ~4 quarters.
- **Inflation is persistent:** a shock to inflation remains positive for several quarters.

See `impulse_responses.pdf` for the full IRF grid.
