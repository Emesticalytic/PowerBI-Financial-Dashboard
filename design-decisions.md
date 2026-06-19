# Design Decisions & Architecture Rationale

## Overview

This document outlines key design decisions made during the Global Financial Dashboard development, including the reasoning and trade-offs considered.

---

## Data Modeling

### Star Schema vs Normalized Design
**Decision:** Star schema with fact and dimension tables  
**Rationale:**
- Optimized for analytical queries (fast aggregations)
- Simplified DAX calculations via single-direction relationships
- Industry standard for BI implementations
- Easier for end-users to understand dimension hierarchies

**Trade-offs:**
- Denormalization increases storage (acceptable for analytics)
- Requires careful relationship management
- Less suitable for transactional updates (not needed here)

---

### Multiple Fact Tables (Reseller + Internet)
**Decision:** Separate FactResellerSales and FactInternetSales tables  
**Rationale:**
- Each has different dimensional context (different customer dimensions)
- Prevents invalid joins (can't mix reseller and internet customers)
- Allows channel-specific measures without complex filtering
- Enables clearer channel performance comparison

**Implementation:**
- Union measures aggregate both channels at calculation layer
- Channel-specific measures filter explicitly
- Avoids "many-to-many" relationship complexity

---

### Date Dimension Design
**Decision:** Explicit calendar dimension rather than implicit dates  
**Rationale:**
- Enables complex time intelligence calculations (YTD, prior year, etc.)
- Provides fiscal calendar support (if needed)
- Allows holiday/weekend flagging
- Central source of date attributes (month names, quarters, etc.)

**Grain:** One row per day

---

## Dimensional Modeling

### Product Hierarchy
**Decision:** Single-level hierarchy (Product → Subcategory → Category)  
**Rationale:**
- Matches AdventureWorks structure
- Sufficient granularity for typical financial analysis
- Avoids excessive hierarchy depth

**Future:** Can be expanded to include Brand, Size ranges if needed

---

### Geography vs Sales Territory
**Decision:** Separate dimensions with relationship link  
**Rationale:**
- DimGeography captures pure location data (cities, states, countries)
- DimSalesTerritory captures organizational structure (sales regions, quotas)
- Allows analysis by both geographic location and sales territory
- Territories may span multiple geographies or regions

---

## Calculation & Aggregation

### DAX Measure Layering
**Decision:** Build measures in layers (simple → derived → complex)  
**Example Structure:**
1. Base measures: [Total Sales], [Total COGS]
2. Derived measures: [Gross Profit] = [Total Sales] - [Total COGS]
3. Complex measures: [YoY Growth %] using multiple base measures

**Benefits:**
- Reusability (lower-layer measures used in multiple places)
- Maintainability (change [Total Sales] once, all dependents update)
- Clarity (easy to trace formula lineage)

---

### SUMX vs SUM Usage
**Decision:** Use SUM for simple aggregations, SUMX only when per-row calculation needed  
**Example:**
```dax
-- CORRECT: Simple sum (no per-row calculation)
[Total Sales] = SUM(FactResellerSales[SalesAmount])

-- CORRECT: Per-row calculation needed
[Freight Cost Impact %] = 
    SUMX(FactResellerSales, 
         DIVIDE([Freight], [SalesAmount], 0))

-- AVOID: Unnecessarily complex
[Total Sales Bad] = 
    SUMX(FactResellerSales, 
         [SalesAmount])  -- No row-specific logic
```

**Rationale:** SUMX iterates rows (slower), unnecessary when aggregating simple columns

---

### Blank Handling in Calculations
**Decision:** Use DIVIDE with 0 default to prevent #DIV/0! errors  
```dax
[Margin %] = DIVIDE([Gross Profit], [Total Sales], 0)
```

**Rationale:**
- Better UX (0% is clearer than #DIV/0! error)
- Allows slicer interaction without breaking
- Contextually reasonable (0 margin = no revenue/zero profit)

---

## Visual Design

### Color Palette
**Decision:** Microsoft Corporate theme  
**Colors:**
- Primary Blue: `#0078D4` (Actions, highlights)
- Dark Gray: `#333333` (Text, borders)
- Light Gray: `#F3F3F3` (Backgrounds)
- Accent Colors: `#107C10` (Positive), `#D13438` (Negative)

**Rationale:**
- Professional, enterprise-appropriate
- Accessible color contrast ratios (WCAG AA)
- Consistent with Microsoft product ecosystem
- Non-distracting for data-focused dashboards

### Page Layout
**Decision:** Horizontal scrolling canvas with consistent sections  
**Structure per Page:**
1. **Top Section:** Key metrics (cards/KPIs)
2. **Middle Section:** Primary visualizations
3. **Bottom Section:** Detailed breakdowns (tables, matrices)

**Rationale:**
- Natural reading flow (top to bottom)
- Separates summary (top) from detail (bottom)
- Allows users to drill from high-level to granular
- Mobile-friendly scrolling (vs. requiring horizontal scroll)

---

### Visualization Selection

#### Revenue Trends: Line Chart
**Why:** Shows temporal patterns, trend clarity, multiple series comparison  
**Alternative Considered:** Bar chart (less suitable for trends over many time periods)

#### Territory Performance: Map Visual
**Why:** Geographic context, intuitive territory comparison  
**Alternative Considered:** Bar chart (less intuitive for regional analysis)

#### Product Performance: Matrix
**Why:** Multi-dimensional (category + subcategory), allows drill-down  
**Alternative Considered:** Table (less hierarchical structure)

#### Summary Metrics: KPI Cards
**Why:** Immediate impact, highlights key business metrics  
**Formatting:** Large font, color-coded for performance (green/red)

---

## Performance Optimization

### Relationship Cardinality
**Decision:** All relationships set to "Many-to-One" (fact to dimension)  
**Direction:** Single (filter flows dimension → fact only)

**Rationale:**
- Prevents accidental many-to-many relationships
- Improves query performance (one direction is faster)
- Simplifies data model for end-users

---

### Column Selection
**Decision:** Only essential attributes included in tables  
**Excluded:** Redundant columns, internal IDs, test data flags

**Benefit:** Smaller file size, faster refresh, cleaner model

---

### Aggregation Tables (Future)
**Potential Addition:** Aggregated table at month/territory level for large datasets  
**Current Status:** Not implemented (AdventureWorks data is small enough)

---

## Data Refresh Strategy

### Incremental Refresh
**Current:** Full refresh on [schedule]  
**Future:** Implement incremental refresh for FactResellerSales and FactInternetSales

**Rationale for future upgrade:**
- AdventureWorks is historical (no incremental demand)
- In production, daily updates require incremental refresh for speed
- Incremental = faster refresh, lower query load

---

### Change Data Capture
**Approach:** Rely on transaction timestamps (OrderDate, ShipDate)  
**Alternative:** Could implement CDC if data warehouse supports

---

## Security & Governance

### Row-Level Security (RLS)
**Current Status:** Not implemented  
**Consideration:** Could implement territory-level RLS where each sales manager sees only their territory

**Implementation (if needed):**
```dax
[TerritoryManager] = USERNAME()

-- RLS Filter:
DimSalesTerritory[Manager] = [TerritoryManager]
```

---

### Data Sensitivity
**Classification:** Internal - Not sensitive  
**Reason:** AdventureWorks is public sample data  
**Production Note:** Real customer/financial data would require encryption, audit logging

---

## Scalability Assumptions

### Current Dataset Size
- Fact tables: [X million] rows
- Refresh time: [X minutes]
- File size: [X MB]

### Scale-up Plan
**If fact tables grow 10x:**
- Consider aggregation tables
- Implement partitioning by date
- Move to Premium capacity with incremental refresh

---

## Known Limitations & Trade-offs

1. **No Real-Time Data:** Dashboard refreshes on schedule, not real-time
   - Trade-off: Simpler architecture vs. immediate visibility
   - Acceptable for: Financial reporting (daily view sufficient)

2. **Single Currency:** All values in USD
   - Trade-off: Simpler measures vs. multi-currency support
   - If expanded: Would require FX dimension + calculated rates

3. **Historical Data Only:** No forecasting or predictive models
   - Future enhancement: Integrate Python/R for trend forecasting

4. **Limited Mobile Optimization:** Designed for desktop viewing
   - Could add mobile-specific page layout in future

---

## Lessons Learned & Recommendations

### What Worked Well
✅ Star schema provided excellent query performance  
✅ Measure layering made DAX maintenance straightforward  
✅ Separate fact tables (reseller/internet) prevented complex relationships  

### What Could Improve
⚠️ Date dimension created early—would benefit from more attributes (week start/end)  
⚠️ No documentation layer in initial build—retroactively documenting  
⚠️ Color formatting in visuals not standardized initially—applied theme late  

### For Future Projects
📝 Define dimension attributes upfront (don't assume)  
📝 Document design decisions as you make them  
📝 Test refresh performance with realistic data volumes early  
📝 Build RLS from the start if multi-user deployment expected  

---

**Document Version:** 1.0  
**Last Updated:** [Date]  
**Review Frequency:** Quarterly or when major changes made
