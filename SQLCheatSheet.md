# SQL Cheatsheet — CustomerInsights.AI Product Intern

> Focused on data analytics, dashboard reporting, and data validation — key skills for the ciPARTHENON team.

---

## 1. Core SELECT Basics

```sql
-- Retrieve all columns
SELECT * FROM clients;

-- Retrieve specific columns
SELECT client_id, client_name, region FROM clients;

-- Alias columns for cleaner dashboard labels
SELECT client_id AS "Client ID", revenue AS "Total Revenue" FROM sales;

-- Remove duplicates
SELECT DISTINCT region FROM clients;
```

---

## 2. Filtering Data — WHERE Clause

```sql
-- Basic filter
SELECT * FROM clients WHERE region = 'North America';

-- Multiple conditions
SELECT * FROM sales WHERE region = 'India' AND year = 2024;

-- Range filter (useful for time-based analytics)
SELECT * FROM sales WHERE revenue BETWEEN 10000 AND 50000;

-- Pattern matching (useful for searching client names)
SELECT * FROM clients WHERE client_name LIKE 'Bio%';

-- Filter on a list of values
SELECT * FROM clients WHERE segment IN ('Pharma', 'Biotech', 'MedTech');

-- Null checks (critical for data validation)
SELECT * FROM reports WHERE submitted_at IS NULL;
SELECT * FROM reports WHERE submitted_at IS NOT NULL;
```

---

## 3. Sorting & Limiting Results

```sql
-- Sort ascending / descending
SELECT client_name, revenue FROM sales ORDER BY revenue DESC;

-- Limit rows (useful for dashboards / previews)
SELECT * FROM events ORDER BY event_date DESC LIMIT 10;

-- Offset for pagination
SELECT * FROM logs ORDER BY created_at DESC LIMIT 20 OFFSET 40;
```

---

## 4. Aggregate Functions — Insight Generation

```sql
-- Count records
SELECT COUNT(*) AS total_clients FROM clients;

-- Sum, Average, Min, Max
SELECT
    SUM(revenue)     AS total_revenue,
    AVG(revenue)     AS avg_revenue,
    MIN(revenue)     AS min_revenue,
    MAX(revenue)     AS max_revenue
FROM sales
WHERE year = 2024;

-- Count distinct values
SELECT COUNT(DISTINCT client_id) AS unique_clients FROM transactions;
```

---

## 5. Grouping Data — GROUP BY & HAVING

```sql
-- Revenue by region (classic dashboard breakdown)
SELECT region, SUM(revenue) AS total_revenue
FROM sales
GROUP BY region
ORDER BY total_revenue DESC;

-- Filter groups with HAVING (like WHERE but for aggregates)
SELECT segment, COUNT(*) AS client_count
FROM clients
GROUP BY segment
HAVING COUNT(*) > 5;

-- Multi-level grouping
SELECT region, segment, AVG(revenue) AS avg_revenue
FROM sales
GROUP BY region, segment
ORDER BY region, avg_revenue DESC;
```

---

## 6. JOINs — Combining Tables

```sql
-- INNER JOIN: only matching rows in both tables
SELECT s.sale_id, c.client_name, s.revenue
FROM sales s
INNER JOIN clients c ON s.client_id = c.client_id;

-- LEFT JOIN: all rows from left, matched rows from right (nulls for no match)
SELECT c.client_name, s.revenue
FROM clients c
LEFT JOIN sales s ON c.client_id = s.client_id;

-- RIGHT JOIN: all rows from right table
SELECT c.client_name, s.revenue
FROM sales s
RIGHT JOIN clients c ON s.client_id = c.client_id;

-- Multiple joins (common in analytics pipelines)
SELECT c.client_name, p.product_name, s.revenue, s.sale_date
FROM sales s
JOIN clients c ON s.client_id = c.client_id
JOIN products p ON s.product_id = p.product_id
WHERE s.sale_date >= '2024-01-01';
```

| Join Type | Returns |
|-----------|---------|
| INNER JOIN | Only matching rows |
| LEFT JOIN | All from left + matches from right |
| RIGHT JOIN | All from right + matches from left |
| FULL OUTER JOIN | All rows from both tables |

---

## 7. Subqueries

