# WealthBuilder – Development Roadmap

## 1. Purpose

This roadmap defines the development milestones for the WealthBuilder trading research platform.

The goal is to incrementally build a **reliable futures signal scanner**, starting with free data sources and evolving into a modular quantitative trading platform.

Each phase introduces specific capabilities while keeping the system stable and testable.

---

# Phase 1 – Foundation Setup

### Objective

Prepare the repository structure and basic development environment.

### Tasks

* Create project folder structure
* Setup Git repository
* Add architecture documentation
* Define configuration structure
* Add dependency management

### Deliverables

Repository structure ready:

```
wealthbuilder
data/
strategies/
scanner/
alerts/
scheduler/
config/
docs/
```

Files created:

```
README.md
architecture.md
roadmap.md
requirements.txt
```

Status: **Completed**

---

# Phase 2 – Market Data Integration

### Objective

Enable the system to fetch market data from Yahoo Finance.

### Tasks

Implement:

```
data/yahoo_fetcher.py
```

Responsibilities:

* Fetch OHLCV candles
* Support configurable timeframe (15m)
* Convert data to pandas dataframe
* Handle API errors

### Example Output

```
timestamp
open
high
low
close
volume
```

### Deliverables

Scanner can successfully retrieve market data for a symbol.

Example:

```
fetch_data("RELIANCE")
```

Returns latest candles.

---

# Phase 3 – Universe Manager

### Objective

Define the trading universe (F&O stocks).

### Tasks

Implement:

```
universe/fno_symbols.py
```

Initial universe:

```
RELIANCE
ICICIBANK
HDFCBANK
INFY
TCS
SBIN
TATAMOTORS
AXISBANK
ITC
LT
```

Limit to **10–20 symbols initially** for stability.

Future improvement:

Automatically fetch F&O list from NSE.

### Deliverables

Scanner loads universe:

```
symbols = get_symbols()
```

---

# Phase 4 – Indicator Engine

### Objective

Calculate indicators required by strategies.

### Tasks

Create module:

```
indicators/indicators.py
```

Implement indicators:

* EMA
* Rolling high
* Rolling low
* Average volume
* VWAP (optional)

### Deliverables

Indicator functions return enhanced dataframe with indicator columns.

---

# Phase 5 – Strategy Engine

### Objective

Implement modular strategy framework.

### Tasks

Create strategy modules:

```
strategies/breakout.py
strategies/volume_spike.py
strategies/momentum.py
```

Each strategy must implement:

```
evaluate(data)
```

Return signal format:

```
{
  symbol
  strategy
  price
  confidence
}
```

### Deliverables

Strategies generate signals based on market data.

---

# Phase 6 – Scanner Engine

### Objective

Build the main orchestration engine.

### Tasks

Implement:

```
scanner/engine.py
```

Responsibilities:

* Load symbol universe
* Fetch data for each symbol
* Run indicator engine
* Execute strategies
* Collect signals

Example loop:

```
for symbol in universe:
    data = fetch_data(symbol)
    indicators = compute_indicators(data)

    for strategy in strategies:
        signal = strategy.evaluate(data)

        if signal:
            signals.append(signal)
```

### Deliverables

Scanner outputs signals to console.

---

# Phase 7 – Scheduler

### Objective

Automate scanner execution.

### Tasks

Implement:

```
scheduler/run_scanner.py
```

Use scheduling library to run scanner every:

```
15 minutes
```

### Deliverables

Scanner runs automatically during market hours.

---

# Phase 8 – Telegram Alerts

### Objective

Deliver signals to users via Telegram.

### Tasks

Create:

```
alerts/telegram_alert.py
```

Integrate Telegram bot API.

Alert format:

```
🚨 Futures Signal

Symbol: ICICIBANK
Strategy: Breakout
Price: 1045
Volume: 2x

Time: 11:15
```

### Deliverables

Signals are delivered to Telegram channel.

---

# Phase 9 – Signal Storage

### Objective

Track signals for later analysis.

### Tasks

Implement:

```
storage/signal_store.py
```

Store:

```
timestamp
symbol
strategy
price
score
```

Initial storage:

```
CSV
```
