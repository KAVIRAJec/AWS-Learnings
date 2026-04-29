## Amazon Forecast

Amazon Forecast is a fully managed **time-series ML forecasting** service — generates accurate predictions without requiring ML expertise. Based on the same technology Amazon uses for its own demand forecasting.

**Key Concepts:**

- **Dataset Group**: Container for all related datasets, predictors, and forecasts for one use case.
- **Datasets**: Time-series data you provide:

| Dataset Type | Required | Description |
|---|---|---|
| **Target Time Series** | Yes | Historical values of what to predict (sales units, energy demand, server load) |
| **Related Time Series** | No | External variables that influence the target (price changes, promotions, weather, holidays) |
| **Item Metadata** | No | Static attributes of items (category, brand, store region) |

- **AutoPredictor (default)**: Automatically selects and ensembles the best algorithm(s) for your data — handles missing values, outliers, and seasonal patterns. Recommended for all new predictors.
- **Algorithms Supported**: DeepAR+, CNN-QR, Prophet, NPTS, ARIMA, ETS — Forecast chooses the best mix.
- **Forecast Horizon**: How far into the future to predict — defined at predictor training time.
- **Forecast Frequency**: Granularity of predictions — hourly, daily, weekly, monthly, or custom.
- **Quantile Forecasts**: Returns predictions at multiple probability quantiles:
   - **P10**: 10% chance actual value is below this — optimistic lower bound.
   - **P50**: Median prediction — most likely outcome.
   - **P90**: 90% chance actual value is below this — conservative upper bound.
   - Use quantiles to balance overstock (P90) vs. stockout risk (P10).

**Explainability:**
- **Forecast Explainability**: Identifies which attributes (price, promotions, day-of-week) had the most impact on a specific forecast — feature importance per item and time point.
- Helps business teams understand and trust the model output.

**What-If Analysis:**
- Run hypothetical scenarios — e.g., "what happens to demand if we drop the price by 10%?" — without retraining the model.
- Creates a new Forecast based on modified Related Time Series values.

**Use cases:** Retail demand and inventory forecasting, cloud resource capacity planning, financial revenue forecasting, supply chain optimization, energy/utility demand planning, call center staffing.
