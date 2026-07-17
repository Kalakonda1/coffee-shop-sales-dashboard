# ☕ Coffee Shop Sales Dashboard

An interactive Excel dashboard that turns six months of raw point-of-sale data from a three-location coffee shop chain into a decision-ready view of revenue, transaction patterns, and product performance.

> Project source: [Maven Analytics Data Playground](https://mavenanalytics.io) — *Coffee Shop Dashboard*
> Tags: `Excel` `Food & Beverage` `Time Series` `Retail`

---

## Why I built this

Raw transaction logs are where most real-world analysis actually starts — not a clean, pre-aggregated dataset. I picked this project because it mirrors a problem every retail and food & beverage business faces: **you have tens of thousands of individual sales records, and you need to turn them into an answer someone can act on in the next five minutes** — which store is underperforming, which hour needs more staff, which product to push.

I chose this project over a "prettier" or more novel dataset because:
- It's **time-series retail data**, which forces real decisions about grouping (by day, hour, week, month) and seasonality instead of a single flat summary.
- It's **multi-location**, so the analysis has to support comparison, not just a single aggregate.
- It's a **realistic transaction grain** (~149K line items across 47K–51K checkouts per store) — large enough that manual eyeballing doesn't work and the analysis has to be built to scale, closer to what a real BI/reporting pipeline looks like.

## What the dashboard answers

- **How is revenue trending month over month, and where are the peaks?**
- **When are transactions happening** — which days of the week and which hours drive the most traffic (useful for staffing and inventory decisions)?
- **What's actually selling** — which product categories and product types generate the most revenue vs. the most volume (a high-count, low-revenue item is a very different lever than a low-count, high-revenue item)?
- **How do the three store locations compare**, and how does the picture change when you filter down to just one?

## Dataset

| | |
|---|---|
| **Grain** | One row per line-item transaction |
| **Rows** | 149,116 line items |
| **Period** | Jan 1, 2023 – Jun 30, 2023 |
| **Locations** | Lower Manhattan, Hell's Kitchen, Astoria |
| **Categories** | Coffee, Tea, Bakery, Drinking Chocolate, Flavours, Coffee Beans, Loose Tea, Branded, Packaged Chocolate |
| **Fields** | `transaction_id`, `transaction_date`, `transaction_time`, `transaction_qty`, `store_id`, `store_location`, `product_id`, `unit_price`, `Revenue`, `product_category`, `product_type`, `product_detail`, plus derived `Month`, `Day of week`, `Hour` |

### Headline numbers (all stores, full period)

| Store | Revenue | Transactions |
|---|---|---|
| Lower Manhattan | $230,057.25 | 47,782 |
| Hell's Kitchen | $236,511.17 | 50,735 |
| Astoria | $232,243.91 | 50,599 |
| **Total** | **$698,812.33** | **149,116** |

> Note: the KPI cards on the **Charts** tab are wired to the store-location slicer, so they display whichever store is currently selected (the file is saved filtered to Lower Manhattan — $230,057 revenue, 47,782 transactions, top category Coffee). That's intentional: it's what proves the filter is actually driving the dashboard rather than being decorative. Clear the slicer to see all three stores combined.

## How the workbook is built

The `.xlsx` is organized as a three-layer model — the same pattern used in any BI tool (raw table → semantic/aggregation layer → presentation layer):

1. **`Transactions`** — the raw, unmodified fact table (149,116 rows). This is the single source of truth; every number on the dashboard traces back to it.
2. **`Sheet1`** — PivotTables that aggregate the fact table into the cuts the dashboard needs: revenue by month, transactions by day-of-week, and product-type performance (count + revenue).
3. **`Charts`** — the dashboard itself: KPI cards (Total Revenue, Total Transactions, Top Category), a store-location filter (slicer), and linked charts/PivotCharts built on top of the `Sheet1` pivots so everything updates together when you filter.

Because it's built on native PivotTables/PivotCharts rather than static charts, **the whole dashboard recalculates and re-renders when the store filter changes** — it behaves like a lightweight BI tool rather than a one-off report.

## Skills demonstrated (and how they apply to real-time analysis)

- **Data modeling from raw transactional data** — going from a flat, ungrouped log to a structured, query-able fact table, the same first step needed before any real-time dashboard, database view, or BI semantic layer can be built.
- **PivotTables & PivotCharts** — the Excel equivalent of writing `GROUP BY` aggregations; this translates directly to SQL aggregation and BI-tool measures (Power BI, Tableau, Looker).
- **Time-series aggregation** — rolling data up to month, day-of-week, and hour granularities to expose seasonality and daily patterns, a core skill for any monitoring or trend-tracking dashboard.
- **Slicers / interactive filtering** — building a single filter (store location) that drives every visual at once, the core UX pattern behind real-time, self-serve dashboards.
- **KPI design** — choosing the handful of numbers that matter most (Total Revenue, Total Transactions, Top Category) instead of showing everything, which is the actual hard part of dashboard design.
- **Data integrity** — keeping the raw data layer untouched and separate from the aggregation/presentation layers, so the dashboard is always auditable back to source, and totals can be reconciled (e.g. per-store revenue sums to the grand total).

## Repo structure

```
coffee-shop-dashboard/
├── README.md
├── .gitignore
├── data/
│   └── Coffee_Shop_Sales.xlsx   # raw transactions + pivot tables + dashboard
└── screenshots/                 # dashboard preview images (add your own exports)
```

## How to use it

1. Download `data/Coffee_Shop_Sales.xlsx` and open it in Excel (PivotTables and slicers require desktop Excel — Google Sheets will not render them correctly).
2. Go to the **Charts** tab to see the dashboard.
3. Use the **store location** filter to switch between Lower Manhattan, Hell's Kitchen, and Astoria, or clear it to see all three combined.
4. Explore the underlying aggregations on **Sheet1**, or the raw data on **Transactions**.

## Possible extensions

- Rebuild the same aggregations in Python (pandas) or SQL to compare approaches and validate totals independently.
- Port the dashboard to Power BI or Tableau for true real-time refresh against a live database.
- Add year-over-year comparison once additional periods of data are available.
- Add an hour-of-day heatmap to make staffing recommendations more concrete.

---

*Original dataset and prompt courtesy of [Maven Analytics](https://mavenanalytics.io).*
