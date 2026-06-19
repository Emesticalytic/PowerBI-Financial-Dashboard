# DAX Measures & Calculations

This document outlines all DAX measures used in the Global Financial Dashboard, including formulas, logic, and usage context.

---

## Revenue Measures

### Total Sales / Total Revenue
**Measure Name:** `[Total Sales]` or `[Total Revenue]`  
**Purpose:** Aggregate total revenue across all sales channels and regions  
**Calculation Type:** Sum of currency values  
**Current Value:** £214.57M

```dax
[Total Sales] = 
SUMX(
    UNION(
        FactResellerSales,
        FactInternetSales
    ),
    [SalesAmount]
)
```

**Usage:** All pages, primary KPI metric, filters dynamically by page context  
**Context Notes:** Combines both reseller and internet sales channels; responds to region, product, customer, and date filters

### Year-to-Date (YTD) Revenue
**Measure Name:** `[Sales YTD]`  
**Purpose:** Cumulative revenue from calendar year start to current date  
**Calculation Type:** Time intelligence function  
**Current Value:** £60.04M

```dax
[Sales YTD] = 
TOTALYTD(
    [Total Sales],
    DimDate[FullDate]
)
```

**Usage:** Forecast page, performance tracking, progress toward annual targets  
**Related Measures:** [Sales MTD], [Sales QTD]

---

### Sales - Internet Only
**Measure Name:** `[Sales Internet]`  
**Purpose:** Filter revenue to internet channel only  
**Calculation Type:** Filtered sum

```dax
[Sales Internet] = 
SUMX(
    FactInternetSales,
    [SalesAmount]
)
```

**Usage:** Channel-level performance comparison  
**Related Measure:** [Sales Reseller]

---

### Sales - Reseller Only
**Measure Name:** `[Sales Reseller]`  
**Purpose:** Filter revenue to reseller channel only  
**Calculation Type:** Filtered sum

```dax
[Sales Reseller] = 
SUMX(
    FactResellerSales,
    [SalesAmount]
)
```

**Usage:** Channel-level performance comparison  
**Related Measure:** [Sales Internet]

---

## Cost & Profitability Measures

### Total Cost of Goods Sold (COGS)
**Measure Name:** `[Total COGS]`  
**Purpose:** Aggregate cost of goods for all sales  
**Calculation Type:** Weighted cost sum

```dax
[Total COGS] = 
SUMX(
    UNION(
        FactResellerSales,
        FactInternetSales
    ),
    [TotalProductCost]
)
```

**Usage:** Profitability analysis, margin calculations  
**Note:** Uses actual product cost, not standard cost

---

### Gross Profit
**Measure Name:** `[Gross Profit]`  
**Purpose:** Revenue minus direct product costs  
**Calculation Type:** Derived calculation

```dax
[Gross Profit] = 
[Total Sales] - [Total COGS]
```

**Usage:** Profitability dashboards, trend analysis  
**Interpretation:** Higher value indicates better product margin

---

### Gross Profit Margin %
**Measure Name:** `[Gross Profit Margin %]`  
**Purpose:** Gross profit as percentage of revenue  
**Calculation Type:** Ratio with division handling

```dax
[Gross Profit Margin %] = 
DIVIDE(
    [Gross Profit],
    [Total Sales],
    0
)
```

