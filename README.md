# CMO Alert — MQL4 Script

A MetaTrader 4 script that computes the **Chande Momentum Oscillator (CMO)** from raw close-to-close price changes and fires level-crossing alerts when the oscillator crosses above `+UpperLevel` or below `−LowerLevel`, using a persistent previous-value state variable to enforce strict directional crossing logic and eliminate redundant same-bar alerts.

---

## Overview

The Chande Momentum Oscillator is a bounded momentum indicator scaled between −100 and +100 that measures the ratio of upward price change momentum to total price movement over a rolling period. Unlike RSI, which uses smoothed average gains and losses, CMO uses raw sum totals — making it more sensitive to short-term momentum shifts. This script implements CMO natively in MQL4, tracks its value across loop iterations using `PrevCMO`, and fires crossing alerts only when the oscillator transitions through the configured threshold levels from the appropriate side.

---

## Features

- **Native CMO calculation** — raw `sumGains` and `sumLosses` accumulated via close-to-close price delta iteration over `CMOPeriod` bars, with division-by-zero protection
- **Strict crossing logic** — `PrevCMO < UpperLevel && currentCMO >= UpperLevel` prevents false repeat alerts on sustained overbought/oversold readings
- **Bidirectional level detection** — independent upper (`+50` default) and lower (`−50` default) crossing thresholds, both fully configurable
- **Persistent state tracking** — `PrevCMO` global variable retains the prior cycle value across loop iterations
- **Three notification channels:** sound alert, email, and mobile push
- **Lightweight loop** — polls once per minute (`Sleep(60000)`)
- Logs CMO value and crossing direction to the MT4 **Experts** tab on every alert

---

## How It Works

1. Every minute, `CalculateCMO()` iterates over `CMOPeriod` close-to-close changes using `iClose(..., i-1) − iClose(..., i)`, accumulating positive changes into `sumGains` and the absolute value of negative changes into `sumLosses`
2. CMO is computed as: `CMO = 100 × (sumGains − sumLosses) / (sumGains + sumLosses)` — returns `0.0` if denominator is zero
3. Two crossing conditions are evaluated using stored `PrevCMO`:
   - `PrevCMO < UpperLevel && currentCMO >= UpperLevel` → **CMO Crossed Above Upper Level**
   - `PrevCMO > LowerLevel && currentCMO <= LowerLevel` → **CMO Crossed Below Lower Level**
4. `PrevCMO` is updated at the end of every cycle

---

## Input Parameters

| Parameter      | Type            | Default     | Description                                               |
|----------------|-----------------|-------------|-----------------------------------------------------------|
| `TradeSymbol`  | string          | `EURUSD`    | Symbol for analysis                                       |
| `Timeframe`    | ENUM_TIMEFRAMES | `PERIOD_H1` | Timeframe for analysis                                    |
| `CMOPeriod`    | int             | `14`        | Lookback period for CMO calculation                       |
| `UpperLevel`   | double          | `50.0`      | CMO level above which an overbought crossing is triggered |
| `LowerLevel`   | double          | `-50.0`     | CMO level below which an oversold crossing is triggered   |
| `EnableAlerts` | bool            | `true`      | Fire an on-screen/sound alert                             |
| `EnableEmail`  | bool            | `false`     | Send an email notification                                |
| `EnablePush`   | bool            | `false`     | Send a mobile push notification                           |

---

## Alert Message Format

```
CMO Crossed Above Upper Level detected on EURUSD (Timeframe: PERIOD_H1)
CMO Value: 52.34
```

---

## Installation

1. Copy `Chande_Momentum_Oscillator__CMO_001.mq4` to `MQL4/Scripts/` in your MT4 data folder
2. Compile in MetaEditor (F7)
3. Drag onto any chart from Navigator → Scripts
4. Configure inputs and click **OK**

---

## Requirements

- MetaTrader 4 (`#property strict` compatible build)
- MQL4 compiler (MetaEditor)

---

## License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
