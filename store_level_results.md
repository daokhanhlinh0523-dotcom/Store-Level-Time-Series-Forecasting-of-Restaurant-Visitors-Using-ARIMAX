# Final Results Summary

## Final Notebook

The final notebook for grading is:

- `03_final_store_level_forecasting.ipynb`

## Final Project Flow

1. `01_data_understanding_eda.ipynb`
2. `02_data_cleaning_feature_engineering.ipynb`
3. `03_final_store_level_forecasting.ipynb`

## Problem

Forecast **daily visitors for each `air_store_id`**.

This project uses **store-level forecasting**, not one aggregate restaurant time series.

## Final Model

- Baseline: `Seasonal Naive (lag = 7)`
- Final model: `ARIMAX(1,1,1)(1,1,1)[7]`
- Exogenous features: `is_holiday`, `is_golden_week`, `is_weekend`, `log1p_reserve`
- Reservation source: `AIR + mapped HPG`
- Holdout: `2017-03-15 -> 2017-04-22`
- Evaluation unit: pooled observed `store-date` rows

## Final Corrections Applied

- Removed synthetic pre-history padding
- Built each store only on its observed date span
- Filled missing dates inside an observed span as `0 visitors`
- Capped holdout reservation inputs at `2017-03-14` to avoid future leakage

## Final Metrics

- Stores total: `814`
- Stores evaluated: `812`
- Stores skipped: `2`
- Holdout rows: `31,476`
- Baseline RMSLE: `0.9622`
- ARIMAX RMSLE: `0.8122`
- Improvement vs baseline: `15.6%`
- ARIMAX better than baseline on `79.1%` of evaluated stores

## Lightweight Time-Series Validation

To support the final holdout result, the project also includes one lightweight expanding-window validation fold:

- CV fold: train to `2017-01-31`, test `2017-02-01 -> 2017-02-07`
- Stores evaluated: `810`
- Pooled rows: `5,670`
- Baseline RMSLE: `0.9327`
- ARIMAX RMSLE: `0.7317`
- Improvement vs baseline: `21.6%`
- ARIMAX better than baseline on `69.4%` of evaluated stores

This fold is not the main reported result. It is included as a lightweight temporal validation to show that the model improvement is not based only on the final holdout window.

## Final Output Files

- `outputs/store_level_summary_v2.json`
- `outputs/store_level_metrics_v2.csv`
- `outputs/store_level_holdout_predictions_v2.csv`

## Note For Grading

Use the numbered notebooks and the `_v2` output files as the final result set.
