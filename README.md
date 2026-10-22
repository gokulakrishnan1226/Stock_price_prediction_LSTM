# Stock Closing Price Prediction Using LSTM

A Google Colab-ready time-series forecasting project that uses a Long Short-Term Memory (LSTM) neural network to predict a stock's next closing price from historical daily prices in a Kaggle CSV dataset.

> **Disclaimer:** This repository is for educational purposes only. Stock prices are uncertain, model outputs are not financial advice, and no accuracy threshold is guaranteed.

## Project highlights

- Loads and cleans daily stock data from a user-provided Kaggle CSV.
- Selects one stock and the latest 2,000 consecutive valid trading-day observations.
- Supports common `Date`, `Close`, `Adj Close`, `Ticker`, `Symbol`, and `Name` column conventions.
- Fits `MinMaxScaler` on training observations only to reduce data leakage.
- Converts the series into 60-day input windows and next-day targets.
- Uses a chronological train/validation/test split.
- Trains an LSTM with dropout, L2 regularization, Early Stopping, and learning-rate reduction.
- Evaluates using MAE, RMSE, R², and tolerance-based accuracy within 1% and 5%.
- Compares the LSTM with a persistence baseline (`tomorrow's close = today's close`).
- Plots test predictions and produces a short recursive forecast.

## Repository structure

```text
stock-price-prediction-lstm/
├── notebooks/
│   └── stock_price_prediction_lstm.ipynb
├── data/
│   └── README.md
├── .gitignore
├── LICENSE
├── README.md
└── requirements.txt
```

## Quick start (Google Colab)

1. Open [Google Colab](https://colab.research.google.com/).
2. Choose **File → Upload notebook** and upload `notebooks/stock_price_prediction_lstm.ipynb` from this repository, or open the notebook after uploading the repository to GitHub.
3. Download a historical daily stock-price CSV from Kaggle. Make sure the selected stock has at least 2,000 valid daily records. Some five-year datasets may not have enough rows for every ticker.
4. Run the notebook from the top. When prompted, upload your CSV.
5. In the configuration cell, set `STOCK_SYMBOL` to the exact symbol in your file (for example, `AAPL`) if the CSV contains multiple stocks. Set `STOCK_SYMBOL = None` if your CSV already contains one stock.
6. Inspect the displayed columns and data range. The notebook will raise an informative error if it cannot identify the date/close columns or if fewer than 2,000 valid observations are available.
7. Run all cells and record the actual evaluation results in your report.

### Dataset requirements

The notebook expects one daily row per trading date and a date column plus a closing-price column. It recognizes `Date`, `DateTime`, or `Timestamp`, and `Close` or `Adj Close`. When available and enabled, `Adj Close` is preferred because it accounts for adjustments such as splits and dividends. For merged files, a stock identifier column such as `Ticker`, `Symbol`, or `Name` can be used to filter a stock.

Download a dataset from Kaggle and comply with the dataset's license and attribution terms. See [`data/README.md`](data/README.md). The raw data is intentionally not included in this repository.

## Configuration

The main settings are near the beginning of the notebook:

| Setting | Default | Purpose |
|---|---:|---|
| `STOCK_SYMBOL` | `AAPL` | Stock identifier to filter when the file contains multiple stocks |
| `RECORD_LIMIT` | `2000` | Number of latest valid trading-day observations |
| `LOOKBACK` | `60` | Number of previous days used for one prediction |
| Training split | `70%` | Earliest data used to fit model weights |
| Validation split | `15%` | Chronological data used to monitor training |
| Test split | `15%` | Final held-out period used for evaluation |
| `FORECAST_DAYS` | `5` | Number of approximate future business days to forecast |

## Metrics and interpretation

- **MAE:** Mean absolute error, in the original price units. Smaller is better.
- **RMSE:** Root mean squared error, in the original price units; it penalizes larger errors more strongly. Smaller is better.
- **R²:** Supplementary measure of fit; it can be negative when the model performs poorly relative to the test-set mean.
- **Accuracy within 5%:** Percentage of test predictions whose absolute percentage error is no more than 5%. This is a custom tolerance-based measure, **not classification accuracy**. It depends on the selected threshold and is not comparable when thresholds differ.
- **Persistence baseline:** Predicts each test day's close using the previous observed day's close. The LSTM should be compared against this baseline before claiming it adds value.

The notebook uses the validation set to monitor training and reserves the test set for final evaluation. Do not tune model settings repeatedly against the test results. A requested target such as 80% tolerance-based accuracy is not guaranteed; report measured results honestly.

## Environment

- Python 3.10+
- TensorFlow / Keras
- NumPy and Pandas
- SciPy
- scikit-learn
- Matplotlib

For a local environment, install dependencies with:

```bash
python -m pip install -r requirements.txt
```

In Colab, the standard runtime typically includes most dependencies. If needed, uncomment the installation line near the top of the notebook.

## Outputs

The notebook displays:

- Historical price chart
- Training and validation loss plot
- Training and validation tolerance-based accuracy plot
- Test-set metric comparison (LSTM vs. persistence baseline)
- Actual vs. predicted prices plot
- Test error distribution
- Approximate five-day recursive forecast

It can also write `model_evaluation_results.csv` and `future_forecast.csv` in the current notebook runtime.

## Limitations

- Historical price patterns may not persist; financial time series are noisy and affected by external events.
- The default notebook uses closing price as its only model feature.
- The future forecast is recursive, so errors may accumulate across steps.
- Generated business-day dates skip weekends but do not remove exchange-specific holidays.
- Adjusted closing prices are historical, adjusted values; they are not necessarily the same as the raw close reported on the original day.
- Results depend on the dataset, selected stock, timeframe, random seed, and runtime.

## License

The project code is provided under the MIT License. Dataset rights remain with the dataset's original owner; review the dataset license separately.
