# European Cross-Commodity Risk Monitor v12

**Gas + Carbon → Power Curve Implications**

An automated daily risk monitoring notebook for European energy markets. It transforms public gas and carbon fundamentals into clear, actionable trading insights for the European power curve — and exports a professional PDF desk note, powered by a local LLM.

---

## Overview

This notebook automates the daily analysis of cross-commodity risk between:
- **TTF Natural Gas** — price, 30-day range, and realized volatility
- **EUA Carbon** — price level and cost pass-through to power
- **German Power** — Day-Ahead and 1Y Forward prices
- **EU Gas Storage** — current level vs. 5-year seasonal average

It produces a **Daily Risk Summary**, a full set of **5 charts**, and a **PDF desk note** with an LLM-generated analyst commentary — all from a single notebook run.

---

## What's New in v12

- **Local LLM integration** via [Ollama](https://ollama.com/) (`llama3.1`) — generates a structured analyst desk note from the daily risk summary
- **Automated PDF export** via `fpdf2` — 3-page report including metrics table, risk score, LLM desk note, and all 5 charts
- **Fully dynamic signals** — no hardcoded interpretations; all labels adapt to live data
- **Timestamped PDF output** — avoids file-lock conflicts on re-run (`EU_Risk_Monitor_YYYYMMDD_HHMM.pdf`)

---

## Key Features

### Automated Data Pipeline
| Data | Source | Method |
|---|---|---|
| TTF price + 30d stats | Yahoo Finance (`TTF=F`) | `yfinance` |
| TTF 6-month history | Yahoo Finance | `yfinance` |
| TTF 30d realized volatility | Calculated from price history | Log returns × √252 |
| EU gas storage (current) | GIE AGSI API | REST API (daily) |
| EU gas storage (5-year history) | GIE AGSI API | REST API (run once/month) |
| EUA carbon price | Manual input | Update `EUA_PRICE` daily |
| German power prices | Manual input | Update `DE_DA` and `DE_1Y` daily |

### Dynamic Risk Scoring
Weighted composite score (0–10) across four components:

| Component | Signal Thresholds | Max Points |
|---|---|---|
| TTF Price | <40 loose / 40–55 moderate / >55 tight | +2 |
| Realized Volatility | <35% low / 35–50% elevated / >50% very high | +3 |
| Storage Deviation | >+5pp comfortable / ±5pp normal / -5 to -10pp tight / <-10pp critical | +3 |
| EUA Carbon | <60 weak / 60–75 constructive / >75 strong | +1 |

Risk levels: `LOW` / `NEUTRAL` / `ELEVATED` / `HIGH` / `CRITICAL`

### Spark Spread
Proxy for gas plant profitability:
```
Spark Spread = DE_DA − (2.0 × TTF) − (0.4 × EUA)
```
Negative → gas plants unprofitable → structurally bullish for forward power.

### Charts (saved as `chart_1.png` – `chart_5.png`)
1. **EU Gas Storage vs 5-Year Average** — current year progress vs seasonal min/max/avg
2. **TTF Price vs EU Storage** — 6-month dual-axis time series
3. **EUA vs German Power Forward** — simulated forward curve overlay
4. **Price & Storage Dashboard** — bar chart snapshot + storage gauge
5. **TTF Price vs 30-Day Realized Volatility** — dual-axis 6-month view

### PDF Report (`EU_Risk_Monitor_YYYYMMDD_HHMM.pdf`)
3-page structured report:
- **Page 1** — Metrics table, risk score box, LLM desk note
- **Page 2** — Charts 1–3
- **Page 3** — Charts 4–5

### LLM Desk Note (llama3.1 via Ollama)
Structured analyst commentary generated from the daily risk summary:
```
MARKET OVERVIEW      — 2–3 sentence macro read
KEY DRIVERS          — 4 bullets (TTF / storage / EUA / spark spread)
RISK ASSESSMENT      — risk score narrative + winter curve implication
TRADING RECOMMENDATION — directional call with conviction
RISKS TO VIEW        — 2 bullets (upside + downside)
```

---

## Quick Start

### 1. Install dependencies
```bash
pip install pandas numpy matplotlib yfinance requests fpdf2
```

### 2. Install and run Ollama
```bash
# Install from https://ollama.com
ollama pull llama3.1
ollama serve
```

### 3. Run the notebook
Open in **Jupyter**, **VS Code**, or **Google Colab** and run all cells in order.

### 4. Update manual inputs daily (Section 3.4 and 3.5)
```python
EUA_PRICE = 72.9    # EUR/tCO₂  — update each morning
DE_DA     = 62.5    # EUR/MWh   — German day-ahead
DE_1Y     = 91.65   # EUR/MWh   — German 1Y forward
```

### 5. Monthly task (Section 3.3a)
Uncomment and run `fetch_historical_storage(years=5)` once per month to refresh the 5-year storage baseline used for seasonal deviation.

---

## Notebook Structure

| Section | Description |
|---|---|
| 1. Setup | Imports and plot style |
| 2. Interpretation Rules | Threshold dictionaries for TTF, EUA, storage, spark spread |
| 3.1a–c | TTF price, 6-month history, realized volatility (automated) |
| 3.2 | TTF 30-day annualized volatility |
| 3.3a | EU storage 5-year historical pull (monthly) |
| 3.3b | EU storage current level (daily) |
| 3.3c | EU storage weekly data — 6 months (for charts) |
| 3.3d | Current year monthly storage (for chart progress line) |
| 3.4 | EUA price (manual) |
| 3.5 | German power prices (manual) |
| 3.6 | Spark spread calculation |
| 4 | Metrics with interpretation |
| 5.1–5.5 | Charts 1–5 (saved as PNGs) |
| 6 | Daily cross-commodity risk summary |
| 7.1 | Ollama desk note generation |
| 7.2 | PDF export |

---

## Sample Output

```
=== DAILY CROSS-COMMODITY RISK SUMMARY ===

Gas Market:
  • TTF: €46.93 (-1.2% 30d) | Range: €38.77–€53.25 | Avg: €45.22
  • 30d Realized Vol: 87.5%
  • TTF Signal: 45–55 EUR/MWh → Moderate tightness (current regime)
  • EU Storage: 34.07% (-19.0pp vs monthly avg) → Critically low, strongly bullish

Carbon & Power:
  • EUA: €72.9/tCO₂ (Carbon cost/MWh: €29.2)
  • EUA Signal: 65–75 EUR/t → Constructive (current range)
  • German Power: DA €62.5 | 1Y Forward €91.65 (Premium: €29.15)
  • Spark Spread: €-60.5/MWh → Gas plants unprofitable → Bullish for power

Risk Assessment:
  • Risk Level: HIGH (Score: 6/10)
    Components → Gas: Moderate | Vol: Very High | Storage: Critical | EUA: Normal
  • Strong bullish bias for winter 2026/27 power curve
  • Bullish bias — consider long power curve positions
```

---

## Known Limitations

- **`TTF=F` on Yahoo Finance is unreliable** — it returns empty data intermittently. When it fails, the notebook silently falls back to hardcoded defaults (`TTF=48.5`, `VOL=42.5`). For production use, replace with a reliable data source (e.g. EPEX, ICE, Refinitiv).
- **EUA and German power prices are manual** — there is no free public API that provides these reliably. Update them each morning before running.
- **EUA vs Power Forward chart (Chart 3) uses simulated data** — the forward curve array is hardcoded. Update it manually or wire up a real data source.
- **`fpdf2` core fonts (Helvetica) are latin-1 only** — the `sanitize()` function handles common Unicode characters from LLM output, but unusual symbols may render as `?`.

---

## Requirements

```
Python 3.10+
pandas
numpy
matplotlib
yfinance
requests
fpdf2
ollama (llama3.1 model)
```

---

## File Outputs

| File | Description |
|---|---|
| `chart_1.png` – `chart_5.png` | Chart images (regenerated each run) |
| `monthly_storage_averages.json` | 5-year monthly storage averages (used for deviation) |
| `monthly_storage_stats.json` | 5-year monthly min/max/avg (used for Chart 1) |
| `EU_Risk_Monitor_YYYYMMDD_HHMM.pdf` | Daily PDF report |

---

## Author

**Clifford Pang**
- X: [@cliff4dp](https://x.com/cliff4dp)
- Version: 12.0 | May 2026

---

## License

This project is provided as-is for educational and personal use.
Feel free to fork, modify, and improve.

---

**Built for energy traders who want clarity in a noisy market.**