**Formatting:** Percentage (0.0%)  
**Interpretation:** Target margin varies by product category  
**Edge Case Handling:** Returns 0 if no sales (instead of #DIV/0!)

---

### Operating Margin %
**Measure Name:** `[Operating Margin %]`  
**Purpose:** Net profit as percentage of revenue  
**Calculation Type:** Advanced ratio

```dax
[Operating Margin %] = 
DIVIDE(
    [Gross Profit] - [Total Operating Expenses],
    [Total Sales],
    0
)
```

**Note:** Requires [Total Operating Expenses] measure (define separately)  
**Usage:** Executive-level profitability KPI

---

## Time Intelligence Measures

### Year-to-Date Sales
**Measure Name:** `[Sales YTD]`  
**Purpose:** Cumulative sales from start of fiscal year to current date  
**Calculation Type:** Cumulative time intelligence

```dax
[Sales YTD] = 
TOTALYTD(
    [Total Sales],
    DimDate[FullDate],
    "FY_END_DATE"
)
```

**Assumption:** Calendar year ending Dec 31 (modify FY_END_DATE if fiscal year differs)  
**Usage:** Year-to-date performance tracking  
**Related Measures:** [Sales MTD], [Sales QTD]

---

### Prior Year Sales
**Measure Name:** `[Sales Prior Year]`  
**Purpose:** Sales from same period in prior year  
**Calculation Type:** Time offset calculation

```dax
[Sales Prior Year] = 
CALCULATE(
    [Total Sales],
    DATEADD(
        DimDate[FullDate],
        -1,
        YEAR
    )
)
```

**Usage:** Year-over-year comparison  
**Edge Case:** Returns blank if prior year data unavailable

---

### Year-over-Year Growth %
**Measure Name:** `[YoY Growth %]`  
**Purpose:** Percentage change from prior year period  
**Calculation Type:** Variance calculation

```dax
[YoY Growth %] = 
DIVIDE(
    [Total Sales] - [Sales Prior Year],
    [Sales Prior Year],
    0
)
```

**Formatting:** Percentage (0.0%)  
**Interpretation:** Positive = growth, Negative = decline  
**Usage:** Growth trend analysis, performance monitoring

---

### Month-to-Date Sales
**Measure Name:** `[Sales MTD]`  
**Purpose:** Cumulative sales from start of month to current date  
**Calculation Type:** Cumulative month intelligence

```dax
[Sales MTD] = 
TOTALMTD(
    [Total Sales],
    DimDate[FullDate]
)
```

**Usage:** Intra-month performance monitoring  
**Related Measures:** [Sales QTD], [Sales YTD]

---

## Quantity Measures

### Total Units Sold
**Measure Name:** `[Total Quantity]`  
**Purpose:** Aggregate count of units sold  
**Calculation Type:** Sum of integers

```dax
[Total Quantity] = 
SUMX(
    UNION(
        FactResellerSales,
        FactInternetSales
    ),
    [OrderQuantity]
)
```

**Usage:** Volume analysis, unit economics  
**Note:** Not the same as transaction count

---

### Average Selling Price
**Measure Name:** `[Avg Selling Price]`  
**Purpose:** Average price per unit sold  
**Calculation Type:** Ratio calculation

```dax
[Avg Selling Price] = 
DIVIDE(
    [Total Sales],
    [Total Quantity],
    0
)
```

**Formatting:** Currency  
**Usage:** Pricing analysis, product mix insights  
**Interpretation:** Changes indicate product mix shift or pricing changes

---

## Performance Ratio Measures

### Revenue per Transaction
**Measure Name:** `[Revenue per Transaction]`  
**Purpose:** Average order value  
**Calculation Type:** Aggregate ratio

```dax
[Revenue per Transaction] = 
DIVIDE(
    [Total Sales],
    COUNTROWS(
        UNION(
            FactResellerSales,
            FactInternetSales
        )
    ),
    0
)
```

**Formatting:** Currency  
**Usage:** AOV analysis, order size trends

---

### Transaction Count
**Measure Name:** `[Transaction Count]`  
**Purpose:** Number of sales transactions  
**Calculation Type:** Row count

```dax
[Transaction Count] = 
COUNTA(
    UNION(
        FactResellerSales,
        FactInternetSales
    )
)
```

**Usage:** Transaction volume tracking, frequency analysis

---

## Territory Benchmarking Measures

### Territory Sales Rank
**Measure Name:** `[Territory Sales Rank]`  
**Purpose:** Rank territories by sales volume  
**Calculation Type:** Ranking function

```dax
[Territory Sales Rank] = 
RANKX(
    ALL(DimSalesTerritory[SalesTerritoryRegion]),
    [Total Sales],
    ,
    DESC
)
```

**Usage:** Territory comparison, competitive positioning  
**Interpretation:** 1 = Top performing territory

---

### Territory vs Company Average
**Measure Name:** `[Territory vs Avg]`  
**Purpose:** Territory performance vs company average  
**Calculation Type:** Indexed comparison

```dax
[Territory vs Avg] = 
DIVIDE(
    [Total Sales],
    CALCULATE(
        [Total Sales],
        ALL(DimSalesTerritory)
    ),
    0
)
```

**Formatting:** Percentage  
**Interpretation:** >1.0 = above average, <1.0 = below average

---

## Context & Filters

All measures above respond to standard Power BI filters:
- **Date Filters:** OrderDate (via DateKey relationship)
- **Product Filters:** Product Name, Category, Subcategory
- **Territory Filters:** Region, Country, Territory
- **Customer Filters:** Customer type, geography

---

## Performance Optimization Notes

- **Measure Dependencies:** Measures are layered (e.g., Gross Profit uses [Total Sales])
- **Aggregation:** Use SUM directly when possible, SUMX only when calculation required per row
- **Context Transition:** CALCULATE used explicitly to avoid implicit context transitions
- **Blank Handling:** DIVIDE function with 0 default prevents #DIV/0! errors

---

## Testing & Validation

Each measure should be validated against:
- [ ] Source system totals (SQL validation)
- [ ] Manual spot-check calculations
- [ ] Trend reasonableness (no unexplained spikes)
- [ ] Channel reconciliation (Internet + Reseller = Total)

---

**Created:** [Date]  
**Last Updated:** [Date]  
**Validated By:** [Name/Date]
