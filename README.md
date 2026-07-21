# Yield Curve Factor Models and Housing Market Forecasting

A test of whether Treasury yield curve shape predicts housing starts, mortgage rates, and home prices, using Nelson-Siegel factor extraction and 15 years of out-of-sample forecasting (2011-2025).

Adapted from a final research paper for Econ 424: Advanced Analytics, Rutgers University.

## Overview

This project investigates whether latent factors extracted from the U.S. Treasury yield curve (level and slope) carry statistically meaningful out-of-sample predictive power for three dimensions of housing market activity:

- Housing starts (HOUST)
- 30-year fixed mortgage rates (MORTGAGE30US)
- The Case-Shiller national home price index (CSUSHPINSA)

Five regression models of increasing complexity, from a naive intercept-only benchmark up to a full model combining both yield curve factors with an AR(1) term, are estimated in-sample and then evaluated out-of-sample using a recursive expanding-window forecasting scheme. Forecast accuracy is compared using Mean Square Forecast Error (MSFE) and formally tested for statistical significance using the Diebold-Mariano test.

**Key finding:** the AR(1) benchmark produces the lowest out-of-sample forecast error across all three targets, with the advantage statistically significant for housing starts and home prices. This is consistent with the efficient markets hypothesis: yield curve information appears to already be priced in by the time it would otherwise be useful for prediction.

## Methodology

All data is sourced from the Federal Reserve Economic Database (FRED). The full sample covers 290 monthly observations from August 2001 through December 2025. The yield curve is constructed from nine Treasury constant maturity rate series (1-month through 10-year). Housing starts and the Case-Shiller index are log-differenced; the mortgage rate and Nelson-Siegel factors are first-differenced. The Case-Shiller index is shifted forward two months to account for its publication lag.

The Nelson-Siegel model is fit via OLS at each month to extract level and slope factors (decay parameter fixed at λ = 0.0609, following Diebold and Li, 2006). Out-of-sample evaluation uses a recursive expanding window: the model is re-estimated at each step from an initial 2001-2010 training period, generating one-step-ahead forecasts across January 2011 through December 2025 (180 months).

## Repository Structure

```
.
├── notebooks/
│   └── yc_analysis.ipynb        # Main analysis notebook
├── data/
│   └── (data fetched live from FRED API at runtime)
├── output/
│   └── yc_analysis.pdf         # Exported notebook, styled for viewing
├── README.md
└── .gitignore
```

## How to Run

1. Install dependencies:
   ```
   pip install pandas numpy fredapi matplotlib statsmodels dieboldmariano python-dotenv
   ```

2. Get a free FRED API key from https://fred.stlouisfed.org/docs/api/api_key.html

3. Open `notebooks/analysis.ipynb` and locate the data-loading cell. Comment out these two lines:
   ```python
   load_dotenv()
   fred = Fred(api_key=os.getenv('FRED_API_KEY'))
   ```
   Then uncomment this line and paste your API key directly inside the parentheses:
   ```python
   #fred = Fred(api_key='')
   ```

4. Run all cells top to bottom. The notebook will fetch data live from FRED, run the Nelson-Siegel factor extraction, estimate all five models in-sample, run the recursive out-of-sample forecasting loop, and compute MSFE and Diebold-Mariano test results.

## Data Sources

| Series | FRED Code | Description |
|---|---|---|
| Treasury yields | DGS1MO, DGS3MO, DGS6MO, DGS1, DGS2, DGS3, DGS5, DGS7, DGS10 | Constant maturity rates, 1-month to 10-year |
| Housing starts | HOUST | New residential construction, thousands of units (SAAR) |
| Mortgage rate | MORTGAGE30US | 30-year fixed mortgage rate |
| Home prices | CSUSHPINSA | Case-Shiller national home price index |

## References

- Diebold, F. X., & Li, C. (2006). Forecasting the term structure of government bond yields. *Journal of Econometrics*, 130(2), 337-364.
- Diebold, F. X., & Mariano, R. S. (1995). Comparing predictive accuracy. *Journal of Business & Economic Statistics*, 13(3), 253-263.
- Nelson, C. R., & Siegel, A. F. (1987). Parsimonious modeling of yield curves. *The Journal of Business*, 60(4), 473-489.
