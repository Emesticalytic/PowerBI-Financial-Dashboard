# Repository Structure & Setup Guide

## Directory Organization

```
PowerBI-Financial-Dashboard/
│
├── README.md                          # Main project overview (START HERE)
├── .gitignore                         # Git ignore rules (excludes .pbix)
├── LICENSE                            # Project license (MIT recommended)
│
├── documentation/                     # Technical documentation
│   ├── data-model.md                 # Fact/dimension tables, relationships
│   ├── measures.md                   # DAX measures & calculations
│   ├── design-decisions.md           # Architecture & design rationale
│   └── data-sources.md               # Refresh schedule, data source details
│
├── queries/                           # Code & scripts
│   ├── power-query-reference.md      # M code patterns & transformations
│   └── sql-validation-queries.sql    # SQL queries for data validation
│
├── screenshots/                       # Visual references
│   ├── page1-executive-overview.png
│   ├── page2-sales-performance.png
│   ├── page3-territory-analysis.png
│   └── README.md                     # Notes on each screenshot
│
├── artifacts/                         # Optional: additional reference files
│   ├── data-dictionary.xlsx          # Column descriptions (optional)
│   └── refresh-history.csv           # Historical refresh logs
│
└── .github/                           # GitHub metadata (optional)
    └── CONTRIBUTING.md               # Contribution guidelines
```

---

## File Descriptions

### Root Level

**README.md** (3-5 min read)
- Executive summary of the dashboard
- Link to documentation
- How to access/use
- Key insights

**LICENSE**
```
MIT License - allows others to use with attribution
```

**.gitignore**
- Excludes .pbix (binary files)
- Excludes OS & IDE temp files
- Excludes sensitive configs

---

### /documentation/ Folder

**data-model.md** (reference)
- Fact table definitions (FactResellerSales, FactInternetSales)
- Dimension table definitions (Product, Customer, Geography, etc.)
- Relationship map
- Data types, cardinality, grain

**measures.md** (reference)
- DAX formulas for all 15+ measures
- Measure layering approach
- Performance notes

**design-decisions.md** (explanation)
- Why star schema chosen
- Why separate fact tables
- Color palette rationale
- Visualization selection reasoning
- Performance optimization decisions

**data-sources.md** (operational)
- Connection details
- Refresh schedule
- Data quality checks
- Reconciliation process
- Troubleshooting guide

---

### /queries/ Folder

**power-query-reference.md**
- M code patterns (connection, transformations, error handling)
- FactResellerSales pipeline
- DimDate creation
- Common transformation patterns
- Incremental refresh pattern

**sql-validation-queries.sql** (optional)
- Queries to validate data integrity
- Row count checks
- Revenue reconciliation
- Null value audit

---

### /screenshots/ Folder

Store high-quality PNG/JPG screenshots of each dashboard page:

**page1-executive-overview.png**
- Visual of KPI cards, trend lines
- Shows color scheme, layout

**page2-sales-performance.png**
- Revenue by channel, territory breakdown
- Illustrates visualization types used

**page3-territory-analysis.png**
- Territory map, regional performance
- Shows geographic visualization

**README.md** (in this folder)
```markdown
# Dashboard Screenshots

## Page 1: Executive Overview
Screenshot showing high-level KPIs and trends.
Key metrics: Total Revenue, YoY Growth, Profit Margin %

## Page 2: Sales Performance
Detailed sales breakdown by channel and product.
Demonstrates: Line charts, bar charts, matrix visualization

## Page 3: Territory Analysis
Geographic view of sales by territory.
Demonstrates: Map visual, regional comparison
```

---

### /artifacts/ Folder (Optional)

**data-dictionary.xlsx**
- Spreadsheet with all columns, data types, descriptions
- Business glossary of terms

**refresh-history.csv**
- Log of refresh times, success/failure
- Useful for performance monitoring

---

## Getting Started: Setup Instructions

### For Portfolio Viewers

**To Review Documentation:**
1. Start with README.md
2. Review screenshots/ folder for visual overview
3. Dive into documentation/ for technical details
4. Reference power-query-reference.md for transformation logic

**To Use the Dashboard (Local):**
1. Clone repository: `git clone https://github.com/Emesticalytic/PowerBI-Financial-Dashboard.git`
2. Download Global_Financial_Dashboard.pbix (if included in releases)
3. Open in Power BI Desktop
4. Update data source connection if needed
5. Refresh data
6. Explore pages and visuals

---

### For Collaborators

**To Contribute Documentation:**
1. Fork repository
2. Create branch: `git checkout -b feature/improve-docs`
3. Edit .md files with improvements
4. Submit pull request with clear description
5. Await review & merge

**To Report Issues:**
1. Check existing issues first
2. Create new issue with title, description, expected vs. actual
3. Include screenshots or specific measure name if applicable

---

## Content Guidelines

### Documentation Standards

