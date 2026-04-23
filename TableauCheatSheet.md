# Tableau Cheatsheet + Interview Prep — CustomerInsights.AI Product Intern

> Covers core Tableau skills for dashboard development, data analytics, and client reporting aligned with the ciPARTHENON team.

---

## 1. Tableau Fundamentals

### Key Concepts

| Term | Meaning |
|------|---------|
| **Workbook** | The `.twb` / `.twbx` file containing your work |
| **Worksheet** | A single chart/visualization tab |
| **Dashboard** | A canvas combining multiple worksheets |
| **Story** | A sequence of dashboards/sheets telling a narrative |
| **Data Source** | Connected database, CSV, Excel, SQL, etc. |
| **Extract (.hyper)** | Snapshot of data saved locally for performance |
| **Live Connection** | Real-time query to the source database |

### Dimensions vs Measures

| Dimension | Measure |
|-----------|---------|
| Categorical / qualitative | Numerical / quantitative |
| Blue pills in Tableau | Green pills in Tableau |
| Region, Segment, Product Name | Revenue, Quantity, Profit |
| Used for grouping / slicing | Used for aggregation |

---

## 2. Connecting to Data

```
Supported Sources:
- Files:       Excel, CSV, JSON, PDF, Spatial files
- Databases:   SQL Server, MySQL, PostgreSQL, Oracle
- Cloud:       Google BigQuery, Snowflake, Redshift, Azure SQL
- Other:       Web Data Connector, OData, Tableau Server
```

### Data Interpreter & Union
- **Data Interpreter** — cleans messy Excel/CSV files automatically
- **Union** — stacks rows from multiple tables/sheets (same structure)
- **Join** — combines columns from different tables on a key field
- **Blend** — joins data from different data sources on a common dimension (secondary source aggregates first)
- **Relationship** — flexible, context-aware join (default in Tableau 2020.2+)

### Join Types in Tableau
| Type | Use Case |
|------|----------|
| Inner | Only matching rows |
| Left | All left + matched right |
| Right | All right + matched left |
| Full Outer | All rows from both |

---

## 3. The Tableau Workspace

```
┌─────────────────────────────────────────────┐
│  Menu Bar                                   │
├──────────────┬──────────────────────────────┤
│              │   Columns Shelf               │
│  Data Pane   │   Rows Shelf                 │
│  (Fields)    ├──────────────────────────────┤
│              │                              │
│  Dimensions  │        View / Canvas         │
│  ─────────── │                              │
│  Measures    │                              │
│              │                              │
├──────────────┴──────────────────────────────┤
│  Marks Card: Color | Size | Label | Detail  │
│  Filters Shelf | Pages Shelf                │
└─────────────────────────────────────────────┘
```

### Marks Card Options
| Mark | Use |
|------|-----|
| **Color** | Encode a dimension or measure by color |
| **Size** | Vary mark size by a measure |
| **Label** | Show values on marks |
| **Detail** | Add granularity without visual encoding |
| **Tooltip** | Customize hover information |
| **Shape** | Use custom shapes for categorical data |

---

## 4. Chart Types & When to Use Them

| Chart Type | Best For | How to Build |
|------------|----------|--------------|
| **Bar Chart** | Comparing categories | Dimension on Rows, Measure on Columns |
| **Line Chart** | Trends over time | Date on Columns, Measure on Rows |
| **Scatter Plot** | Correlation between two measures | Measure on Columns + Rows |
| **Pie / Donut** | Part-to-whole (use sparingly) | Dimension on Color, Measure on Angle |
| **Heat Map** | Two dimensions + one measure density | Dimensions on Rows/Columns, Measure on Color |
| **Tree Map** | Hierarchical part-to-whole | Dimension on Color + Label, Measure on Size |
| **Bubble Chart** | 3 measures in one view | X, Y axis + Size |
| **Gantt Chart** | Timelines / project tracking | Date on Columns, Size = duration |
| **Highlight Table** | Cross-tab with color encoding | Dimensions on Rows/Columns, Measure on Color |
| **Box Plot** | Distribution / outliers | Dimension on Columns, Measure on Rows → Show Me |
| **Map** | Geographic data | Lat/Long or country/state fields |
| **Waterfall Chart** | Running total changes | Using calculated fields + Gantt bar |
| **Funnel Chart** | Conversion stages | Sorted bar with custom sizing |

---

## 5. Calculated Fields

### Creating: Analysis → Create Calculated Field (or right-click in data pane)

