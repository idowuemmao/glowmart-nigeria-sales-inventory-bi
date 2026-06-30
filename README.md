# GlowMart Nigeria — Sales vs. Inventory BI Resolution

A 3-page Power BI analytics report resolving a real-world inventory allocation conflict for a simulated FMCG beauty & personal care retailer, built for the SkillAhead Data Analytics Challenge.

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-217346?style=flat&logo=microsoft-excel&logoColor=white)
![Status](https://img.shields.io/badge/status-complete-success)

---

## 📌 Project Summary

GlowMart Nigeria Ltd is a mid-size FMCG beauty and personal care retailer operating across 5 Nigerian cities (Lagos, Abuja, Kano, Ibadan, Port Harcourt), with 500 customers, 90 products across 4 brands, and 15,000 transactions recorded between 2022–2024.

A management dispute arose between the Sales Manager and the Inventory Manager over stock allocation:

> **Sales:** *"My team is losing sales because Inventory is not stocking the right products in the right cities. Port Harcourt and Kano customers are ready to buy, but the shelves are empty."*
>
> **Inventory:** *"I stock based on what Sales tells me to plan for. My records show equal allocation across all five cities — show me the data."*

The MD commissioned a single dashboard to resolve this conflict with evidence and guide future inventory allocation decisions. This repository contains that dashboard, the underlying data model, the full DAX measure library, and the strategic recommendations delivered to leadership.

---

## ⚖️ The Conflict — Resolved

**Verdict:** Both managers were partially correct.

- **Inventory was right on the facts** — allocation was broadly equal across all five cities (18.5%–21.6% revenue share range, a tight spread).
- **Sales was right on the implication** — equal allocation is the wrong strategy when demand is unequal. Port Harcourt generated ₦141.0M in revenue from 3,210 transactions, the highest of any city, yet received identical stock to Lagos, the lowest-demand city at 2,760 transactions and ₦121.0M in revenue.
- **A supporting signal validated Sales' concern further** — Kano's transaction count dropped from 1,033 (2023) to 971 (2024), a decline of 62 transactions consistent with early demand-frustration from understocking.

**Resolution delivered to the MD:** Move from equal allocation to demand-weighted, quarterly-rebalanced allocation based on prior-quarter transaction share per city.

---

## 📊 Key Findings

| Metric | Value | Benchmark | Status |
|---|---|---|---|
| Total Revenue | ₦652.60M | ₦600–700M | ✅ On target |
| Gross Profit Margin | 27.64% | 25–35% | ✅ On target |
| Avg Order Value | ₦43,506.92 | ₦40K–50K | ✅ On target |
| Total Transactions | 15,000 | ~5,000/yr | ✅ On benchmark |
| Total Units Sold | 45,226 | 45K–46K | ✅ On target |
| Transactions per Customer | 30.0 | 30 | ✅ Exact match |
| Avg Discount Rate | 9.96% | ≤10% | ⚠️ At upper limit |
| 3-Year Revenue Growth | 1.11% | ≥15% (3-yr) | ❌ Below target |

**Brand insight:** BrandA leads every single city in revenue and holds the highest GP margin (31.3%) — confirming it as GlowMart's anchor brand and single highest stockout risk. BrandC's GP margin (22.1%) is the only brand below the 25% FMCG profitability floor.

**City insight:** Port Harcourt ranks #1 in revenue, #1 in AOV (₦43,921), and #2 in GP margin — the strongest case for priority inventory allocation in the entire dataset.

---

## 🏗️ Data Model

A standard star-schema model with one fact table and three dimension tables:

```
            DimCustomers
                 │
                 │ (CustomerID, 1:*)
                 ▼
DimDate ──── FactSales ──── DimProducts
(Date, 1:*)              (ProductID, 1:*)
```

| Table | Type | Key | Role |
|---|---|---|---|
| FactSales | Fact | SalesID | All transactions — CustomerID, ProductID, OrderDate, Quantity, Unit Price, Discount, Total Sales |
| DimCustomers | Dimension | CustomerID | City, Region, Gender, Age Group, Signup Date |
| DimProducts | Dimension | ProductID | Brand, Cost Price, Category, Subcategory |
| DimDate | Dimension | Date | Year, Month, Quarter, Year-Month, Day of Week, Is Weekend |

All relationships are **one-to-many, single filter direction**, flowing from each dimension into the fact table — the standard star-schema configuration for Power BI.

---

## 📐 KPI & DAX Measure Library

12 core KPIs were calculated against documented business thresholds, each backed by a verified DAX measure:

1. Total Revenue
2. Total Transactions
3. Average Order Value (AOV)
4. Total Units Sold
5. Gross Profit
6. Gross Profit Margin %
7. Revenue by City
8. Revenue by Brand
9. Total Discount Amount
10. Year-on-Year Revenue Growth %
11. Transactions per Customer
12. Revenue per Customer

In addition to the core KPI library, the report uses **dynamic narrative measures** — DAX text measures that recompute their insight sentence in real time based on whatever slicer context is active (city, brand, year, etc.), rather than static commentary. These include:

- `Status_*` measures — threshold-aware KPI card labels (e.g. "✓ Within ₦600–700M target")
- `Insight_YoY_Revenue_Trend` — auto-narrates growth trajectory and flags stagnation
- `Insight_Brand_Performance` — dynamically identifies leader/laggard brand by revenue and GP margin
- `Insight_City_Priority` — ranks cities by revenue, AOV, and GP margin to surface the priority allocation target
- `Insight_Conflict_Verdict` — computes the allocation equity spread and renders the Sales vs. Inventory verdict live
- `Insight_Brand_City_Dominance` — detects whether a brand dominates all cities or only some, adjusting language accordingly when filtered
- `Reco_1 / Reco_2 / Reco_3` — recommendation cards that recalculate supporting figures dynamically (e.g. margin uplift potential, transaction gaps)

Key DAX techniques demonstrated: `SUMX` row-context iteration, `RELATED()` cross-table lookups, `CALCULATE()` + `ALLEXCEPT()` for dimensional filtering, `SAMEPERIODLASTYEAR()` time intelligence, `DISTINCTCOUNT()`, `RANKX()` with virtual tables, `TOPN()`, `CROSSJOIN()`, `ADDCOLUMNS()` for derived virtual columns, and `CONCATENATEX()` for dynamic slicer-aware text.

> Full measure code is documented in [`/dax/measures.md`](./dax/measures.md).

---

## 📑 Report Structure (3 Pages)

## Page 1: Overview 
Executive performance summary — all 8 core KPIs, revenue trend by month/quarter/year with MoM%, brand profitability scatter (revenue vs. gross profit) 

<img width="864" height="492" alt="1" src="https://github.com/user-attachments/assets/c6e4fce0-2752-49b5-a1be-32cd22f6baa4" />

## Page 2: Resolution
Conflict resolution — expected allocation % vs. revenue share % vs. demand gap % by city, yearly transaction trend by city, the data verdict 

<img width="863" height="492" alt="2" src="https://github.com/user-attachments/assets/49674d9f-0af9-418c-ba8c-6954b2f10167" />

## Page 3: Recommendations 
Strategic outlook — highest/lowest/fastest-growing cities, brand-by-city revenue matrix, three evidence-backed recommendations for the MD 

<img width="863" height="493" alt="3" src="https://github.com/user-attachments/assets/2112856f-c410-48ee-97dc-aef56c48f8f5" />


Each page connects narratively to the next: Overview establishes the baseline → Resolution interrogates the conflict using city and brand data → Recommendations translates findings into specific, numbers-backed actions.

---

## 🎯 Recommendations Delivered

1. **Implement demand-weighted inventory allocation** — replace equal city allocation with a quarterly rebalancing model tied to prior-quarter transaction share. Closes the 450-transaction gap between Port Harcourt and Lagos.
2. **Protect BrandA stock, review BrandC pricing** — BrandA is the anchor brand and highest stockout risk; BrandC's 22.1% GP margin sits below the FMCG floor — a 5–8% price adjustment could recover ~₦13.4M in gross profit.
3. **Launch a growth recovery plan** — 3-year revenue growth (1.11%) is well below the 15% target despite strong customer retention. Recommended: push AOV toward ₦48,000 and reduce average discount rate below 8%.

---

## 🛠️ Tools & Skills Used

- **Microsoft Power BI Desktop** — data modeling, DAX, report design
- **DAX** — 12 core KPI measures + 8+ dynamic narrative/status measures
- **Star schema modeling** — fact/dimension design with proper cardinality and filter direction
- **Power Query** — data shaping and transformation (if applicable to your build)

---

## 📁 Repository Structure

```
├── README.md
├── /pbix
│   └── GlowMart_Nigeria_BI_Dashboard.pbix
├── /dataset
│   └── SkillAhead_GlowMart_Dataset.xlsx
├── /dax
│   └── measures.md
├── /docs
│   └── KPI_Reference_Document.pdf
└── /screenshots
    ├── page1_overview.png
    ├── page2_resolution.png
    └── page3_recommendations.png
```

---

## 🎥 Video Walkthrough

A full video walkthrough covering the data model, all three report pages, dynamic insight measures, page-level recommendations, and selected complex DAX is available: [HERE](https://www.linkedin.com/posts/emmanuel-idowu-analyst_powerbi-dax-dataanalytics-ugcPost-7477439032755392512-4MXL/)

---

## 👤 About

Built by **Emmy** as a portfolio submission for the [SkillAhead Academy](https://www.linkedin.com/company/skillahead/) AND [Etuk Anietie](https://www.linkedin.com/in/etukanietie/) Data Analytics Challenge, and as part of ongoing data analytics and Power BI development work under **Manifest Data**.

This project was completed independently for skill development and portfolio purposes.

---
#SkillAheadChallenge #JKBChallenge #datadna 
