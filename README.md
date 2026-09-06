# Supply Chain Analytics — Advanced Excel

An end-to-end supply chain analysis built entirely in Excel, covering Power Query data cleaning, a relational Data Model with Power Pivot and DAX, business-question-driven Pivot Table analysis, What-If Analysis, and an interactive dashboard. No Power BI involved, every technique here uses Excel's own native and add-in capabilities.

## Status: Complete

## Dataset

**DataCo Smart Supply Chain Dataset** (Mendeley Data / Kaggle), 180,519 order-line records across 53 original columns, spanning January 2015 to January 2018. Covers orders, customers, products, categories, shipping, and delivery outcomes across five global markets.

## Tools

Microsoft Excel: Power Query, Power Pivot (Data Model + DAX measures), Pivot Tables & Pivot Charts, What-If Analysis (Goal Seek, Scenario Manager), Conditional Formatting, INDEX/MATCH.

## Methodology

**1. Power Query cleaning.** The raw file required an explicit encoding fix (ISO-8859-1, not the default UTF-8) to correctly display accented city and country names. Seven columns were removed: two for data quality (`Product Description`, 100% empty; `Order Zipcode`, 86% missing) and five for privacy (`Customer Email`, `Fname`, `Lname`, `Password`, `Street`), even though this is a synthetic academic dataset, stripping personally-identifying fields is the correct habit regardless. Both date columns were originally stored as text in US format (month/day/year); converting them required explicitly specifying `English (United States)` locale during the type conversion, since a naive conversion under a different regional setting would have silently misread ambiguous dates (day/month swapped).

**2. Relational Data Model.** The single wide source table was split into four related tables using a disabled-load staging query as the shared source: **Orders** (fact table, kept at line-item grain, 180,519 rows), **Customers** (20,652 rows), **Products** (118 rows), and **Categories** (51 rows). Three relationships connect them: Orders→Customers, Orders→Products, and Products→Categories (a snowflake rather than a pure star, since category information reaches Orders indirectly through Products).

**3. DAX measures.** Five core measures power every analysis in this project: `Total Sales`, `Total Profit` (using the `Benefit per order` column specifically, a documented assumption, see Data Quality Notes), `Profit Margin %`, `Late Delivery Rate`, and `Order Count`. All division uses `DIVIDE()` rather than a plain `/`, so a zero-denominator edge case returns blank instead of an error.

**4. Business-question analysis.** Five Pivot Table views answer the core questions: profitability and delivery performance by region/market, late-delivery rate by shipping mode, top and bottom performing product categories, and customer segment behaviour. INDEX/MATCH and Conditional Formatting provide lookup and visual-flagging capability (this Excel version predates XLOOKUP and FILTER, so these are the version-appropriate equivalents).

**5. What-If Analysis.** Goal Seek models the profit needed to hit a target margin; Scenario Manager compares three shipping-mix scenarios and their effect on the overall late-delivery rate.

**6. Dashboard.** Four KPI cards (Total Sales, Total Profit, Profit Margin %, Late Delivery Rate) alongside two Pivot Charts (profit margin by region, late-delivery rate by shipping mode), each chart built from its own independent PivotTable to avoid shared-dependency conflicts.

## Key findings

- **Southern Africa (13.5%) and Canada (12.8%) have the highest profit margins of any region**, both well above the 10.78% company-wide average. Eastern Asia and Oceania sit lowest, around 9.9-10%.
- **Canada also has the lowest late-delivery rate at 48.8%**, the only region meaningfully below the 52-58% band every other region falls into.
- **Counterintuitive finding**: First Class shipping has a 95.3% late-delivery rate, the worst of any shipping mode, while Standard Class, the slowest and cheapest option, has the best rate at 38.1%. All four shipping modes have order counts in the tens of thousands, ruling out a small-sample explanation. The likely cause: premium shipping options promise tighter delivery windows that are harder to consistently meet, while Standard Class's low expectations make it easy to appear "on time."
- **Fishing is the single dominant product category** at $6.93M in sales, more than the bottom 40+ categories combined. **Books has the lowest profit margin of any meaningfully-sized category at 7.0%** (405 orders, a real enough sample to trust).
- **Customer segments show tight profit margins (10.6-10.9%) but very uneven volume**: the Consumer segment alone accounts for over half of all 180,519 orders.
- **Goal Seek**: reaching a 15% profit margin at current sales levels would require an additional **$1,550,807** in profit, a concrete target for any margin-improvement initiative.
- **Scenario Manager**: redirecting half of First Class orders to Standard Class shipping would drop the overall late-delivery rate from 54.8% to 50.4%; a full redirect would bring it to 46.0%, an 8.8 percentage point improvement without any other change to the business.

## Data quality notes

- **Two profit-like columns exist in the source data**: `Benefit per order` and `Order Profit Per Order`. This analysis uses `Benefit per order` throughout; the two were not fully reconciled given project time constraints, a documented assumption rather than a verified fact.
- **The source data contains a duplicate-ID pattern**: `Customer Id`/`Order Customer Id`, `Product Card Id`/`Order Item Cardprod Id`, and `Category Id`/`Product Category Id` each hold identical values under two different names. The redundant copies were removed from the Orders fact table to avoid confusion once relationships were built.
- **`Product Image` (a URL column) was dropped entirely**, it carried no analytical value and, like other product-level attributes, belonged in the Products dimension table rather than repeated across every order line, if it were needed at all.
- **Excel version constraint**: this workbook was built without access to Power Pivot's dedicated ribbon (not bundled with this Excel edition) or to XLOOKUP/FILTER (introduced after this Excel version). DAX measures were created via the "Add Measure" option inside a Data-Model-sourced PivotTable's field list; INDEX/MATCH and Conditional Formatting substitute for the newer lookup and dynamic-array functions.

## Repository structure

```
supply-chain-analytics/
├── SupplyChainAnalysis.xlsx
├── screenshots/
│   ├── relationships.png
│   ├── dashboard.png
│   └── scenario-manager.png
└── README.md
```

## Screenshots

**Data Model relationships:**

![Relationships](screenshots/relationships.png)

**Dashboard:**

![Dashboard](screenshots/dashboard.png)

**Scenario Manager comparison:**

![Scenario Manager](screenshots/scenario-manager.png)
