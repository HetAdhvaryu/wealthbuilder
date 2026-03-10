# WealthBuilder – Strategy Framework

## 1. Purpose

The strategy framework defines how trading strategies are implemented and integrated into the scanner engine.

The goal is to ensure:

* Strategies are modular
* Strategies are easy to add/remove
* Strategies follow a consistent interface
* The scanner engine remains simple

Each strategy must implement a **standard evaluation interface**.

---

# 2. Strategy Folder Structure

All strategies are stored in:

```text
strategies/
```

Example:

```text
strategies/
    breakout.py
    volume_spike.py
    momentum.py
```

Each file represents **one strategy**.

---

# 3. Standard Strategy Interface

Every strategy must implement the function:

```python
evaluate(symbol, dataframe)
```

### Parameters

| Parameter | Description                           |
| --------- | ------------------------------------- |
| symbol    | trading symbol                        |
| dataframe | market data dataframe with indicators |

Example dataframe columns:

```text
timestamp
open
high
low
close
volume
ema20
ema50
volume_avg
rolling_high
```

---

# 4. Strategy Output Format

Each strategy returns either:

```
None
```

or a signal dictionary.

Signal format:

```python
{
    "symbol": "ICICIBANK",
    "strategy": "breakout",
    "price": 1045,
    "score": 85,
    "timestamp": "2026-03-11 11:15"
}
```

Fields explanation:

| Field     | Meaning         |
| --------- | --------------- |
| symbol    | stock symbol    |
| strategy  | strategy name   |
| price     | current price   |
| score     | signal strength |
| timestamp | signal time     |

---

# 5. Example Strategy Implementation

Example breakout strategy:

```python
def evaluate(symbol, df):

    last_row = df.iloc[-1]

    breakout_level = df["high"].rolling(20).max().iloc[-2]

    if last_row["close"] > breakout_level:

        return {
            "symbol": symbol,
            "strategy": "breakout",
            "price": last_row["close"],
            "score": 80,
            "timestamp": str(last_row["timestamp"])
        }

    return None
```

---

# 6. Strategy Execution Flow

The scanner engine loads all strategies and executes them sequentially.

Execution logic:

```python
signals = []

for strategy in strategies:

    signal = strategy.evaluate(symbol, dataframe)

    if signal:
        signals.append(signal)
```

---

# 7. Strategy Naming Convention

Strategy filenames should follow lowercase naming.

Examples:

```
breakout.py
volume_spike.py
momentum.py
vwap_reclaim.py
```

Strategy name returned in signal should match filename.

Example:

```
strategy = "breakout"
```

---

# 8. Strategy Independence

Strategies must:

* Not fetch market data
* Not send alerts
* Not modify global state

Strategies should **only evaluate data and return signals**.

This keeps them reusable and testable.

---

# 9. Strategy Testing

Each strategy should be testable independently.

Example test:

```python
from strategies.breakout import evaluate

signal = evaluate("RELIANCE", dataframe)
```

Expected output:

```
signal dictionary or None
```

---

# 10. Future Strategy Types

Possible strategies for future development:

Breakout strategies

```
20-candle breakout
opening range breakout
trend continuation
```

Volume strategies

```
volume spike
volume breakout
```

Momentum strategies

```
EMA crossover
price momentum
VWAP reclaim
```

Futures strategies (later)

```
long buildup
short covering
OI spike
```

---

# 11. Strategy Configuration

Enabled strategies are defined in:

```
config/settings.json
```

Example:

```json
{
  "enabled_strategies": [
    "breakout",
    "volume_spike",
    "momentum"
  ]
}
```

The scanner engine will load only enabled strategies.

---

# 12. Design Principles

The strategy framework follows these principles:

* Strategy modularity
* Strategy independence
* Consistent input/output format
* Easy extensibility

This design allows WealthBuilder to support **dozens of strategies without modifying the scanner engine**.