```sql
-- Subquery in WHERE (clients with above-average revenue)
SELECT client_name, revenue
FROM sales
WHERE revenue > (SELECT AVG(revenue) FROM sales);

-- Subquery in FROM (inline derived table)
SELECT region, avg_rev
FROM (
    SELECT region, AVG(revenue) AS avg_rev
    FROM sales
    GROUP BY region
) AS regional_summary
WHERE avg_rev > 20000;

-- Subquery with IN
SELECT client_name FROM clients
WHERE client_id IN (
    SELECT DISTINCT client_id FROM sales WHERE year = 2024
);
```

---

## 8. CTEs (Common Table Expressions) — Readable Complex Queries

```sql
-- Basic CTE (great for multi-step analytics workflows)
WITH regional_totals AS (
    SELECT region, SUM(revenue) AS total_revenue
    FROM sales
    GROUP BY region
),
top_regions AS (
    SELECT region
    FROM regional_totals
    WHERE total_revenue > 100000
)
SELECT c.client_name, s.revenue, s.region
FROM sales s
JOIN clients c ON s.client_id = c.client_id
WHERE s.region IN (SELECT region FROM top_regions);
```

---

## 9. Window Functions — Advanced Analytics

```sql
-- Running total (cumulative revenue)
SELECT sale_date, revenue,
    SUM(revenue) OVER (ORDER BY sale_date) AS running_total
FROM sales;

-- Rank clients by revenue within each region
SELECT client_name, region, revenue,
    RANK() OVER (PARTITION BY region ORDER BY revenue DESC) AS revenue_rank
FROM sales;

-- Row number for deduplication
SELECT *
FROM (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY client_id ORDER BY created_at DESC) AS rn
    FROM transactions
) t
WHERE rn = 1;

-- Moving average (3-period)
SELECT sale_date, revenue,
    AVG(revenue) OVER (ORDER BY sale_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
FROM daily_sales;
```

| Function | Purpose |
|----------|---------|
| `ROW_NUMBER()` | Unique sequential number per partition |
| `RANK()` | Rank with gaps for ties |
| `DENSE_RANK()` | Rank without gaps |
| `SUM() OVER` | Running/cumulative total |
| `AVG() OVER` | Moving average |
| `LAG() / LEAD()` | Access previous / next row value |

---

## 10. Date & Time Functions

```sql
-- Current date/time
SELECT CURRENT_DATE, CURRENT_TIMESTAMP;

-- Extract parts of a date (useful for dashboard time filters)
SELECT EXTRACT(YEAR FROM sale_date) AS year,
       EXTRACT(MONTH FROM sale_date) AS month
FROM sales;

-- Date arithmetic
SELECT client_name, onboard_date,
    onboard_date + INTERVAL '90 days' AS trial_end
FROM clients;

-- Filter by date range
SELECT * FROM reports
WHERE report_date BETWEEN '2024-01-01' AND '2024-12-31';

-- Truncate to month (group by month in dashboards)
SELECT DATE_TRUNC('month', sale_date) AS month, SUM(revenue)
FROM sales
GROUP BY DATE_TRUNC('month', sale_date)
ORDER BY month;
```

---

## 11. CASE Statements — Conditional Logic

```sql
-- Categorize clients by revenue tier
SELECT client_name, revenue,
    CASE
        WHEN revenue > 100000 THEN 'Enterprise'
        WHEN revenue > 25000  THEN 'Mid-Market'
        ELSE 'SMB'
    END AS client_tier
FROM sales;

-- Flag data quality issues (data validation)
SELECT report_id, submitted_at,
    CASE
        WHEN submitted_at IS NULL THEN 'Missing'
        WHEN submitted_at > CURRENT_DATE THEN 'Future Date Error'
        ELSE 'Valid'
    END AS data_status
FROM reports;
```

---

## 12. Data Validation Queries

> Essential for validating data accuracy and troubleshooting issues on the ciPARTHENON platform.

```sql
-- Check for NULL values in key columns
SELECT COUNT(*) AS null_count
FROM clients
WHERE client_name IS NULL OR region IS NULL;

-- Find duplicate records
SELECT client_id, COUNT(*) AS occurrences
FROM clients
GROUP BY client_id
HAVING COUNT(*) > 1;

-- Check value ranges (outlier detection)
SELECT MIN(revenue), MAX(revenue), AVG(revenue), STDDEV(revenue)
FROM sales;

-- Orphaned foreign keys (referential integrity check)
SELECT s.sale_id FROM sales s
LEFT JOIN clients c ON s.client_id = c.client_id
WHERE c.client_id IS NULL;

-- Row count comparison (validate data loads)
SELECT 'sales' AS table_name, COUNT(*) AS row_count FROM sales
UNION ALL
SELECT 'clients', COUNT(*) FROM clients
UNION ALL
SELECT 'products', COUNT(*) FROM products;
```