```tableau
-- Basic Math
[Revenue] - [Cost]
[Profit] / [Revenue] * 100

-- String Functions
UPPER([Client Name])
LEFT([Email], FIND([Email], "@") - 1)
CONTAINS([Region], "North")

-- Date Functions
DATEDIFF('day', [Start Date], [End Date])
DATEPART('month', [Sale Date])
DATETRUNC('quarter', [Sale Date])
TODAY() - [Onboard Date]

-- Logical / CASE
IF [Revenue] > 100000 THEN "Enterprise"
ELSEIF [Revenue] > 25000 THEN "Mid-Market"
ELSE "SMB"
END

CASE [Region]
    WHEN "NA" THEN "North America"
    WHEN "IN" THEN "India"
    ELSE "Other"
END

-- Null Handling
IFNULL([Revenue], 0)
ZN([Revenue])          -- ZN = zero if null shorthand
ISNULL([Client Name])

-- Aggregate calculations
SUM([Revenue]) / SUM([Target])
AVG([Score]) - WINDOW_AVG(AVG([Score]))
```

---

## 6. Table Calculations

> Computed on the result set in Tableau — not at the database level.

| Calculation | Formula / Use |
|-------------|---------------|
| **Running Total** | RUNNING_SUM(SUM([Revenue])) |
| **% of Total** | SUM([Revenue]) / TOTAL(SUM([Revenue])) |
| **Rank** | RANK(SUM([Revenue])) |
| **Difference From** | LOOKUP(SUM([Revenue]), -1) |
| **Moving Average** | WINDOW_AVG(SUM([Revenue]), -2, 0) |
| **YoY % Change** | (SUM([Revenue]) - LOOKUP(SUM([Revenue]), -1)) / ABS(LOOKUP(SUM([Revenue]), -1)) |

**Compute Using options:** Table (Across), Table (Down), Pane, Cell, or specific dimensions.

---

## 7. Level of Detail (LOD) Expressions

> Control the granularity of a calculation, independent of the view.

```tableau
-- FIXED: compute at specified dimension, ignore view filters
{ FIXED [Client ID] : SUM([Revenue]) }

-- INCLUDE: add more detail than the view
{ INCLUDE [Product] : AVG([Revenue]) }

-- EXCLUDE: remove a dimension from the view level
{ EXCLUDE [Month] : SUM([Revenue]) }

-- Real examples:
-- Customer's first purchase date
{ FIXED [Client ID] : MIN([Sale Date]) }

-- Revenue as % of region total (on a row-level view)
SUM([Revenue]) / { FIXED [Region] : SUM([Revenue]) }

-- Count of distinct clients per segment
{ FIXED [Segment] : COUNTD([Client ID]) }
```

**LOD vs Table Calc:**
- LOD runs at **database level** — faster, respects context filters
- Table Calc runs **post-query** in Tableau — more flexible but slower

---

## 8. Filters & Filter Order

### Filter Hierarchy (Top → Bottom execution order)

```
1. Extract Filters       → Applied when creating extract
2. Data Source Filters   → Applied to entire workbook
3. Context Filters       → (Add to Context) — reference point for others
4. Dimension Filters     → Filter on categorical fields
5. Measure Filters       → Filter on aggregated values
6. Table Calc Filters    → Filter after table calculations
```

### Types of Filters
- **Dimension Filter** — filter by category/string/date
- **Measure Filter** — filter aggregated numbers (SUM > 1000)
- **Quick Filter** — interactive dropdown/slider shown on dashboard
- **Context Filter** — makes a filter the baseline for dependent filters
- **Top N Filter** — show only top/bottom N records
- **Relative Date Filter** — last 30 days, last quarter, etc.

---

## 9. Parameters

> User-controlled variables that dynamically change views, calculations, or filters.

```
Use cases:
- Switch metrics dynamically (Revenue / Profit / Units)
- Top N control (show top 5 / 10 / 20)
- Date range selector
- Scenario modeling (what-if analysis)
- Toggle between chart types
```

```tableau
-- Parameter: [Select Metric] (string: Revenue, Profit, Margin)
-- Calculated Field using parameter:
CASE [Select Metric]
    WHEN "Revenue" THEN SUM([Revenue])
    WHEN "Profit" THEN SUM([Profit])
    WHEN "Margin" THEN SUM([Profit]) / SUM([Revenue])
END
```

---

## 10. Dashboard Design Best Practices

### Layout
- Use **Tiled vs Floating** layout intentionally (Tiled = structured grid, Floating = free placement)
- Set a **fixed dashboard size** for consistent client delivery (e.g., 1200 × 800)
- Use **containers** (horizontal/vertical) to group and align elements
- Leave whitespace — don't overcrowd

### Interactivity
- **Filter Actions** — click a mark to filter other sheets
- **Highlight Actions** — highlight related marks across sheets
- **URL Actions** — open external links from marks
- **Sheet Swap** — show/hide sheets using parameters + floating containers
- **Navigation Buttons** — link between dashboards in a story

