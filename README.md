# Store-Level Time Series Forecasting of Restaurant Visitors Using ARIMAX

This repository contains the final submission for a time series forecasting project on **daily restaurant visitors**.

## Read This First

If you are grading this project, read the files in this order:

1. `README.md`
2. `store_level_results.md`
3. `03_final_store_level_forecasting.ipynb`
4. `02_data_cleaning_feature_engineering.ipynb`
5. `01_data_understanding_eda.ipynb`

This keeps the project easy to follow:

- `01`: understand the data
- `02`: clean data and build features
- `03`: run the final corrected forecasting model

## Final Submission Files

The main files for grading are:

- `store_level_results.md`
- `03_final_store_level_forecasting.ipynb`
- `outputs/store_level_summary_v2.json`
- `outputs/store_level_metrics_v2.csv`
- `outputs/store_level_holdout_predictions_v2.csv`

## How to Run

1. Create and activate a Python environment.
2. Install dependencies:
   `pip install -r requirements.txt`
3. Open Jupyter Notebook:
   `jupyter notebook`
4. Read the project in this order:
   `01_data_understanding_eda.ipynb` -> `02_data_cleaning_feature_engineering.ipynb` -> `03_final_store_level_forecasting.ipynb`
5. For the final result only, open:
   `03_final_store_level_forecasting.ipynb`

The final notebook reads input data from:

- `processed_data/`

and writes final outputs to:

- `outputs/store_level_summary_v2.json`
- `outputs/store_level_metrics_v2.csv`
- `outputs/store_level_holdout_predictions_v2.csv`

## Project Goal

Forecast **daily visitors for each `air_store_id`**.

This is a **store-level forecasting** project, not one aggregate time series for all restaurants combined.

## Final Modeling Setup

- Baseline: `Seasonal Naive (lag = 7)`
- Final model: `ARIMAX(1,1,1)(1,1,1)[7]`
- Exogenous features: `is_holiday`, `is_golden_week`, `is_weekend`, `log1p_reserve`
- Reservation source: `AIR + mapped HPG`
- Holdout period: `2017-03-15 -> 2017-04-22`

## Final Corrected Results

- Stores total: `814`
- Stores evaluated: `812`
- Stores skipped: `2`
- Holdout rows: `31,476`
- Baseline RMSLE: `0.9622`
- ARIMAX RMSLE: `0.8122`
- Improvement vs baseline: `15.6%`
- ARIMAX better than baseline on `79.1%` of evaluated stores

## Lightweight Validation Check

The final notebook also includes one lightweight expanding-window validation fold:

- Train end: `2017-01-31`
- Test window: `2017-02-01 -> 2017-02-07`
- Baseline RMSLE: `0.9327`
- ARIMAX RMSLE: `0.7317`
- Improvement vs baseline: `21.6%`

This is included as a compact temporal validation step. The main reported result is still the final holdout.

## Repository Structure

### Main notebooks

- `01_data_understanding_eda.ipynb`: EDA and dataset understanding
- `02_data_cleaning_feature_engineering.ipynb`: cleaning and feature engineering
- `03_final_store_level_forecasting.ipynb`: final corrected forecasting notebook

### Data folders

- `raw_Data/`: original source data
- `processed_data/`: cleaned and transformed data used by the final notebook

### Output folder

- `outputs/`: final saved summary, metrics, and holdout predictions

## Important Method Notes

The final notebook:

- does **not** pad synthetic pre-history for stores
- models each store only on its **observed date span**
- fills missing days inside an observed span as `0 visitors`
- caps holdout reservation regressors at `2017-03-14` to avoid future leakage
- includes one **lightweight expanding-window validation fold** before the final holdout

## What Was Removed For Clarity

- The separate methodology notebook is not needed in the final structure.
- Old output files from earlier experiments were removed.
- Only the corrected `_v2` outputs are part of the final claim.
