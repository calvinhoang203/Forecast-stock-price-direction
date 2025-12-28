# Forecast Stock Price Direction

Predicting whether Amazon stock will go up or down the next day using machine learning.

## What This Project Does

Instead of trying to predict exact stock prices (which is nearly impossible), this project predicts **direction** - will the stock close higher or lower than it opens tomorrow?

**Simple goal:** Predict UP or DOWN
- **UP (1)**: Tomorrow's closing price > tomorrow's opening price
- **DOWN (0)**: Tomorrow's closing price < tomorrow's opening price

**Why does this matter?** If we know the direction, we can make trading decisions:
- Predict UP → Buy at opening, sell at closing
- Predict DOWN → Don't buy (or sell if we own it)

## Results

**Test Set Performance:**
- **AUC Score: 0.5378** ✓ (target was 0.515)
- **Accuracy: 51.55%** (better than random guessing)
- **Best Model: Gradient Boosting**

Yeah, 51.55% doesn't sound impressive, but in stock trading, even a 1-2% edge over random can be profitable. The model correctly identifies market direction slightly better than a coin flip.

## Dataset

Amazon stock data from 1997 to 2020, split into three periods:
- **Training (1997-2016):** 4,760 trading days
- **Validation (2016-2018):** 482 trading days  
- **Test (2018-2020):** 483 trading days

Each day includes:
- Open, High, Low, Close prices
- Adjusted Close (accounts for stock splits, dividends)
- Volume (number of shares traded)

## The Approach

### 1. Created the Target
For each day, I looked at the *next* day and labeled it:
- 1 if next day's close > next day's open (UP day)
- 0 if next day's close < next day's open (DOWN day)

Turns out the data is balanced - about 50% UP days and 50% DOWN days.

### 2. Engineered 39 Features

This is where most of the work went. I created features in 10 categories:

**Basic Price Features:**
- Daily return: (Close - Open) / Open
- Intraday range: (High - Low) / Open  
- Close position: Where did we close in the day's range?

**Gap Features:** (Turned out to be important!)
- Gap: (Today's Open - Yesterday's Close) / Yesterday's Close
- Shows overnight sentiment from after-hours news

**Moving Averages:**
- 5, 10, and 20-day moving averages
- Price distance from each MA
- Trend direction (short MA vs long MA)

**Volatility:**
- 5, 10, 20-day standard deviation of returns
- Bollinger band position
- Measures how jumpy the stock is

**Momentum:**
- Price changes over 3, 5, 10, 20 days
- Acceleration (is momentum increasing?)

**Volume Features:** (These ended up being most predictive!)
- Volume ratio compared to 5 and 20-day averages
- Price-volume interaction
- High volume often signals big moves

**Pattern Recognition:**
- Consecutive up/down days in last 5 days
- Distance from 20-day high/low
- Looking for support/resistance levels

**Technical Indicators:**
- RSI (Relative Strength Index) - overbought/oversold
- MACD (Moving Average Convergence Divergence)
- Professional trader tools

**Lag Features:**
- Yesterday's and previous days' returns
- Previous days' volume ratios
- Past behavior predicts future behavior

**Time Features:**
- Day of week (Monday-Friday)
- Month, day of month
- Beginning/end of month effects

### 3. Tried Three Models

**Logistic Regression:**
- AUC: 0.4794
- Simple linear model, didn't work well

**Random Forest:**
- AUC: 0.5073  
- Better, but still below target

**Gradient Boosting:** ✓ Winner
- Validation AUC: 0.5229
- Test AUC: 0.5378
- Builds trees sequentially, each fixing previous mistakes

### 4. What Actually Matters

Top 3 most important features:
1. **volume_ratio_20** (4.21%) - Trading volume vs 20-day average
2. **gap** (4.21%) - Overnight price change
3. **volume_ratio_lag_3** (4.09%) - Volume ratio from 3 days ago

**Key finding:** Volume patterns are more predictive than price movements alone. When trading volume spikes compared to recent averages, it signals something's happening.

## What I Learned

**Why stock prediction is hard:**
- Markets are efficient - most info is already priced in
- We're competing against algorithms with way more data
- News, tweets, world events affect prices instantly
- Patterns change over time

**What worked:**
- Feature engineering matters more than the model
- Volume signals are stronger than I expected
- Gap (overnight changes) captures sentiment
- Tree-based models handle complexity better than linear ones

**What could improve this:**
- News sentiment analysis (are articles positive/negative?)
- Social media trends (#AMZN mentions, sentiment)
- Economic indicators (GDP, interest rates, unemployment)
- Competitor stock prices (AAPL, GOOGL, MSFT)
- Options data (puts/calls ratios)
- Deep learning models (LSTM for time series)

## Project Structure

```
forecast-stock-price-direction/
├── datasets/
│   ├── AMZN_train.csv
│   ├── AMZN_val.csv
│   └── AMZN_test.csv
├── main.ipynb
└── README.md
```

## How to Run

1. Install requirements:
```bash
pip install pandas numpy scikit-learn matplotlib seaborn
```

2. Open the Jupyter notebook:
```bash
jupyter notebook main.ipynb
```

3. Run all cells in order

The notebook walks through each step with explanations.

## Key Takeaways

- Got a 53.78% AUC score (beat the 51.5% target)
- Even small edges matter in trading
- Volume patterns beat price patterns
- Feature engineering > fancy models
- Stock prediction is genuinely difficult

## Disclaimer

This is an educational project. Not financial advice. Don't actually trade based on this model. Stock markets are unpredictable, and past performance doesn't guarantee future results.

Also, in real trading you'd need to account for:
- Transaction costs (commissions, bid-ask spread)
- Slippage (prices move while you buy/sell)
- Market impact (your trades affect prices)
- Risk management (don't risk more than you can lose)

## What's Next

Things I'd try if I continued this:
- Test on other stocks (does it generalize?)
- Add macroeconomic data
- Build a trading simulator with realistic costs
- Try LSTM neural networks
- Implement portfolio strategies (not just one stock)
- Add risk management (position sizing, stop losses)

---

Built this to learn about time series prediction, feature engineering, and how hard stock forecasting actually is. Spoiler: it's really hard.