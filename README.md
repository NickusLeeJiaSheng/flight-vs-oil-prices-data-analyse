# Airline Financial Resilience During Global Crises (2019–2026)

An end-to-end data analysis project examining how geopolitical crises, oil price shocks, and fuel hedging strategies affected airline profitability and passenger ticket prices across 25 major airlines from Q1 2019 to Q1 2026.

---

## 📊 Interactive Dashboard

**[View on Tableau Public →](https://public.tableau.com/app/profile/nickus.lee/viz/AirlineFinancialResilienceDuringGlobalCrises20192026/GeopoliticalEventTracker#1)**

---

## 📁 Project Structure

```
airline-crisis-analysis/
├── data/
│   ├── airline_financial_impact.csv 
│   ├── airline_ticket_prices.csv    
│   ├── oil_jet_fuel_prices.csv     
│   ├── conflict_oil_events.csv       
│   ├── fuel_surcharges.csv          
│   └── route_cost_impact.csv 
│   ├── cleaned/
│       ├── clean_financial.csv
│       ├── clean_tickets.csv
│       ├── clean_oil.csv
│       ├── clean_events.csv
│       └── clean_routes.csv
├── notebooks/
│   └── airline_eda.ipynb        
└── README.md
```

---

## Research Questions

1. **Did fuel hedging actually protect profit margins during price shocks?**
2. **How fast did oil price increases pass through to passenger ticket prices?**
3. **What is the lag between a geopolitical event and airfare impact?**

---

## Data Cleaning

All 6 datasets were audited across four dimensions before analysis:

**Missing Values**
- `yoy_price_change_pct` (tickets) and `yoy_brent_change_pct` (oil): nulls confirmed to 2019 only - structurally missing as no prior year exists. Kept as NaN.
- `days_since_prev_event` (events): 1 null for the first event. Filled with 0.

**Outliers**
All outliers identified (values beyond 3 S.D.) were **retained** with justification:
- `fuel_cost_pct_revenue` reaching 143% - COVID period where revenue collapsed below fuel costs. Removing these rows would erase the most analytically important data points.
- Brent crude at $155–169/barrel — real US-Iran War period prices, central to the analysis.
- Extra rerouting distances up to 2,345 km - real operational impact of Strait of Hormuz closure.

**Logic Checks**
10 assertions validated across all datasets - revenue non-negative, hedging between 0-100%, surcharge less than total fare, jet fuel price exceeding crude price (refining margin), extra distances non-negative. All passed.

**Type Casting & Derived Columns**
- Parsed all date columns to datetime
- Applied ordered categorical to `conflict_phase` for correct chart ordering
- Created `fuel_ratio` (fuel cost / revenue) and `surcharge_pct_fare` (surcharge / total fare)

---

## Key Findings

### Finding 1 - Fuel Hedging Protects Against Cost Shocks, Not Demand Shocks

Average profit margins by crisis phase:

| Crisis Phase | Avg Profit Margin |
|---|---|
| Pre-Pandemic Baseline | +9.18% |
| COVID-19 Collapse | -33.45% |
| Ukraine War Shock | +6.15% |
| US-Iran War Conflict | *severe disruption* |

During the **Ukraine War Shock** (a fuel cost shock), hedging showed minimal differentiation across coverage levels — airlines with 0–25% hedging averaged 6.16% margin vs 6.33% for 50-75% hedging (r = -0.013, p = 0.910). The effect was statistically insignificant.

During **COVID-19** (a demand shock), hedging provided almost no protection - all airlines regardless of hedging level posted deeply negative margins averaging -33.45%.

**Implication:** Fuel hedging is a cost management tool, not a crisis management tool. Airlines that survived COVID did so through capacity cuts and government support - not hedging strategy.

---

### Finding 2 - Fuel Surcharges Pass Through to Passengers Almost Immediately

Correlation between jet fuel price and average fuel surcharge:

| Lag | Correlation (r) |
|---|---|
| Same month (Lag 0) | **0.888** |
| 1 month later | 0.764 |
| 2 months later | 0.670 |
| 3 months later | 0.607 |

Surcharges adjust **within the same month** as fuel price changes - airlines do not absorb cost increases before passing them to passengers.

The magnitude of pass-through varies dramatically by route class:

| Route Class | Pass-Through Rate |
|---|---|
| Ultra-Long-Haul | **$7.65 per $1 fuel increase** |
| Long-Haul | $3.25 |
| Medium-Haul | $1.08 |
| Short-Haul Regional | $0.40 |
| Short-Haul Domestic | $0.18 |

Ultra-long-haul passengers absorb **43x more fuel cost** than short-haul domestic passengers per $1 increase in jet fuel. This is driven by fuel consumption scaling non-linearly with distance.

---

### Finding 3 - Event Severity Predicts Airfare Impact More Than Event Type

Correlation between oil price change at event and subsequent airfare impact: **r = 0.632 (p < 0.001)**

Average airfare impact by severity:

| Severity | Avg Oil Price Change | Avg Airfare Impact |
|---|---|---|
| Medium | +3.1% | +1.3% |
| High | +1.8% | +3.7% |
| Very High | +1.3% | +3.1% |
| Extreme | +4.4% | **+11.8%** |

Extreme events - Strait of Hormuz closure, Ukraine invasion, US-Iran strikes - produce airfare impacts 9x larger than medium severity events. The magnitude of supply disruption matters more than the headline category.

---

### Finding 4 - Crisis Phase Is the Dominant Predictor of Profit Margins (Regression)

A Random Forest regression predicting `profit_margin_pct` from 20+ features achieved:
- **R² = 0.904** on held-out test data
- **MAE = 3.87%** (predictions accurate to within ~4 percentage points)

Feature importance:

| Feature | Importance |
|---|---|
| Crisis: COVID-19 Collapse | **85.8%** |
| Jet Fuel Price | 3.3% |
| Crisis: US-Iran War | 2.0% |
| Fuel Hedging Coverage % | 1.8% |
| Brent Crude Price | 1.7% |

The COVID-19 phase alone accounts for 86% of the model's predictive power. This confirms that **demand shocks dwarf all other variables** in determining airline profitability — fuel prices, hedging, fleet size, and region are secondary.

---

## Tools & Methodology

| Tool | Usage |
|---|---|
| Python (Pandas, NumPy) | Data cleaning, EDA, feature engineering |
| Matplotlib, Seaborn | Static visualisations |
| SciPy | Correlation analysis, regression statistics |
| Scikit-learn | Random Forest, Linear Regression, cross-validation |
| Tableau Public | Interactive dashboards |

**Models compared:** Linear Regression, Random Forest, Gradient Boosting — all achieved R² > 0.90. Linear Regression and Random Forest performed equivalently (R² ≈ 0.907), suggesting the underlying relationships are largely linear once crisis phase is encoded.

---

## Conclusion

1. **Airlines should not rely on fuel hedging as a primary crisis risk tool.** The data shows hedging provides marginal margin protection during fuel cost shocks but zero protection during demand collapses. Investment in demand resilience - route diversification, flexible capacity agreements - provides broader protection.

2. **Long-haul passengers are disproportionately exposed to fuel price volatility.** A $10/barrel fuel increase adds $76.50 to an ultra-long-haul ticket vs $1.80 to a short-haul domestic fare. Airlines targeting premium long-haul markets face greater revenue sensitivity to oil markets.

3. **Geopolitical risk monitoring should focus on supply disruption magnitude, not event type.** Political events and military strikes produce similar airfare responses when the oil supply impact is small. Only events that materially disrupt supply - blockades, major sanctions - warrant pricing responses.

