# ABC Bank Global Financial Dashboard

A sophisticated enterprise-grade Power BI financial analytics dashboard built. This multi-page intelligence platform demonstrates advanced data modeling, DAX optimisation, AI-powered insights, and professional financial visualization for global business performance monitoring.

## Overview

The ABC Bank Global Financial Dashboard provides comprehensive visibility into financial metrics, market intelligence, and AI-driven insights across six interactive pages. Built with enterprise color themes and professional design patterns, this dashboard serves as a strategic hub for executive decision-making, sales performance tracking, and global market analysis.

**Dataset:**   
**Tools:** Power BI Desktop | DAX | Power Query M  
**Theme:** Multi-page design with region-specific color palettes  
**Status:** Production Ready (All 6 Pages Complete)

---

## Dashboard Pages

### Page 1: Sales Performance
Revenue, profitability, and market growth analysis across regions:
- Actual vs. targeted revenue by region
- Top countries by profit performance
- Revenue breakdown by region with variance tracking
- 4-year revenue trend (2020-2024)
- Region, product, and territory filtering

### Page 2: Executive Overview
High-level regional and customer performance summary:
- Region-level KPI table (Revenue, Profit, Margin %, Orders)
- Top 10 customers by revenue
- Total orders by customer segment breakdown
- Order volume by sales channel (Direct, Partner, Online, Reseller, Inbound)
- Total revenue by product category
- Strategic business questions for stakeholder guidance

### Page 3: Financial Analysis
Deep-dive into cost structures, budgeting, and profitability:
- Profit targets, cost budgets, and variance analysis
- Margin targets and monthly recurring revenue (MRR)
- P&L revenue breakdown (Increase/Decrease/Total) by category
- Total cost analysis by product category
- Monthly budget vs. actual revenue tracking
- Key financial insights and performance commentary

### Page 4: Global Map
Geographic market intelligence and territorial analysis:
- Interactive world map showing revenue by country
- Top 5 countries by revenue (ranked)
- Market tier segmentation (Tier 1: Core, Tier 2: Growth, Tier 3: Emerging)
- Revenue opportunity scoring by country
- Year-over-year growth metrics
- Global market distribution insights

### Page 5: AI Insights
Advanced analytics and influential factor analysis:
- Key influencers of total revenue (using correlation analysis)
- Interactive revenue breakdown by Category → Region → Country → Channel → Segment
- AI-powered what-if analysis ("When... then..." scenario modeling)
- Revenue drivers identified through machine learning patterns
- Segment, region, and category drilldown capabilities
- AI-generated business insights summary

### Page 6: Forecast
Revenue forecasting and growth projection analysis:
- Year-to-date (YTD) revenue tracking
- Year-over-year growth percentage and trends
- Annual revenue targets and budget variance analysis
- YoY growth by year (2021-2024 historical with narrative)
- Revenue forecast model (2020-2024 actuals, 2025-2026 projected)
- Growth scenarios and confidence intervals
- Key forecast insights and strategic implications

---

## Technical Architecture

### Data Model
- **Fact Tables:** FactResellerSales, FactInternetSales
- **Dimension Tables:** DimProduct, DimCustomer, DimDate, DimGeography, DimSalesTerritory
- **Relationships:** Star schema with optimized cardinality
- **Data Types:** Properly typed for DAX performance

### Key Measures (DAX)
See [measures.md](documentation/measures.md) for complete DAX documentation, including:
- Total Sales
- Gross Profit
- Profit Margin %
- YoY Growth
- Running Totals
- Territory Benchmarks

### Power Query Transformations
Data preparation and transformation logic documented in [power-query.txt](queries/power-query.txt):
- Data cleaning and validation
- Calendar table generation
- Surrogate key creation
- Incremental refresh patterns

---

## How to Use

### Option 1: Download & Open Locally
1. Clone this repository
2. Download `Global_Financial_Dashboard.pbix` from the releases or main directory
3. Open in Power BI Desktop
4. Update data source connections if needed (currently configured for AdventureWorks SQL Server)
5. Refresh data and explore

### Option 2: Review Documentation Only
All technical specifications, DAX logic, and design decisions are documented in the `/documentation` folder. Start with:
- [Data Model Overview](documentation/data-model.md)
- [Measures & Calculations](documentation/measures.md)
- [Design Decisions](documentation/design-decisions.md)

---

