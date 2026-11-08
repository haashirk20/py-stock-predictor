<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/readme/hero-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="assets/readme/hero-light.svg">
    <img src="assets/readme/hero-dark.svg" width="880" alt="Stock Predictor: a Streamlit app that forecasts a stock's closing price 1 to 4 years ahead with Prophet, using daily prices since 2015. Illustrated forecast chart.">
  </picture>
</p>

<p align="center">
  <strong>Type a ticker, pick 1 to 4 years, and get a Prophet forecast of the closing price.</strong><br>
  <sub>Streamlit, yfinance, Prophet and Plotly. A proof of concept: don't use it to make financial decisions.</sub>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-0E1117?style=flat-square&logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/Streamlit-0E1117?style=flat-square&logo=streamlit&logoColor=FF4B4B" alt="Streamlit">
  <img src="https://img.shields.io/badge/Prophet-time%20series-0E1117?style=flat-square" alt="Prophet">
  <img src="https://img.shields.io/badge/Plotly-0E1117?style=flat-square&logo=plotly&logoColor=white" alt="Plotly">
</p>

---

Type a ticker symbol and choose a forecast length of 1 to 4 years. The app pulls daily prices since January 2015 from Yahoo Finance, shows the latest rows and an interactive chart of open and close prices, then fits a Prophet model to the closing prices. It plots the forecast with its uncertainty band, plus Prophet's trend and seasonality components.

## Demo

![Product demonstration](https://github.com/haashirk20/py-stock-predictor/assets/114118228/6f4b082a-0ebf-4835-b126-7f9bdc333669)

## How it works

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/readme/how-it-works-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="assets/readme/how-it-works-light.svg">
    <img src="assets/readme/how-it-works-dark.svg" width="880" alt="How it works: ticker and years, load_data with yfinance since 2015, rename Date and Close to ds and y, fit Prophet, forecast years times 365 days.">
  </picture>
</p>

`load_data` downloads the history with `yf.download` and is wrapped in `st.cache_data`, so moving the slider doesn't download the data again. The app renames `Date` and `Close` to Prophet's expected `ds` and `y` and fits the default model, which handles trend plus weekly and yearly seasonality with no feature engineering. The horizon becomes `years × 365` days and goes to `make_future_dataframe`, so the one slider sets the whole forecast window.

Other details:

- Any ticker Yahoo Finance knows works, typed into a text box.
- The historical chart is an interactive Plotly figure with a range slider.
- The components plot (Matplotlib, from Prophet) shows trend, weekly and yearly seasonality separately.
- An empty input, a ticker with no data, and a failed fit each show a message instead of a stack trace.

| Area | Tools |
|---|---|
| App | Streamlit |
| Data | yfinance, pandas |
| Forecasting | Prophet |
| Charts | Plotly, Matplotlib (Prophet components) |

## Known issue

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/readme/known-issue-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="assets/readme/known-issue-light.svg">
    <img src="assets/readme/known-issue-dark.svg" width="880" alt="Known issue: current yfinance returns two-level columns and Prophet's fit raises a TypeError; passing multi_level_index=False to yf.download fixes it.">
  </picture>
</p>

Current versions of `yfinance` return a two-level column index such as `('Close', 'AAPL')`, and `m.fit` then fails with `TypeError: arg must be a list, tuple, 1-d array, or Series`. Older `yfinance` versions no longer download any data from Yahoo, so pinning an old version doesn't help. The fix is one argument in `load_data`:

```python
data = yf.download(ticker, START, TODAY, multi_level_index=False)
```

With that change the app fits and forecasts normally on yfinance 1.8.0 and Prophet 1.5.0.

## Getting started

```sh
pip install streamlit yfinance prophet plotly
streamlit run main.py
```

Then open the local URL Streamlit prints, usually http://localhost:8501.