---

## 13. String Functions

```sql
-- Concatenate
SELECT first_name || ' ' || last_name AS full_name FROM users;
-- or: CONCAT(first_name, ' ', last_name)

-- Upper / Lower case
SELECT UPPER(client_name), LOWER(email) FROM clients;

-- Trim whitespace (data cleaning)
SELECT TRIM(client_name) FROM clients;

-- Substring
SELECT SUBSTRING(phone, 1, 3) AS area_code FROM contacts;

-- Replace
SELECT REPLACE(description, 'N/A', 'Unknown') FROM products;

-- Length
SELECT client_name, LENGTH(client_name) AS name_length FROM clients;
```

---

## 14. NULL Handling

```sql
-- Replace NULL with a default value
SELECT client_name, COALESCE(revenue, 0) AS revenue FROM sales;

-- NULLIF (avoid division by zero)
SELECT total_revenue / NULLIF(num_clients, 0) AS revenue_per_client
FROM summary;

-- Filter and replace in one shot
SELECT client_name,
    COALESCE(NULLIF(TRIM(region), ''), 'Unknown') AS region
FROM clients;
```

---

## 15. Common Dashboard Query Patterns

```sql
-- KPI Summary (top-level dashboard card)
SELECT
    COUNT(DISTINCT client_id)   AS total_clients,
    SUM(revenue)                AS total_revenue,
    ROUND(AVG(revenue), 2)      AS avg_revenue_per_client,
    MAX(sale_date)              AS last_sale_date
FROM sales
WHERE EXTRACT(YEAR FROM sale_date) = 2024;

-- Month-over-Month Growth
WITH monthly AS (
    SELECT DATE_TRUNC('month', sale_date) AS month,
           SUM(revenue) AS revenue
    FROM sales
    GROUP BY 1
)
SELECT month, revenue,
    LAG(revenue) OVER (ORDER BY month) AS prev_month,
    ROUND(100.0 * (revenue - LAG(revenue) OVER (ORDER BY month))
          / NULLIF(LAG(revenue) OVER (ORDER BY month), 0), 2) AS pct_change
FROM monthly
ORDER BY month;

-- Top N per category (e.g., top 3 clients per region)
SELECT * FROM (
    SELECT client_name, region, revenue,
        RANK() OVER (PARTITION BY region ORDER BY revenue DESC) AS rnk
    FROM sales
) ranked
WHERE rnk <= 3;
```

---

## 16. Quick Reference — Execution Order

Understanding SQL's logical execution order helps debug queries:

```
1. FROM / JOIN      → Identify source tables
2. WHERE            → Filter rows
3. GROUP BY         → Aggregate groups
4. HAVING           → Filter groups
5. SELECT           → Choose columns / compute expressions
6. DISTINCT         → Remove duplicates
7. ORDER BY         → Sort results
8. LIMIT / OFFSET   → Restrict output rows
```

> **Tip:** You cannot use a SELECT alias in a WHERE clause because WHERE runs before SELECT.

---

## 17. Performance Tips for Analytics Queries

```sql
-- Use indexes on filter/join columns
CREATE INDEX idx_sales_client ON sales(client_id);
CREATE INDEX idx_sales_date ON sales(sale_date);

-- Avoid SELECT * in production queries — specify only needed columns
-- Bad:  SELECT * FROM sales
-- Good: SELECT sale_id, client_id, revenue FROM sales

-- Filter early to reduce data scanned
SELECT client_id, SUM(revenue)
FROM sales
WHERE sale_date >= '2024-01-01'   -- filter first
GROUP BY client_id;

-- Use EXPLAIN / EXPLAIN ANALYZE to debug slow queries
EXPLAIN ANALYZE
SELECT * FROM sales WHERE client_id = 101;
```

---

*Prepared for CustomerInsights.AI Product Intern application — aligned with ciPARTHENON analytics, dashboard development, and data validation workflows.*
