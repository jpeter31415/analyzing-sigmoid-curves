

## QQQ Options Analytics: Delta Sigmoid Curve Pipeline

An end-to-end data pipeline that QQQ options data, creates a storage layer, and generates interactive financial visualizations. The primary analytical engine isolates and graphs the mathematical cumulative distribution (sigmoid) curves created by mapping option strike prices against their corresponding Deltas ($\Delta$).

## How It Works

(Yahoo Finance) ──(Python)──> (PostgreSQL Database) ──> (PowerBI Interactive Dashboard)
1. **Extraction (Python):** Connects to market APIs, pulls underlying equities data, parses complex option chains, and calculates/extracts real-time Options Greeks.
2. **Storage (PostgreSQL):** Normalizes and warehouses timeseries asset prices alongside option chain matrices.
3. **Visualization (Power BI):** Transforms raw data into visual insights, isolating risk sensitivity curves.

---

## The Delta Sigmoid Curve

The interactive visualization layer maps the options chain to isolate derivative risk positioning across different expiration dates. By plotting the **Strike Price on the X-Axis** and the **Option Delta ($\Delta$) on the Y-Axis**, the system visually confirms the cumulative distribution function of a normal distribution (the Sigmoid Curve):

* **Call Options ($\Delta \in [0, 1]$):** Form an S-shaped curve ascending from left to right. Deep Out-of-the-Money (OTM) options rest near $0$, shifting sharply at the At-the-Money (ATM) strike ($\approx 0.5$), and flattening near $1.0$ for deep In-the-Money (ITM) positions.
* **Put Options ($\Delta \in [-1, 0]$):** Form an inverted S-shaped curve. Deep ITM options sit near $-1.0$, crossing ATM near $-0.5$, and rising asymptotically up to $0$ as they decay into deep OTM territory.

---

##Project Components

### 1. Ingestion Layer (Python, PostgreSQL)
* **Key Dependencies:** `yfinance`, `pandas`, `sqlalchemy`.
* **Process:** Connects to the QQQ ticker, iterates through all available expiration dates, collects the calls/puts data, and streams the data directly into a PostgreSQL database.

### 2. Dashboards (Power BI)
The reporting tier establishes a connection directly into the Postgres database.
* **Data Connectivity:** Native PostgreSQL database connector.
* **Core Visualization Engine:** 
  * **X-Axis:** `strike_price`
  * **Y-Axis:** `delta`
  * **Legend** `option_type`

#### Interactive Controls & Filters
* **Option Type Slicer:** Instantly toggles between **Call** and **Put** options to isolate individual curves or view their mirrored symmetry side-by-side.
* **Expiration Date Slicer:** Dynamically filters the underlying options by specific contract expirations, allowing users to analyze how the slope of the sigmoid curve steepens or flattens as time to maturity approaches zero.

#### Dashboard Preview
<img width="1425" height="761" alt="call_curve" src="https://github.com/user-attachments/assets/51963442-e8b9-45dc-8107-ec7c23e1e833" />
<img width="1410" height="760" alt="put_curve" src="https://github.com/user-attachments/assets/cf17ac96-3499-42dc-82d5-7dd9885f05a6" />
---

## Insights

A sigmoid delta vs. strike price curve is not completely smooth due to market noise, discrete trading intervals, and order book dynamics. Key factors include bid-ask bounce, liquidity gaps, and stale quotes.Market Microstructure and Data IssuesBid-Ask Bounce: Real-time trades alternate between the bid and ask prices, creating small price jumps that distort implied volatility and delta.Stale Quotes: Out-of-the-money options may not trade frequently, meaning their quoted prices reflect older market conditions rather than the current second.Discrete Strikes: Options exist only at fixed strike intervals, leaving gaps where intermediate values must be estimated or connected.Supply, Demand, and Pricing AnomaliesLiquidity Gaps: Low trading volume at certain strikes causes wider bid-ask spreads and erratic pricing inputs.Asynchronous Trades: Options at different strikes are not executed at the exact same millisecond, capturing different underlying asset prices.Arbitrage and Order Flow: Large block orders or temporary buying pressure can cause a single strike to misprice relative to its neighbors before market makers adjust.