## Project Highlights

 **Multi-Page Enterprise Dashboard** — 6 fully-featured pages with distinct analytical purposes  
 **Advanced DAX Measures** — YTD calculations, YoY comparisons, running totals, variance analysis  
 **AI-Powered Insights** — Correlation analysis, key influencer identification, what-if scenarios  
 **Geographic Intelligence** — Interactive world map with revenue distribution and market tier analysis  
 **Predictive Analytics** — Time-series forecasting with confidence intervals and growth projections  
 **Professional Design** — Multi-theme color palettes, responsive layouts, executive-grade visuals  
**Interactive Drill-down** — Hierarchical navigation (Category → Region → Country → Channel → Segment)  
**Comprehensive Documentation** — Technical specifications, design decisions, and operational guidelines  

---

## File Structure

```
PowerBI-Financial-Dashboard/
├── README.md
├── Global_Financial_Dashboard.pbix (optional - add to .gitignore)
├── documentation/
│   ├── data-model.md
│   ├── measures.md
│   ├── design-decisions.md
│   └── data-sources.md
├── queries/
│   └── power-query-m-code.txt
├── screenshots/
│   ├── page1-executive-overview.png
│   ├── page2-sales-performance.png
│   └── page3-territory-analysis.png
└── .gitignore
```

---

## Key Insights & Findings

- **Strong Revenue Growth**: ABC Bank revenue has grown consistently from £1M monthly average in 2020 to £6M in 2024, representing **38.86% YoY growth**
- **Profitability Leadership**: Gross margin maintained at **66.58%** against 55% industry benchmark, demonstrating strong operational efficiency
- **Target Setting Challenge**: Revenue consistently missed monthly targets by ~13.9%, suggesting targets were set too ambitiously
- **Regional Concentration Risk**: North America dominates with £74M (35% of revenue), while Africa represents only £9M, indicating geographic diversification opportunity
- **Channel Effectiveness**: Direct Sales and Partner channels consistently outperform Online and Reseller channels across segments
- **Enterprise Segment Strength**: Enterprise customers deliver highest value per order despite lower volumes; SMB segment shows healthy volume with consistent performance
- **Product Category Distribution**: Software drives highest revenue (£136M, 63%) and margin; Professional Services shows highest cost ratio (60%) indicating service delivery overhead
- **Market Tier Opportunity**: Tier 1 (Core) markets represent 54.98% of revenue; growth potential exists in underexploited Tier 2 and Tier 3 markets
- **Forecast Confidence**: 2025-2026 projections show continued growth trajectory with expected revenue reaching £7M-£8M monthly average

---

## Future Enhancements

- [ ] Add predictive forecasting module using R/Python integration
- [ ] Implement drill-through to detailed transaction-level analysis
- [ ] Extend territory analysis with market share benchmarking
- [ ] Create mobile-optimized views for executive mobile access
- [ ] Add anomaly detection for KPI alerts

---

## Technologies & Skills Demonstrated

- **BI Tools:** Power BI Desktop, Power BI Service
- **Data Transformation:** Power Query, M language
- **Analytics & Calculations:** DAX (Data Analysis Expressions)
- **Data Modeling:** Dimensional modeling, star schema design
- **Database:** SQL Server (AdventureWorks)
- **Design:** UX/UI principles for analytics, accessible color palettes
- **Version Control:** Git, GitHub

---

## Notes for Reviewers

This project demonstrates:
1. **Data Modeling Expertise** — Complex multi-table relationships and optimized grain design
2. **DAX Proficiency** — Advanced calculations including context transitions and time intelligence
3. **Analytical Thinking** — KPI selection, metric hierarchy, and business-relevant insights
4. **Design Discipline** — Professional aesthetics, consistent formatting, and user-centric layout
5. **Documentation Standards** — Clear technical specifications suitable for enterprise hand-off

-Fictional Company & Anonymized Data

ABC Bank is a fictional company created for portfolio and educational demonstration purposes.

This dashboard and all associated documentation do not represent any real, existing organization, company, or financial institution.

Data Anonymization


All company names, customer names, and financial figures are anonymized and fictitious
Any resemblance to real companies, organizations, or actual financial data is purely coincidental
The data model, metrics, and scenarios are created for demonstration purposes only


Portfolio Purpose

This project is created to showcase:


Advanced Power BI skills
Data modeling expertise
DAX calculation capabilities
Business analytics thinking
Professional documentation standards


Usage Rights

This documentation and dashboard design are provided as a portfolio example and may be used as reference material for learning purposes.


Project Status: Portfolio Demonstration | Data: Anonymized/Fictional | Date: June 2026
## Contact

For questions or collaboration, reach out via [GitHub Profile](https://github.com/Emesticalytic) or [emem-akpan-analytics.netlify.app]

---

**Last Updated:** June 2026]  
**Status:** In Active Development
