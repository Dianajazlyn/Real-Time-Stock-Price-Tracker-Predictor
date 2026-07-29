# Real-Time Stock Price Tracker & Predictor

A Python project that fetches **live stock market data**, displays a real-time updating chart, and predicts the next likely price using a simple machine learning model — built to run entirely in **Google Colab**.

## Features

- Fetches live/near-real-time stock prices using the `yfinance` library
- Displays an auto-refreshing line chart of price movement
- Predicts the next price point using **Linear Regression**
- Runs with zero setup — no API keys, no local installation, works directly in Colab

## Tech Stack

- **Python 3**
- [yfinance](https://pypi.org/project/yfinance/) – fetches stock market data from Yahoo Finance
- **pandas** – data handling
- **matplotlib** – live chart plotting
- **scikit-learn** – Linear Regression model for price prediction
- **Google Colab** – cloud notebook environment (no local setup required)

## How It Works

1. **Data Fetching** – `yfinance` pulls minute-level stock data (open, high, low, close, volume) for a chosen ticker (e.g., `AAPL`).
2. **Live Visualization** – A loop refreshes the chart every 30 seconds, plotting the closing price trend over time.
3. **Prediction** – A Linear Regression model is trained on the recent price trend and used to estimate the next price point.

## Project Structure

```
stock-tracker/
│
├── README.md
└── stock_tracker.ipynb   # Main Colab notebook
```

## Getting Started

### 1. Open in Google Colab
Upload the notebook to [Google Colab](https://colab.research.google.com/) or open it directly from GitHub.

### 2. Install dependencies
Run this in the first cell:
```python
!pip install yfinance pandas matplotlib scikit-learn -q
```

### 3. Run the live tracker
```python
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt
import time
from IPython.display import clear_output

def get_live_data(ticker="AAPL", period="1d", interval="1m"):
    data = yf.download(ticker, period=period, interval=interval, progress=False)
    return data

ticker = "AAPL"

for i in range(10):
    clear_output(wait=True)
    df = get_live_data(ticker)

    plt.figure(figsize=(10,5))
    plt.plot(df.index, df['Close'], label=f"{ticker} Price", color='blue')
    plt.title(f"Live Stock Price - {ticker}")
    plt.xlabel("Time")
    plt.ylabel("Price (USD)")
    plt.legend()
    plt.grid(True)
    plt.show()

    print(f"Last Price: {float(df['Close'].iloc[-1]):.2f}")
    time.sleep(30)
```

### 4. Run the prediction model
In a **separate cell**, after the loop above finishes:
```python
from sklearn.linear_model import LinearRegression
import numpy as np

df = get_live_data(ticker)
df = df.reset_index()
df['time_index'] = np.arange(len(df))

X = df[['time_index']]
y = df['Close']

model = LinearRegression()
model.fit(X, y)

next_index = np.array([[len(df)]])
predicted_price = model.predict(next_index)

print(f"Predicted next price for {ticker}: {float(predicted_price[0]):.2f}")
```

## Sample Output

```
Last Price: 341.15
Predicted next price for AAPL: 341.42
```

## Possible Extensions

- Add real-time sentiment analysis from news/Twitter for the ticker
- Send alerts (Telegram/Email) when price crosses a threshold
- Replace Linear Regression with an LSTM model for better forecasting
- Support tracking multiple tickers simultaneously

## Notes

- Change the `ticker` variable to track any other stock (e.g., `"TSLA"`, `"GOOGL"`, `"MSFT"`)
- Data is fetched from Yahoo Finance and may be delayed by a few minutes depending on market and plan restrictions
- This project is for educational/demo purposes and **not financial advice**

## License

Free to use and modify for personal or academic projects.
