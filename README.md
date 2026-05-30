# Store-Level Time Series Forecasting of Restaurant Visitors Using SARIMAX

This repository contains the final submission for a time series forecasting project on **daily restaurant visitors**.

Dataset source: [Recruit Restaurant Visitor Forecasting (Kaggle input page)](https://www.kaggle.com/code/headsortails/be-my-guest-recruit-restaurant-eda/input)

## Read This First

If you are grading this project, read the files in this order:

1. `README.md`
2. `report/store_level_results.md`
3. `latex/main.tex`
4. `notebooks/03_final_store_level_forecasting.ipynb`
5. `notebooks/02_data_cleaning_feature_engineering.ipynb`
6. `notebooks/01_data_understanding_eda.ipynb`

This keeps the project easy to follow:

- `01`: understand the data
- `02`: clean data and build features
- `03`: run the final store-level forecasting comparison

## Final Submission Files

The main files for grading are:

- `report/store_level_results.md`
- `latex/main.tex`
- `notebooks/03_final_store_level_forecasting.ipynb`
- `output/store_level_summary_main250cv.json`
- `output/store_level_metrics_main250cv.csv`
- `output/store_level_holdout_predictions_main250cv.csv`
- `output/store_level_cv_summary_main250cv.csv`
- `output/store_level_ablation_metrics_main250cv.csv`

## How to Run

1. Create and activate a Python environment.
2. Install dependencies:
   `pip install -r requirements.txt`
3. Open Jupyter Notebook:
   `jupyter notebook`
4. Read the project in this order:
   `notebooks/01_data_understanding_eda.ipynb` -> `notebooks/02_data_cleaning_feature_engineering.ipynb` -> `notebooks/03_final_store_level_forecasting.ipynb`
5. For the final result only, open:
   `notebooks/03_final_store_level_forecasting.ipynb`

The final notebook reads input data from:

- `notebooks/processed_data/`

and writes the final outputs to:

- `output/store_level_summary_main250cv.json`
- `output/store_level_metrics_main250cv.csv`
- `output/store_level_holdout_predictions_main250cv.csv`
- `output/store_level_cv_summary_main250cv.csv`
- `output/store_level_ablation_metrics_main250cv.csv`

## Project Goal

Forecast **daily visitors for each `air_store_id`**.

This is a **store-level forecasting** project, not one aggregate time series for all restaurants combined.

## Final Modeling Setup

- Baseline: `Seasonal Naive (lag = 7)`
- Benchmark model: `SARIMA(1,1,1)(1,1,1)[7]` with no exogenous regressors
- Final model: `SARIMAX(1,1,1)(1,1,1)[7]` with `is_holiday` only
- Run mode: `main250cv`
- Main evaluation sample: `250` eligible stores
- Holdout period: `2017-03-15 -> 2017-04-22`
- Evaluation unit: pooled observed `store-date` rows

## Final Results

- Stores total: `814`
- Stores eligible: `812`
- Stores targeted: `250`
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
- SARIMAX better than Naive on `89.2%` of stores
- SARIMAX better than SARIMA on `67.2%` of stores

## Rolling-Origin Validation

The final notebook also includes three rolling-origin validation folds on the same `250`-store sample:

- `CV1`: train to `2017-01-14`, test `2017-01-15 -> 2017-01-28`
- `CV2`: train to `2017-01-31`, test `2017-02-01 -> 2017-02-14`
- `CV3`: train to `2017-02-14`, test `2017-02-15 -> 2017-02-28`

Pooled RMSLE by fold:

- `CV1`: Naive `1.0777`, SARIMA `0.7345`, SARIMAX `0.7162`
- `CV2`: Naive `0.8679`, SARIMA `0.7020`, SARIMAX `0.7115`
- `CV3`: Naive `0.9050`, SARIMA `0.6802`, SARIMAX `0.6784`

## 400-Store Robustness Check

The notebook has also been used to run a larger deterministic `400`-store holdout-only robustness check using the same model ladder and final holdout window.

- Stores evaluated: `400`
- Holdout rows: `15,555`
- Seasonal Naive RMSLE: `0.9209`
- SARIMA no exog RMSLE: `0.7178`
- Model fallbacks: `1`
- SARIMAX holiday-only RMSLE: `0.7034`
- SARIMA improvement vs Naive: `22.06%`
- SARIMAX improvement vs Naive: `23.62%`
- SARIMAX improvement vs SARIMA: `2.00%`
- SARIMA better than Naive on `88.0%` of stores
- SARIMAX better than Naive on `89.25%` of stores
- SARIMAX better than SARIMA on `65.5%` of stores

Interpretation:

- the qualitative ranking remains stable on the larger sample
- the dominant gain remains the move from `Seasonal Naive` to `SARIMA`
- the incremental holiday gain stays positive and remains slightly smaller than in the `250`-store main sample

## Representative Residual Diagnostics

The notebook was also used to run representative store-level residual diagnostics for the fixed-order `SARIMA(1,1,1)(1,1,1)[7]` benchmark on six deterministic stores spanning the eligible panel.

Summary interpretation:

- for `5/6` representative stores, Ljung-Box p-values at lags `7`, `14`, and `21` remain above `0.05`
- for `1/6` representative stores, residual dependence at seasonal lags remains statistically noticeable
- this supports a disciplined interpretation: the common SARIMA benchmark removes most dominant weekly dependence for representative stores, but it does not produce perfectly white residuals for every case

## Optional Extensions In The Notebook

The final notebook also includes two optional extension blocks that are disabled by default:

- a deterministic `400`-store robustness re-run using the same holdout and rolling-origin design
- representative residual diagnostics for selected stores using the fixed-order `SARIMA(1,1,1)(1,1,1)[7]` benchmark, with residual ACF plots and Ljung-Box p-values at seasonal lags

These extensions are included to strengthen the time-series argument when needed, but they are not part of the default `main250cv` final result set unless explicitly run.

## Repository Structure

### Main notebooks

- `notebooks/01_data_understanding_eda.ipynb`: EDA and dataset understanding
- `notebooks/02_data_cleaning_feature_engineering.ipynb`: cleaning and feature engineering
- `notebooks/03_final_store_level_forecasting.ipynb`: final forecasting comparison

### Data folders

- `notebooks/raw_Data/`: original source data
- `notebooks/processed_data/`: cleaned and transformed data used by the final notebook

### Output folder

- `output/`: final saved summary, metrics, validation results, and holdout predictions
- `output/archive/`: archived legacy and non-final experiment outputs

### Report folders

- `report/store_level_results.md`: short grading summary
- `latex/main.tex`: full report source

## Important Method Notes

The final notebook:

- does **not** pad synthetic pre-history for stores
- models each store only on its **observed date span**
- fills missing days inside an observed span as `0 visitors`
- compares `Seasonal Naive`, `SARIMA no exog`, and `SARIMAX holiday only`
- uses one fixed `250`-store main sample for the holdout and all three CV folds
- includes optional code paths for `400`-store robustness and representative residual diagnostics

## Note For Grading

Use the numbered notebooks, `latex/main.tex`, and the `*_main250cv.*` files in `output/` as the final result set.