**Markdown Formatting:**
- H1 (#) for main title
- H2 (##) for sections
- H3 (###) for subsections
- Use code blocks for formulas: ` ```dax DAX_FORMULA ``` `
- Use tables for reference data
- Links: [Text](URL)

**DAX Code Block Example:**
```dax
[Total Sales] = 
SUMX(
    FactResellerSales,
    [SalesAmount]
)
```

**SQL Code Block Example:**
```sql
SELECT 
    Territory,
    SUM(SalesAmount) AS TotalSales
FROM FactResellerSales
GROUP BY Territory
```

**File Naming Conventions:**
- Lowercase with hyphens: `power-query-reference.md`
- Descriptive: `data-model.md` not `reference.md`
- Screenshots: `page1-executive-overview.png` not `page1.png`

---

## Maintenance Checklist

### When Adding New Pages to Dashboard:

- [ ] Create screenshot and save to `/screenshots/`
- [ ] Update README.md "Dashboard Pages" section
- [ ] Document new measures in measures.md
- [ ] Document new filters/interactions in design-decisions.md
- [ ] Update data-sources.md if new data added
- [ ] Commit with clear message: "Add Page 4: Customer Segmentation"

### When Updating Measures:

- [ ] Update measures.md with new DAX
- [ ] Note any breaking changes
- [ ] Update design-decisions.md if logic fundamentally changes
- [ ] Commit with: "Update: [Measure Name] DAX logic"

### When Changing Data Model:

- [ ] Update data-model.md with new tables/columns
- [ ] Update design-decisions.md rationale
- [ ] Update power-query-reference.md if transformations change
- [ ] Create pull request for review before merging

### Quarterly Review:

- [ ] Validate refresh is running successfully
- [ ] Check data-sources.md for accuracy
- [ ] Review open issues/PRs
- [ ] Update "Last Updated" dates in documents

---

## Version Control Best Practices

### Commit Message Format

```
git commit -m "Add: Territory Analysis page documentation"
git commit -m "Update: DAX measures for improved performance"
git commit -m "Fix: Typo in data-model.md"
```

### Branching Strategy (if collaborative)

- `main` - Production/final version
- `develop` - Development branch
- `feature/[name]` - New feature
- `docs/[name]` - Documentation updates

Example:
```bash
git checkout -b feature/add-forecasting-measures
# ... make changes ...
git push origin feature/add-forecasting-measures
# Create pull request on GitHub
```

---

## README.md Customization Checklist

Before publishing, fill in these sections:

- [ ] Project title (Current: "Global Financial Dashboard")
- [ ] Dataset name (Current: "AdventureWorks")
- [ ] Color theme description
- [ ] Data refresh frequency
- [ ] Your portfolio/contact link
- [ ] Technology stack
- [ ] Status (In Development / Complete)
- [ ] License type

---

## Optional Enhancements

### GitHub Features to Consider

1. **Releases**
   - Create release with .pbix file attachment
   - Version numbering: v1.0, v1.1, v2.0

2. **Issues Template**
   - Bug report template
   - Feature request template
   - Documentation improvement template

3. **GitHub Pages**
   - Convert README to static site
   - Showcase dashboard visually
   - Add live links to screenshots

4. **Wiki**
   - Detailed tutorials
   - FAQ section
   - Deployment guide

5. **Discussions**
   - Q&A about methodology
   - Share insights
   - Collaborate with community

---

## Example: Pull Request Template (.github/PULL_REQUEST_TEMPLATE.md)

```markdown
## What does this PR do?
Brief description of changes

## Related Issues
Closes #[issue number]

## Changes Made
- [] Updated documentation
- [ ] Modified DAX measures
- [ ] Added/updated screenshots
- [ ] Updated data model

## Testing
How have you tested these changes?

## Screenshots (if applicable)
Before/After screenshots

## Checklist
- [ ] README updated if needed
- [ ] Documentation is clear
- [ ] No .pbix files added
```

---

## Repository Growth Plan

**Phase 1 (Current):** Core documentation complete
**Phase 2 (Month 2):** Add validation scripts, refresh logs
**Phase 3 (Month 3):** Create companion blog post / case study
**Phase 4 (Month 4):** Add video walkthrough links
**Phase 5 (Ongoing):** Maintenance, issue responses, versioning

---

## Troubleshooting Repository Issues

**Issue: Large file won't commit**
```bash
# Check if .pbix in .gitignore
cat .gitignore | grep pbix

# Remove from cache if accidentally added
git rm --cached Global_Financial_Dashboard.pbix
git commit -m "Remove .pbix from version control"
```

**Issue: Want to add binary .pbix file safely**
```bash
# Use GitHub Releases instead of committing to repo
# Upload via GitHub UI under "Releases" tab
```

---

## Summary

This structure provides:
✅ Clear organization (documentation, queries, screenshots)  
✅ Professional presentation for portfolio  
✅ Easy onboarding for viewers/collaborators  
✅ Maintainability as dashboard evolves  
✅ Git best practices compliance  

---

**Template Version:** 1.0  
**Created:** [Date]  
**Customized For:** Global Financial Dashboard
