<div align="center">

# Decathlon PerformanceIQ

### Retail Performance Dashboard | Sales & Profitability Overview

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-3642B8?style=for-the-badge)
![Power Query](https://img.shields.io/badge/Power%20Query-217346?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Draft%20v1.0-orange?style=for-the-badge)

</div>

---

## Table of Contents

- [Overview](#overview)
- [Business Questions](#business-questions)
- [Key Features](#key-features)
- [KPI Definitions](#kpi-definitions)
- [Dashboard Visuals](#dashboard-visuals)
- [Filters and Interactions](#filters-and-interactions)
- [Data Model](#data-model)
- [Reporting-Period Rules](#reporting-period-rules)
- [Data Quality and Validation](#data-quality-and-validation)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
- [Documentation](#documentation)
- [Author](#author)

---

## Overview

**Decathlon PerformanceIQ** is a single-page, interactive **Microsoft Power BI** report that helps the retail management team monitor sales, profitability, order activity, and product performance.

Managers can compare the current year-to-date (YTD) with the same period last year (PYTD) and identify the sports, product types, regions, and brands driving results. The dashboard shows *where* further investigation or commercial action is needed, and it does not imply causes that the data does not establish.


---

## Business Questions

| ID | Question |
|----|----------|
| BQ01 | What are current YTD net sales and gross profit, and how do they compare with PYTD? |
| BQ02 | Is gross margin improving, and by how many percentage points? |
| BQ03 | How have units, order count, and average order value changed? |
| BQ04 | Which months show growth or decline against the same period last year? |
| BQ05 | Which sports generate the most YTD net sales? |
| BQ06 | Which product types combine strong sales with strong margins? |
| BQ07 | How much does each region contribute to YTD sales? |
| BQ08 | Which brands have the strongest and weakest YTD sales growth? |
| BQ09 | How do these answers change when a user applies the available filters? |

---

## Key Features

- **Reporting-period header** showing both years and the fixed cutoff, with September flagged as a partial month
- **6 KPI cards** showing YTD result, PYTD result, and year-over-year change
- **5 analytical visuals** covering time trend, sport, product type, region, and brand
- **5 dropdown slicers** with multi-select support, plus a **reset button**
- **Fixed cutoff date** that does not shift when business slicers change
- **Like-for-like comparison:** the same selected segment is applied to both current and prior year
- **Accessible design:** changes are shown with arrows and text, not colour alone

---

## KPI Definitions

| KPI | Definition | Comparison | Display |
|-----|------------|------------|---------|
| Net Sales YTD | Sum of `Net_Sales_INR` in the YTD window | (YTD − PYTD) / PYTD | INR, millions |
| Gross Profit YTD | Sum of validated `Gross_Profit_INR` in the YTD window | (YTD − PYTD) / PYTD | INR, millions |
| Gross Margin YTD % | YTD gross profit / YTD net sales | YTD margin − PYTD margin | % and change in pp |
| Units Sold YTD | Sum of units using the agreed sold-unit definition | (YTD − PYTD) / PYTD | Thousands |
| Total Orders YTD | Distinct count of valid `Order_ID` in the YTD window | (YTD − PYTD) / PYTD | Thousands |
| AOV YTD | YTD net sales / YTD distinct orders | (YTD − PYTD) / PYTD | INR per order |

**Calculation principles**

- Margin is the ratio of aggregated profit to aggregated sales, not an average of row-level margins
- AOV is calculated from aggregated sales and distinct orders, not an average of line-level values
- A margin change is expressed in **percentage points (pp)**, not relative growth
- If the prior-year value is zero or missing, the change shows **N/A** rather than an infinite or misleading percentage
- Unrounded values are used in calculations; rounding is applied for display only

---

## Dashboard Visuals

| Visual | Purpose |
|--------|---------|
| **Monthly Net Sales** | Current-year vs prior-year monthly sales (Jan to Sep) to identify seasonality and YoY movement |
| **Net Sales YTD by Sport** | Sports ranked by YTD net sales, with an average reference line |
| **Product Type Performance** | Bubble chart: X = Net Sales YTD, Y = Gross Margin YTD %, size = Total Orders YTD |
| **Net Sales YTD by Region** | Regional sales with contribution percentages that reconcile to the sales KPI |
| **Sales Growth by Brand** | Brands ranked by YTD vs PYTD growth, with a clearly labelled internal benchmark |

**Benchmark lines** in this report are internal calculations, not market benchmarks.

---

## Filters and Interactions

| Slicer | Field |
|--------|-------|
| State | `store[State]` |
| Sport | `products[Sport]` |
| Colour | `products[Colour]` |
| Payment Method | `sales[Payment_Method]` |
| Promotion | `sales[Promotion]` |

- All visuals respond consistently to slicer selections
- The **reset button** returns all five slicers to the baseline (all values included)
- Selecting an item in a chart cross-filters the other visuals; clearing it restores the slicer-defined view
- The literal Promotion value *None* is treated as a segment, not as an unfiltered state

---

## Data Model

| Table | Role | Key Fields |
|-------|------|------------|
| `sales` | Transaction fact table (one row per sales line) | `Sale_Line_ID`, `Order_ID`, `Purchase_Date`, `Net_Sales_INR`, `Net_COGS_INR`, `Gross_Profit_INR`, `Quantity`, `Returned_Qty`, `Payment_Method`, `Promotion` |
| `products` | Product dimension | `Product_Key`, `Product_Type`, `Sport`, `Brand_Label`, `Colour` |
| `store` | Store dimension | `Store_Key`, `State`, `Region` |
| `Date` | Continuous calendar dimension | `Date`, `Year`, `Month Num`, `Month Name`, `Month Short`, `Quarter` |

**Model rules**

- One-to-many relationships from product, store, and date keys to the fact table
- Dimension-to-fact filtering
- Date table covers every day from 1 January 2025 through at least 15 September 2026 and extends as new data arrives
- Month labels are sorted by `Month Num`

---

## Reporting-Period Rules

| Ref | Rule |
|-----|------|
| RP01 | The cutoff is the latest valid purchase date in the loaded sales data (reference cutoff: **15 September 2026**) |
| RP02 | **YTD** = 1 January of the cutoff year through the cutoff date, inclusive |
| RP03 | **PYTD** = 1 January of the previous year through the corresponding prior-year cutoff (1 Jan to 15 Sep 2025) |
| RP04 | The cutoff stays fixed when business slicers change |
| RP05 | The monthly chart shows individual monthly sales, not cumulative; September compares days 1 to 15 in both years |
| RP06 | Both years and the cutoff are displayed in the header |
| RP07 | For a leap-day cutoff, a documented prior-year equivalent is used (for example, 28 February for 29 February) |

---

## Data Quality and Validation

The dashboard is validated against a set of acceptance tests, including:

- Calendar completeness and unique dimension keys
- Cutoff and PYTD range validation
- Monthly CY and PY values reconciling to the YTD and PYTD KPIs
- Sport and region breakdowns reconciling to the Net Sales KPI
- Margin and AOV recalculated from totals
- Distinct order counts (no duplicate order lines)
- Zero or missing prior-year sales handled without errors
- Every slicer tested separately and in combination
- Reset behaviour after multiple slicer changes
- Formatting checks for labels, units, colours, and arrows

See the BRD for the full list of 12 acceptance tests.

---

## Repository Structure

```
Decathlon-PerformanceIQ/
├── README.md
├── docs/
│   └── Decathlon_BRD.pdf
├── images/
│   └── dashboard-preview.png
└── powerbi/
    └── Decathlon_PerformanceIQ.pbix
```

---

## Getting Started

**Prerequisites**

- Microsoft Power BI Desktop (latest version)

**Steps**

1. Clone the repository
   ```bash
   git clone https://github.com/<Suryansh-Singhwal>/Decathlon-PerformanceIQ.git
   ```
2. Open `powerbi/Decathlon_PerformanceIQ.pbix` in Power BI Desktop
3. Update the data source path if prompted, then select **Refresh**
4. Use the left-hand slicers to explore performance and the reset button to return to the default view

---

## Documentation

The full requirements are in the Business Requirements Document: [`docs/Decathlon_BRD.pdf`](docs/Decathlon_BRD.pdf)

| Field | Detail |
|-------|--------|
| Document reference | DEC-BRD-RPD-001 |
| Version | 1.0 (Draft for review) |
| Business owner | Retail Management Team |
| Delivery platform | Microsoft Power BI (single interactive report page) |

**Out of scope:** sales targets, forecasting, inventory availability, customer retention, market-share analysis, and claims about promotion effectiveness.

---

## Author

**Suryansh Singhwal**
Retail Analytics & Business Intelligence

[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/<Suryansh-Singhwal>)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/suryansh-singhwal)

---

<div align="center">

*Confidential – For Internal Use Only (if applicable)*

</div>
