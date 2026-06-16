# African Economic Analysis and Forecasting (1990 to 2024)

A comparative macroeconomic study of seven African economies, Nigeria, South Africa, Egypt, Ethiopia, Algeria, Morocco, and Kenya, built on 35 years of World Bank data. The project moves from raw data collection through cleaning, exploratory analysis, and finally time series forecasting, using Nigeria as the central case study.

## Overview

This notebook answers two questions. First, how do these seven economies compare across GDP, inflation, unemployment, and foreign direct investment between 1990 and 2024. Second, can classical forecasting models predict Nigeria's near term economic path for 2025 and 2026, and how do those predictions hold up against real world outcomes once they become available.

## Data

All data is pulled directly from the World Bank API using the `wbdata` Python library. Five indicators are tracked for each country.

* GDP (current USD)
* GDP growth (annual %)
* Inflation (consumer prices, annual %)
* Unemployment (% of total labor force)
* Foreign direct investment, net inflows (current USD)

The cleaned dataset is exported as `africa_economics_1990_2024.csv` so the API does not need to be queried again for downstream analysis.

## Methodology

### Phase 1: Cleaning and exploration

Missing unemployment values are filled using linear interpolation within each country's own time series. A real outlier is investigated rather than discarded. Egypt's 2024 FDI figure of $46.6B was confirmed genuine but driven entirely by a single transaction, a $35B UAE sovereign wealth deal. That value is capped to a historical norm for modeling purposes while the original figure is preserved separately. The notebook then produces comparative charts for GDP, inflation, unemployment, and FDI, a correlation heatmap across indicators, and a full 2024 scorecard for all seven countries.

### Phase 2: Forecasting Nigeria

Three forecasting approaches are built and compared.

1. **ARIMA**, auto tuned. It produced a 61.3% MAPE because it extrapolated Nigeria's pre 2023 growth trend and could not anticipate the Naira devaluation. This result is treated as a finding about the limits of trend based models when a policy shock occurs, not a modeling failure.
2. **Prophet**, used as a comparison forecast against ARIMA.
3. **VAR (Vector Autoregression)**. This required stationarity testing using the Augmented Dickey Fuller test and several rounds of differencing. GDP growth never became reliably stationary and was modeled separately with ARIMA instead of being forced into the VAR system. VAR produced the best result of the three models, at 37.2% MAPE.

A differencing reversal bug that produced an unrealistic unemployment forecast is identified and corrected in the notebook, with the diagnosis kept visible rather than removed.

## Validation against real outcomes

Once partial 2025 data became available from the World Bank, IMF, and NBS Nigeria, model predictions were checked against actual figures.

| Indicator | Model Prediction | Actual / Estimate (2025) | Directionally Correct |
|---|---|---|---|
| GDP (USD) | $700B | $290B | No, explained by post devaluation structural break |
| Inflation | 32.3% | 23.0% | Yes |
| Unemployment | 5.9% | 3.06% | Yes |
| GDP growth | Continued growth trend | 3.9% | Yes |

Three of four directional calls were correct. The GDP dollar value miss is fully explained by the Naira devaluation, which occurred after the models were trained on historical trend data, and is documented as an expected limitation rather than an error.

## Tech stack

Python, pandas, NumPy, matplotlib, wbdata, pmdarima, Prophet, statsmodels (VAR, ADF test), scikit learn (MAPE, RMSE).

## Repository contents

* `AFRICA_STOCKS.ipynb`, the full notebook with code, narrative, and 8 generated charts
* `africa_economics_1990_2024.csv`, the cleaned master dataset

## Key takeaway

This project treats forecast error as information rather than failure. Each model's weaknesses are diagnosed, documented, and used to choose the next approach, and the final validation step shows where the models held up and where a real world structural break, the Naira devaluation, broke a purely trend based prediction.
