# Data Sources & Refresh

## Primary Data Source

### AdventureWorks SQL Server Database

**Database Type:** Microsoft SQL Server relational database  
**Data Source:** Sample dataset (publicly available)  
**Location:** [Specify your SQL Server instance]  
**Authentication:** SQL Server Authentication [or other method]  

**Tables Used:**
- FactResellerSales
- FactInternetSales
- DimProduct
- DimProductSubcategory
- DimProductCategory
- DimCustomer
- DimDate (may be generated)
- DimGeography
- DimSalesTerritory

---

## Connection Details

### Power BI Desktop
**Data Source Type:** SQL Server  
**Server:** [Your server address]  
**Database:** AdventureWorks  
**Import Mode:** [Direct Query / Import]  
**Connection String:** [If using Advanced Options]

### Power BI Service
**Gateway:** [On-premises gateway / Cloud service]  
**Refresh Type:** [Scheduled / Manual]  
**Credentials:** [Service account / User credentials]

---

## Refresh Schedule

### Current Configuration
**Frequency:** [Daily / Weekly / Monthly]  
**Time:** [e.g., 6:00 AM EST]  
**Duration:** [e.g., 15-20 minutes]  
**Last Successful Refresh:** [Date/Time]

### Refresh History
| Date | Status | Duration | Notes |
|------|--------|----------|-------|
| [Date] | Success | [X min] | [Any notes] |
| [Date] | Success | [X min] | |

---

## Data Completeness

### Date Range
**Available Data:** [e.g., January 2010 - December 2014]  
**Last Updated:** [Latest transaction date in system]  
**Note:** Historical data; AdventureWorks does not receive new transactions

### Record Counts (as of last refresh)
| Table | Row Count | Last Updated |
|-------|-----------|--------------|
| FactResellerSales | [X] | [Date] |
| FactInternetSales | [Y] | [Date] |
| DimCustomer | [Z] | [Date] |
| DimProduct | [N] | [Date] |
| DimGeography | [M] | [Date] |
| DimSalesTerritory | [K] | [Date] |

---

## Data Quality & Validation

### Known Data Issues
- [List any known issues or limitations]
- Null values in [Column]: [Frequency/Reason]
- Duplicate handling: [How duplicates are managed, if applicable]

### Reconciliation Points
**Internet Sales:** Reconcile [FactInternetSales] total with source system [monthly/quarterly]  
**Reseller Sales:** Reconcile [FactResellerSales] total with source system [monthly/quarterly]  
**Last Reconciliation:** [Date] - [Status: OK / Issues found]

### Data Validation Checklist
- [ ] Transaction counts match source system
- [ ] Revenue totals reconcile
- [ ] No unexpected nulls in key fields
- [ ] Date ranges are complete (no gaps)
- [ ] Foreign key relationships valid (no orphaned records)
- [ ] Currency consistency (all USD)

---

## Security & Access

### Database Permissions
**Service Account:** [Account name]  
**Permissions:** SELECT on tables listed above  
**Encryption:** [Yes/No] - [TLS / SSL details if encrypted]

### Credentials in Power BI
**Stored:** Power BI Service gateway credentials  
**Managed By:** [Person/team responsible for credential rotation]  
**Rotation Schedule:** [Annually / Every 6 months]

### Data Classification
**Sensitivity Level:** Internal (public sample data)  
**Restrictions:** None (AdventureWorks is sample/training data)

---

## Refresh Troubleshooting

### Common Issues & Solutions

**Issue: Refresh timeout**
- Solution: Optimize queries or increase timeout setting
- Contact: [DBA / Support]

**Issue: Credentials invalid**
- Solution: Update stored credentials in Power BI gateway
- Process: [Link to credential update procedure]

**Issue: Missing data**
- Solution: Check if source tables were updated
- Verification: Run reconciliation query [Link to query]

---

## Refresh Logs

### Recent Refresh Details
```
Last Refresh: [Date] [Time]
Status: ✅ Success / ⚠️ Partial / ❌ Failed
Duration: [Minutes]
Records Processed: [Fact table counts]
Errors: [None / List errors]
```

---

## Alternative Data Sources (if applicable)

### Testing/Development Environment
**Source:** [Dev database]  
**Purpose:** Testing new calculations before production deployment  
**Refresh Frequency:** [Same as production / Different]  
**Note:** Contains sample data only

---

## Future Data Enhancements

### Planned Additions
- [ ] Add real-time data source (currently historical only)
- [ ] Integrate additional fact tables (e.g., Marketing campaigns)
- [ ] Enhance geography data with postal code details
- [ ] Add currency exchange rates for multi-currency analysis

### Data Source Migration
**Timeline:** [If planned]  
**New Source:** [Details of migration]  
**Testing Plan:** [How new source will be validated]

---

## Contact & Support

**Data Source Owner:** [Name/Team]  
**Power BI Owner:** [Name]  
**DBA Contact:** [Name/Email]  

**For Issues:**
1. Check this document for troubleshooting steps
2. Review Power BI Service refresh status
3. Contact [DBA] for database-level issues
4. Contact [BI Owner] for Power BI configuration issues

---

**Document Version:** 1.0  
**Last Updated:** [Date]  
**Next Review:** [Date]