### Performance
- Use **Extracts** over live connections for large datasets
- **Hide unused fields** from the data source
- Reduce the number of marks on a single sheet
- Use **aggregated data** wherever possible
- Avoid too many quick filters simultaneously

---

## 11. Aggregation Functions

```tableau
SUM([Revenue])
AVG([Score])
MIN([Date])
MAX([Date])
COUNT([Order ID])
COUNTD([Client ID])     -- Count Distinct (unique values)
MEDIAN([Revenue])
ATTR([Region])          -- Returns value if all rows are same, else *
```

---

## 12. Sets & Groups

### Groups
- Manually combine dimension members (e.g., group "Pharma" + "Biotech" → "Life Sciences")
- Right-click dimension → Create → Group

### Sets
- A subset of dimension members based on a condition
- **Static set** — fixed members you select
- **Dynamic set** — condition-based (top 10 clients by revenue)
- Use in calculations: `IF [Top Clients Set] THEN "Key Account" ELSE "Standard" END`

---

## 13. Sorting & Formatting

```
Sort options:
- Data source order
- Alphabetical
- By field (sort bar chart by SUM(Revenue) descending)
- Manual drag-and-drop
- Nested sort (sort within groups)

Formatting:
- Right-click axis → Format for number format, font, shading
- Format → Workbook for global fonts/colors
- Use color palettes consistently for brand alignment
- Synchronize dual axes for combo charts
```

---

## 14. Advanced Features Quick Reference

| Feature | Use Case |
|---------|----------|
| **Dual Axis** | Overlay bar + line chart |
| **Reference Lines / Bands** | Show targets, averages, thresholds |
| **Trend Lines** | Show statistical trend on scatter/line |
| **Forecasting** | Built-in exponential smoothing |
| **Clustering** | K-means clustering on scatter plots |
| **Animations** | Smooth transitions on filter/parameter changes |
| **Device Layouts** | Separate layouts for desktop/tablet/phone |
| **Embedding** | Embed views in web apps via Tableau JS API |
| **Row-Level Security** | User filters via data source or calculated fields |

---

## 15. Tableau Interview Questions & Model Answers

### 🟢 Basic Level

**Q1. What is the difference between a live connection and an extract in Tableau?**
> A live connection queries the database in real time — always current but depends on DB performance. An extract creates a local `.hyper` snapshot — faster, can be used offline, but requires scheduled refreshes to stay current. For large enterprise datasets like at CustomerInsights.AI, extracts are preferred for dashboard performance.

---

**Q2. What are Dimensions and Measures? Give an example.**
> Dimensions are qualitative/categorical fields (Client Name, Region, Segment) shown as blue pills — used for grouping and slicing. Measures are quantitative numeric fields (Revenue, Count, Score) shown as green pills — used for aggregation. Dragging a measure to a shelf auto-aggregates it (default: SUM).

---

**Q3. What is the difference between JOIN and BLEND in Tableau?**
> A JOIN combines data at the row level from two tables within the same data source, before aggregation. A BLEND works across two different data sources, aggregating the secondary source first and then linking on a common dimension — less precise but necessary when sources can't be joined directly.

---

**Q4. How do you create a calculated field? Give an example.**
> Go to Analysis → Create Calculated Field (or right-click in the Data pane). Example: `[Profit Margin] = SUM([Profit]) / SUM([Revenue])`. Calculated fields let you derive new metrics, apply logic, and handle nulls without modifying the source data.

---

**Q5. What chart type would you use to show revenue trend over 12 months?**
> A line chart — it's ideal for showing continuous trends over time. I'd place the date (truncated to Month) on Columns and SUM(Revenue) on Rows. I might add a reference line for the annual target and a trend line to show the overall direction.

---

### 🟡 Intermediate Level

**Q6. What are Table Calculations? How are they different from regular calculated fields?**
> Table calculations (e.g., RUNNING_SUM, WINDOW_AVG, RANK) compute on the data already returned by the query — they run in Tableau's engine, not the database. Regular calculated fields run at the database level during the query. Table calcs are great for running totals, % of total, and period-over-period comparisons, but they're aware of the view structure, so you must set "Compute Using" correctly.

---

**Q7. Explain LOD expressions with an example.**
> LOD (Level of Detail) expressions let you control aggregation independent of the view. For example, to find each client's first purchase date regardless of how the view is filtered: `{ FIXED [Client ID] : MIN([Sale Date]) }`. This runs at the database level and ignores view-level dimension filters (but respects context filters).

---

**Q8. What is a Context Filter and when would you use it?**
> A context filter acts as a pre-filter that creates a temporary table from which all other filters operate. You'd use it when you have a Top N filter — without context, "Top 10 clients" is computed across all data; with a context filter on Region = 'India', you get the top 10 clients within India specifically.

