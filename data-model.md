# Data Model Documentation

## Overview

The Global Financial Dashboard uses a **star schema** design with a central fact table surrounded by dimension tables. This architecture optimizes query performance and enables efficient DAX calculations.

---

## Fact Tables

### FactResellerSales
**Purpose:** Records all reseller (B2B) sales transactions  
**Grain:** One row per sales line item  
**Row Count:** [Insert actual count]  
**Updated:** [Daily / As needed]

| Column | Data Type | Description |
|--------|-----------|-------------|
| SalesOrderNumber | Text | Unique sales order identifier |
| SalesOrderLineNumber | Integer | Line item number within order |
| ResellerKey | Integer | FK to DimReseller |
| ProductKey | Integer | FK to DimProduct |
| OrderDateKey | Integer | FK to DimDate (order date) |
| DueDateKey | Integer | FK to DimDate (due date) |
| ShipDateKey | Integer | FK to DimDate (ship date) |
| SalesAmount | Currency | Total sale amount (revenue) |
| OrderQuantity | Integer | Units sold |
| UnitPrice | Currency | Price per unit |
| ExtendedAmount | Currency | Quantity × UnitPrice |
| ProductStandardCost | Currency | Cost of goods sold |
| TotalProductCost | Currency | Total COGS |
| SalesAmountQuota | Currency | Sales target/quota |
| Freight | Currency | Shipping cost |
| CarrierTrackingNumber | Text | Logistics tracking ID |
| CustomerPONumber | Text | Customer PO reference |

**Key Relationships:**
- Links to DimProduct via ProductKey
- Links to DimDate via OrderDateKey, DueDateKey, ShipDateKey
- Links to DimReseller (via ResellerKey if available)

---

### FactInternetSales
**Purpose:** Records all direct internet (B2C) sales transactions  
**Grain:** One row per sales line item  
**Row Count:** [Insert actual count]  
**Updated:** [Daily / As needed]

| Column | Data Type | Description |
|--------|-----------|-------------|
| SalesOrderNumber | Text | Unique sales order identifier |
| SalesOrderLineNumber | Integer | Line item number within order |
| CustomerKey | Integer | FK to DimCustomer |
| ProductKey | Integer | FK to DimProduct |
| OrderDateKey | Integer | FK to DimDate (order date) |
| DueDateKey | Integer | FK to DimDate (due date) |
| ShipDateKey | Integer | FK to DimDate (ship date) |
| SalesAmount | Currency | Total sale amount (revenue) |
| OrderQuantity | Integer | Units sold |
| UnitPrice | Currency | Price per unit |
| ExtendedAmount | Currency | Quantity × UnitPrice |
| ProductStandardCost | Currency | Cost of goods sold |
| TotalProductCost | Currency | Total COGS |
| SalesAmountQuota | Currency | Sales target/quota |
| Freight | Currency | Shipping cost |
| CarrierTrackingNumber | Text | Logistics tracking ID |
| CustomerPONumber | Text | Customer PO reference |
| RevisionNumber | Integer | Order revision count |
| ShipToAddressID | Integer | Shipping address reference |

**Key Relationships:**
- Links to DimCustomer via CustomerKey
- Links to DimProduct via ProductKey
- Links to DimDate via OrderDateKey, DueDateKey, ShipDateKey

---

## Dimension Tables

### DimDate
**Purpose:** Calendar dimension for time-based analysis  
**Grain:** One row per calendar day  
**Date Range:** [Start Year] to [End Year]

| Column | Data Type | Key Attributes |
|--------|-----------|---|
| DateKey | Integer | PK (YYYYMMDD format) |
| FullDate | Date | Calendar date |
| DayNumberOfWeek | Integer | 1-7 (Monday-Sunday) |
| DayNameOfWeek | Text | Monday, Tuesday, ... |
| DayNumberOfMonth | Integer | 1-31 |
| DayNumberOfYear | Integer | 1-365 |
| WeekNumberOfYear | Integer | 1-53 |
| MonthNumberOfYear | Integer | 1-12 |
| MonthName | Text | January, February, ... |
| CalendarQuarter | Integer | 1-4 |
| CalendarYear | Integer | 2010-2014 |
| CalendarSemester | Integer | 1-2 |
| FiscalQuarter | Integer | 1-4 (if different from calendar) |
| FiscalYear | Integer | Fiscal year assignment |
| WeekendFlag | Boolean | Weekend indicator |
| IsHoliday | Boolean | Holiday flag (if applicable) |

**Usage:** All date-based filtering and time intelligence DAX calculations

---

### DimProduct
**Purpose:** Product master dimension  
**Grain:** One row per product SKU  
**Row Count:** [Insert actual count]

