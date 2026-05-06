# European Cross-Commodity Risk Monitor v12

**Gas + Carbon → Power Curve Implications**

An automated daily risk monitoring notebook for European energy markets. It transforms public gas and carbon fundamentals into clear, actionable trading insights for the European power curve.

---

## Overview

This notebook automates the daily analysis of cross-commodity risk between:
- **TTF Natural Gas**
- **EUA Carbon**
- **German Power (Day-Ahead + 1Y Forward)**
- **EU Gas Storage**

It produces a professional **Daily Summary** with dynamic risk scoring and a full set of charts to support trading decisions.

---

## Key Features

- **Automated Data Pipeline**
  - TTF prices & volatility via `yfinance`
  - EU gas storage via GIE AGSI API (with historical + weekly data)
  - Manual inputs for EUA and German power prices

- **Dynamic Interpretation**
  - Uses rule-based logic for TTF, EUA, Storage, and Spark Spread
  - No hardcoded text — everything adapts to live data

- **Smart Risk Assessment**
  - Weighted risk scoring (0–10) combining TTF price, volatility, storage, and EUA
  - Clear component breakdown (Gas / Vol / Storage / EUA)
  - Dynamic recommendation engine

- **Professional Charts**
  - EU Gas Storage vs 5-Year Average
  - TTF Price vs EU Storage (6-month)
  - EUA vs German Power Forward Curve
  - Price & Storage Dashboard
  - TTF Price vs Realized Volatility

- **Clean Daily Summary**
  - Concise, desk-ready output
  - Fully dynamic with no hardcoded strings

---

## Quick Start

1. Open the notebook in **Jupyter**, **Google Colab**, or **VS Code**
2. Run all cells in order
3. Update manual inputs daily:
   - `EUA_PRICE`
   - `DE_DA`
   - `DE_1Y`
4. Re-run the notebook each morning

> **Note:** The GIE AGSI API key is hardcoded (free tier). You can replace it with your own key if needed.

---

## Sample Output

```text
=== DAILY CROSS-COMMODITY RISK SUMMARY (v11) ===

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

## Data Sources

| Data                  | Source                          | Update Frequency |
|-----------------------|----------------------------------|------------------|
| TTF Prices & Volatility | Yahoo Finance (`TTF=F`)         | Daily            |
| EU Gas Storage        | GIE AGSI API                    | Daily + Historical |
| EUA Carbon Price      | Manual input                    | Daily            |
| German Power Prices   | Manual input                    | Daily            |

---

## Requirements

- Python 3.10+
- `pandas`, `numpy`, `matplotlib`, `yfinance`, `requests`

All dependencies are standard and listed at the top of the notebook.

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

**Built with ❤️ for energy traders who want clarity in a noisy market.**