---

**Q9. How would you build a dynamic dashboard where users can toggle between Revenue, Profit, and Margin?**
> I'd create a **Parameter** (string type with allowed values: Revenue, Profit, Margin), then a calculated field using a CASE statement that returns the appropriate measure based on the selected parameter. I'd display the parameter control on the dashboard as a radio button selector and use the calculated field as the measure in the chart.

---

**Q10. How do you handle NULL values in Tableau?**
> Several ways: `IFNULL([Field], 0)` replaces nulls with 0; `ZN([Field])` is shorthand for the same; `ISNULL([Field])` returns TRUE/FALSE for filtering. For dimension nulls, I can either exclude them using a filter or alias them to "Unknown" for better dashboard readability.

---

**Q11. What is the difference between COUNTD and COUNT?**
> COUNT counts all rows including duplicates; COUNTD counts only distinct/unique values. For example, COUNT([Client ID]) gives total transactions while COUNTD([Client ID]) gives the number of unique clients — critical for KPIs like "Total Active Clients."

---

**Q12. How would you show Year-over-Year growth in Tableau?**
> Using a table calculation: place Year on the Columns shelf alongside Month, then add a Quick Table Calculation → Percent Difference From → Previous. Alternatively, use a calculated field with `LOOKUP(SUM([Revenue]), -1)` to access the prior period's value and compute the percentage change manually for more control.

---

### 🔴 Advanced / Scenario-Based

**Q13. A dashboard is loading slowly. How would you troubleshoot and optimize it?**
> I'd check: (1) Switch from live to extract connection; (2) Reduce the number of marks — simplify charts or aggregate more; (3) Hide unused fields from the data source; (4) Remove or reduce quick filters that trigger independent queries; (5) Use context filters to reduce the dataset scope; (6) Check query performance via Performance Recorder (Help → Settings → Start Performance Recording).

---

**Q14. How do you implement row-level security in Tableau?**
> Create a calculated field that compares the logged-in user to an entitlements table: `[Region] = LOOKUP_REGION(USERNAME())`. Add this as a data source filter so each user only sees their authorized data. On Tableau Server/Cloud, this can also be managed via User Filters or Row-Level Security policies in the data source.

---

**Q15. A client asks for a dashboard that shows their sales performance vs. industry benchmark. How would you design it?**
> I'd blend two data sources — the client's sales data and a benchmark dataset — joined on a common dimension (Region or Segment). I'd use a combo chart (bar for client revenue, line for benchmark) with a dual axis. I'd add color encoding (green = above benchmark, red = below) using a calculated field, plus a summary KPI card showing the overall gap. Filters for date range and region would let clients drill down interactively.

---

**Q16. What is the difference between FIXED, INCLUDE, and EXCLUDE LOD expressions?**
> - **FIXED** computes at the specified dimension regardless of the view — `{ FIXED [Region] : SUM([Revenue]) }` always gives region-level totals even in a more granular view.
> - **INCLUDE** adds dimensions to the aggregation beyond the view level — useful for computing averages of averages correctly.
> - **EXCLUDE** removes a dimension from the current view level — useful for showing totals alongside detail rows.

---

**Q17. Describe a dashboard you built end-to-end.**
> Structure your answer as: **(1) Business problem** — what question needed answering; **(2) Data** — source, volume, cleaning steps; **(3) Design decisions** — chart types chosen and why; **(4) Interactivity** — filters, parameters, actions added; **(5) Outcome** — how stakeholders used it, what decision it drove. Even a personal/academic project counts — focus on your reasoning.

---

**Q18. What is the difference between a dashboard filter and a parameter?**
> A filter restricts data to a subset of existing values (e.g., Region = 'India'). A parameter is a user-controlled input variable that can hold any value and is used inside calculated fields to dynamically change the behavior of the view — it's not a filter itself but drives logic. Parameters are more flexible: they can switch metrics, set thresholds, or control Top N, which filters cannot.

---

## 16. Tips for the Interview

- **Show, don't just tell** — if given a take-home task, polish the dashboard design (consistent colors, proper titles, clean tooltips).
- **Know Tableau's Show Me** — understand which chart types it recommends and why.
- **Be ready to explain your design choices** — why this chart, why this color, why this filter type.
- **Mention performance awareness** — enterprise analytics at CustomerInsights.AI means large datasets; always think about efficiency.
- **Connect Tableau to SQL** — they'll likely ask how you'd pull data from a DB into Tableau; know the flow from SQL query → data source → dashboard.
- **Know the difference between Tableau Desktop, Server, and Cloud.**

---

*Prepared for CustomerInsights.AI Product Intern application — aligned with ciPARTHENON dashboard development, client analytics, and data visualization workflows.*