| Column | Data Type | Key Attributes |
|--------|-----------|---|
| ProductKey | Integer | PK |
| ProductAlternateKey | Text | Product SKU |
| ProductName | Text | Product description |
| ProductSubcategoryKey | Integer | FK to DimProductSubcategory |
| StartDate | Date | Product launch date |
| EndDate | Date | Product discontinuation (if applicable) |
| Status | Text | Active / Discontinued |
| StandardCost | Currency | COGS for costing |
| ListPrice | Currency | List price (MSRP) |
| Size | Text | Product size |
| SizeRange | Text | XS-M, M-L, etc. |
| Weight | Decimal | Product weight |
| Color | Text | Product color |
| Class | Text | Product class (High/Medium/Low) |
| Style | Text | Product style (M=Men's, W=Women's, U=Unisex) |
| DaysToManufacture | Integer | Manufacturing lead time |

**Usage:** Product-level filtering, product hierarchy, costing calculations

---

### DimCustomer
**Purpose:** Customer master dimension  
**Grain:** One row per customer account  
**Row Count:** [Insert actual count]

| Column | Data Type | Key Attributes |
|--------|-----------|---|
| CustomerKey | Integer | PK |
| CustomerAlternateKey | Text | Customer business ID |
| FirstName | Text | Customer first name |
| LastName | Text | Customer last name |
| FullName | Text | Full customer name |
| Title | Text | Personal title (Mr., Ms., etc.) |
| DateOfBirth | Date | Customer birth date |
| MaritalStatus | Text | Marital status |
| Gender | Text | Gender (M/F) |
| EmailAddress | Text | Primary email |
| Phone | Text | Contact phone |
| AddressLine1 | Text | Primary address |
| City | Text | City |
| StateProvinceName | Text | State/Province |
| PostalCode | Text | Postal code |
| CountryRegionName | Text | Country |
| CustomerType | Text | Individual / Company |
| CommuteDistance | Text | Distance to store |
| GeographyKey | Integer | FK to DimGeography |

**Usage:** Customer segmentation, demographic analysis, behavioral metrics

---

### DimGeography
**Purpose:** Geographic dimension (countries, states, cities)  
**Grain:** One row per geographic location  
**Row Count:** [Insert actual count]

| Column | Data Type | Key Attributes |
|--------|-----------|---|
| GeographyKey | Integer | PK |
| City | Text | City name |
| StateProvinceCode | Text | State/Province code |
| StateProvinceName | Text | State/Province name |
| CountryRegionCode | Text | Country code (ISO) |
| CountryRegionName | Text | Country name |
| PostalCode | Text | Postal code |
| SalesTerritoryKey | Integer | FK to DimSalesTerritory |
| IPAddressLocator | Text | IP geolocation (if available) |
| Latitude | Decimal | Geographic latitude |
| Longitude | Decimal | Geographic longitude |

**Usage:** Territory analysis, geographic segmentation, regional performance

---

### DimSalesTerritory
**Purpose:** Sales territory and organizational hierarchy  
**Grain:** One row per sales territory  
**Row Count:** [Insert actual count]

| Column | Data Type | Key Attributes |
|--------|-----------|---|
| SalesTerritoryKey | Integer | PK |
| SalesTerritoryAlternateKey | Integer | Territory ID |
| SalesTerritoryRegion | Text | Region name (e.g., Northwest, Central) |
| SalesTerritoryCountry | Text | Country within territory |
| SalesTerritoryGroup | Text | Territory grouping (e.g., North America) |
| SalesYTDAmount | Currency | Year-to-date sales |
| SalesLastYearAmount | Currency | Prior year sales |
| CostYTDAmount | Currency | Year-to-date cost |
| CostLastYearAmount | Currency | Prior year cost |

**Usage:** Territory performance analysis, regional benchmarking, hierarchical filtering

---

## Relationship Map

```
FactResellerSales ──→ DimProduct
                  ├→ DimDate (multiple: OrderDate, DueDate, ShipDate)
                  └→ DimReseller (if available)

FactInternetSales ──→ DimProduct
                   ├→ DimDate (multiple: OrderDate, DueDate, ShipDate)
                   ├→ DimCustomer
                   └→ DimGeography

DimCustomer ──→ DimGeography
DimGeography ──→ DimSalesTerritory
DimProduct ──→ DimProductSubcategory ──→ DimProductCategory
```

---

## Data Quality Assumptions

- **Null Values:** [Document expected nulls in EndDate, SalesAmountQuota, etc.]
- **Date Validity:** All dates are within valid range [Start] to [End]
- **Foreign Key Integrity:** All FK references have matching PK values
- **Currency:** All monetary values in USD
- **Grain:** Fact tables at transaction line-item level

---

## Performance Considerations

- **Relationships:** All set to "Single" direction (filter fact from dimensions only)
- **Data Types:** Optimized for DAX performance (integers for FK, currency for monetary)
- **Cardinality:** Many-to-one from facts to dimensions (standard star schema)
- **Column Count:** Minimal—only essential attributes included

---

## Updates & Maintenance

- **Refresh Schedule:** [Daily at 6 AM EST / Other schedule]
- **Historical Data:** Maintained for [Number of years]
- **Incremental Refresh:** [Yes/No - if yes, describe logic]
- **Last Validated:** [Date of last data quality check]

---

**Created:** [Date]  
**Last Updated:** [Date]
