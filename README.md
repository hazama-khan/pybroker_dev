## Algorithmic Trading in Python with Machine Learning

Are you looking to enhance your trading strategies with the power of Python and
machine learning? **PyBroker** is a Python framework
designed for developing algorithmic trading strategies, with a focus on
strategies that use machine learning. With PyBroker, you can easily create and
fine-tune trading rules, build powerful models, and gain valuable insights into
your strategy's performance.

## Key Features

* A super-fast backtesting engine built in NumPy and accelerated with Numba.
* Easy creation of trading rules and models for executing across multiple instruments.
* Integration of trading signals across multiple time intervals, including daily, weekly, and monthly.
* Access to historical data from Alpaca, Yahoo Finance, AKShare, or from your own data provider.
* Model training and backtesting using Walkforward Analysis, which simulates how the strategy would perform during actual trading.
* Reliable trading metrics that use randomized bootstrapping to provide more accurate results.
* Parameter optimization with Optuna to select the best strategy parameters.
* Caching of downloaded data, indicators, and models to speed up your development process.
* Parallelized computation and training for faster performance.
* Agent Skills that help AI agents write trading strategies and backtests using PyBroker.

PyBroker provides you with the tools to build, test, and evaluate algorithmic trading strategies backed by machine learning.

## Installation

PyBroker supports Python 3.11+ on Windows, Mac, and Linux. **We recommend
installing Python 3.12**, the tooling version used to build and test PyBroker,
for the best experience.

You can install PyBroker using ``pip``:

```bash
   pip install -U lib-pybroker
```

Or you can clone the Git repository with:

```bash
   git clone https://github.com/hazama-khan/pybroker_dev
```

After installing PyBroker, you can run the backtest test suite with:

```bash
   python -m pytest -q
```

## A Quick Example

Here's a glimpse of what backtesting with PyBroker looks like with these code
snippets:

**Rule-based Strategy**:

```python
   from pybroker import Strategy, YFinance, highest

   def exec_fn(ctx):
      # Get the rolling 10 day high.
      high_10d = ctx.indicator('high_10d')
      # Buy on a new 10 day high.
      if not ctx.long_pos() and high_10d[-1] > high_10d[-2]:
         ctx.buy_shares = 100
         # Hold the position for 5 days.
         ctx.hold_bars = 5
         # Set a stop loss of 2%.
         ctx.stop_loss_pct = 2

   strategy = Strategy(YFinance(), start_date='1/1/2025', end_date='8/1/2026')
   strategy.add_execution(
      exec_fn, ['AAPL', 'MSFT'], indicators=highest('high_10d', 'close', period=10))
   # Run the backtest after 20 days have passed.
   result = strategy.backtest(warmup=20)
```

**Model-based Strategy**:

```python
   import pybroker
   from pybroker import Alpaca, Strategy

   def train_fn(symbol, train_data, test_data):
      # Train the model using indicators stored in train_data.
      ...
      return trained_model

   # Register the model and its training function with PyBroker.
   my_model = pybroker.model('my_model', train_fn, indicators=[...])

   def exec_fn(ctx):
      preds = ctx.preds('my_model')
      if not ctx.long_pos() and preds[-1] > buy_threshold:
         ctx.buy_shares = 100
      elif ctx.long_pos() and preds[-1] < sell_threshold:
         ctx.sell_all_shares()

   alpaca = Alpaca(api_key=..., api_secret=...)
   strategy = Strategy(alpaca, start_date='1/1/2025', end_date='8/1/2026')
   strategy.add_execution(exec_fn, ['AAPL', 'MSFT'], models=my_model)
   # Run Walkforward Analysis on 1 minute data using 5 windows with 50/50 train/test data.
   result = strategy.walkforward(timeframe='1m', windows=5, train_size=0.5)
```

## Documentation

Full reference documentation is available in the ``docs/`` directory.

