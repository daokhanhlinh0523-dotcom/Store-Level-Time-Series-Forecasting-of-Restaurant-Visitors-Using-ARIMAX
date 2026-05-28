# Final Results Summary

## Final Notebook

The final notebook for grading is:

- `notebooks/03_final_store_level_forecasting.ipynb`

## Final Project Flow

1. `notebooks/01_data_understanding_eda.ipynb`
2. `notebooks/02_data_cleaning_feature_engineering.ipynb`
3. `notebooks/03_final_store_level_forecasting.ipynb`

## Final Scope

Forecast **daily visitors for each `air_store_id`**.

This project uses **store-level forecasting**, not one aggregate restaurant time series.

The final reported run is the `main250cv` version:

- full AIR stores in raw data: `814`
- eligible stores after modeling requirements: `812`
- fixed main evaluation sample: `250` stores

Sample interpretation:

- the `250` stores are selected by a deterministic pre-model rule, not by forecast performance
- selection is based on observed span and available training history, so the final sample is a high-information subset of the eligible panel
- the sample covers about `30.8%` of eligible stores and about `30.9%` of eligible holdout rows

## Final Model Comparison

- Baseline: `Seasonal Naive (lag = 7)`
- Benchmark model: `SARIMA(1,1,1)(1,1,1)[7]` with no exogenous regressors
- Final model: `SARIMAX(1,1,1)(1,1,1)[7]` with `is_holiday` only
- Holdout: `2017-03-15 -> 2017-04-22`
- Evaluation unit: pooled observed `store-date` rows

Fixed-order tradeoff:

- one common order keeps the model ladder comparable across stores
- this improves runtime, stability, and interpretability
- the cost is that the specification is not individually tuned for every store, so the final claim is about a coherent common benchmark, not per-store optimality

## Final Main Holdout Metrics

- Stores evaluated: `250`
- Stores skipped: `0`
- Model fallbacks: `0`
- Holdout rows: `9,721`
- Seasonal Naive RMSLE: `0.9314`
- SARIMA no exog RMSLE: `0.7255`
- SARIMAX holiday-only RMSLE: `0.7098`
- SARIMA improvement vs Naive: `22.1%`
- SARIMAX improvement vs Naive: `23.8%`
- SARIMAX improvement vs SARIMA: `2.17%`
- SARIMA better than Naive on `87.2%` of stores
- SARIMAX better than Naive on `89.2%` of stores
- SARIMAX better than SARIMA on `67.2%` of stores

## Rolling-Origin Validation

Three rolling-origin folds are included on the same `250`-store main sample:

- `CV1`: train to `2017-01-14`, test `2017-01-15 -> 2017-01-28`
- `CV2`: train to `2017-01-31`, test `2017-02-01 -> 2017-02-14`
- `CV3`: train to `2017-02-14`, test `2017-02-15 -> 2017-02-28`

Pooled RMSLE:

- `CV1`: Naive `1.0777`, SARIMA `0.7345`, SARIMAX `0.7162`
- `CV2`: Naive `0.8679`, SARIMA `0.7020`, SARIMAX `0.7115`
- `CV3`: Naive `0.9050`, SARIMA `0.6802`, SARIMAX `0.6784`

Interpretation:

- `SARIMA` clearly improves over the weekly baseline.
- `SARIMAX holiday only` is best on the final holdout and on `2/3` validation folds.
- The incremental gain from `is_holiday` is positive but modest, which matches the final report narrative.

## 400-Store Holdout Robustness Check

To test whether the main conclusion depends too heavily on the `250`-store evaluation subset, the final holdout comparison was re-run on a larger deterministic `400`-store subset using the same model ladder and holdout window.

- Stores evaluated: `400`
- Holdout rows: `15,555`
- Seasonal Naive RMSLE: `0.9209`
- SARIMA no exog RMSLE: `0.7178`
- SARIMAX holiday-only RMSLE: `0.7145`
- SARIMA improvement vs Naive: `22.06%`
- SARIMAX improvement vs Naive: `22.41%`
- SARIMAX improvement vs SARIMA: `0.45%`
- SARIMA better than Naive on `88.0%` of stores
- SARIMAX better than Naive on `89.25%` of stores
- SARIMAX better than SARIMA on `65.25%` of stores

Interpretation:

- the qualitative ranking remains stable on the larger deterministic subset
- the strong gain from modelling seasonal stochastic dependence remains intact
- the holiday-only gain stays positive, but it narrows materially relative to the `250`-store main sample
- this strengthens the conclusion that the central forecasting improvement comes from the SARIMA seasonal core, while the holiday regressor adds a smaller and more sample-sensitive incremental gain

Residual diagnostics interpretation:

- the raw aggregate diagnostics show strong weekly dependence, so a seasonal model is empirically motivated
- the representative residual ACF after SARIMA is materially flatter than the raw-series correlogram, which supports the claim that the benchmark removes most of the dominant weekly autocorrelation
- the fact that `SARIMA` beats `Seasonal Naive` on the final holdout and all three folds strengthens the residual argument out of sample, not just in sample

Representative store-level residual diagnostics:

- a deterministic set of `6` representative stores was tested with residual ACF diagnostics and Ljung-Box p-values at lags `7`, `14`, and `21`
- `5/6` representative stores retain Ljung-Box p-values above `0.05` at those seasonal lags
- `1/6` representative store still shows statistically noticeable seasonal residual dependence
- the correct interpretation is therefore strong but not absolute: the fixed-order SARIMA benchmark removes most dominant weekly dependence for representative stores, but it does not fully whiten every residual sequence

Threats to validity:

- sample scope: the main claim is strongest for the deterministic high-information subset used in `main250cv`
- residual scope: aggregate and representative diagnostics support the model design, but they are not a full store-by-store whiteness battery by default
- panel construction: filling internal missing dates with `0 visitors` is a modelling convention used to preserve a regular daily index

## Optional Extensions Included In The Notebook

The final notebook now also includes optional code blocks for:

- a deterministic `400`-store robustness re-run with the same holdout and CV structure
- representative residual diagnostics for selected stores, including residual ACF plots and Ljung-Box p-values at seasonal lags

These extensions are disabled by default so that the final submitted result set remains the clean `main250cv` run unless the user explicitly enables them.

Representative residual diagnostics note:

- the notebook includes a deterministic representative-store diagnostic block that fits the same fixed-order SARIMA benchmark and records residual ACF behaviour and Ljung-Box p-values at lags `7`, `14`, and `21`
- this is designed to strengthen the residual argument at the store level without changing the main submitted result set

## Final Output Files

- `output/store_level_summary_main250cv.json`
- `output/store_level_metrics_main250cv.csv`
- `output/store_level_holdout_predictions_main250cv.csv`
- `output/store_level_cv_summary_main250cv.csv`
- `output/store_level_ablation_metrics_main250cv.csv`

## Note For Grading

Use the numbered notebooks, `latex/main.tex`, and the `*_main250cv.*` files in `output/` as the final result set.
