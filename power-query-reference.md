# Power Query Transformations (M Code Reference)

This document provides reference M code and transformation patterns used in the Power BI data model.

---

## Connection Strings

### SQL Server Connection (AdventureWorks)
```m
// Basic connection to SQL Server
let
    Source = Sql.Database("SERVER_NAME", "AdventureWorks"),
    // Select tables as needed
    Navigation = Source
in
    Navigation
```

### Using Connection String
```m
let
    Source = Sql.Database(
        "localhost",        // Server
        "AdventureWorks",   // Database
        [Query="SELECT * FROM FactInternetSales"]  // Optional: specific query
    )
in
    Source
```

---

## FactResellerSales Transformations

### Basic Load & Cleaning
```m
let
    Source = Sql.Database("SERVER", "AdventureWorks"){[Schema="dbo",Item="FactResellerSales"]}[Data],
    
    // Remove sensitive columns
    Removed = Table.RemoveColumns(Source, {"DiscountAmount"}),  // Remove if not needed
    
    // Type columns appropriately
    Typed = Table.TransformColumnTypes(Removed, {
        {"SalesOrderNumber", type text},
        {"SalesAmount", type currency},
        {"OrderQuantity", type int},
        {"SalesOrderLineNumber", type int},
        {"OrderDateKey", Int64.Type},
        {"DueDateKey", Int64.Type},
        {"ShipDateKey", Int64.Type}
    }),
    
    // Filter out test records (if applicable)
    Filtered = Table.SelectRows(Typed, each [SalesAmount] > 0)
in
    Filtered
```

---

## DimDate (Calendar) Creation

### Generating Calendar Dimension
```m
let
    // Define date range
    StartYear = 2010,
    EndYear = 2014,
    StartDate = #date(StartYear, 1, 1),
    EndDate = #date(EndYear, 12, 31),
    
    // Generate list of dates
    DateList = List.Dates(StartDate, Duration.Days(EndDate - StartDate) + 1, #duration(1,0,0,0)),
    
    // Convert to table
    Source = Table.FromList(DateList, Splitter.SplitByNothing(), {"FullDate"}, null, ExtraValues.Error),
    
    // Add date attributes
    AddDateKey = Table.AddColumn(Source, "DateKey", each 
        let
            y = Date.Year([FullDate]),
            m = Date.Month([FullDate]),
            d = Date.Day([FullDate])
        in
            y * 10000 + m * 100 + d, 
        Int64.Type),
    
    AddDayOfWeek = Table.AddColumn(AddDateKey, "DayNumberOfWeek", 
        each Date.DayOfWeek([FullDate]) + 1, Int64.Type),
    
    AddDayName = Table.AddColumn(AddDayOfWeek, "DayNameOfWeek", 
        each Text.Proper(Date.ToText([FullDate], "dddd")), type text),
    
    AddMonth = Table.AddColumn(AddDayName, "MonthNumberOfYear", 
        each Date.Month([FullDate]), Int64.Type),
    
    AddMonthName = Table.AddColumn(AddMonth, "MonthName", 
        each Text.Proper(Date.ToText([FullDate], "MMMM")), type text),
    
    AddQuarter = Table.AddColumn(AddMonthName, "CalendarQuarter", 
        each Number.RoundUp(Date.Month([FullDate]) / 3, 0), Int64.Type),
    
    AddYear = Table.AddColumn(AddQuarter, "CalendarYear", 
        each Date.Year([FullDate]), Int64.Type),
    
    // Reorder columns logically
    Reordered = Table.ReorderColumns(AddYear, 
        {"DateKey", "FullDate", "DayNameOfWeek", "MonthName", "CalendarQuarter", "CalendarYear"})
in
    Reordered
```

---

## DimProduct Transformations

### Product Dimension Cleanup
```m
let
    Source = Sql.Database("SERVER", "AdventureWorks"){[Schema="dbo",Item="DimProduct"]}[Data],
    
    // Remove discontinued products (optional - keep for historical reporting)
    // Filtered = Table.SelectRows(Source, each [Status] = "Active"),
    
    // Standardize text fields
    TrimmedText = Table.TransformColumns(Source, {
        {"ProductName", Text.Trim},
        {"Color", Text.Trim},
        {"Size", Text.Trim}
    }),
    
    // Replace empty strings with null
    ReplacedBlanks = Table.ReplaceValue(TrimmedText, "", null, Replacer.ReplaceValue, 
        {"Color", "Size", "Style"}),
    
    // Type columns
    Typed = Table.TransformColumnTypes(ReplacedBlanks, {
        {"ProductKey", Int64.Type},
        {"ProductName", type text},
        {"StandardCost", type currency},
        {"ListPrice", type currency},
        {"DaysToManufacture", Int64.Type}
    })
in
    Typed
```

---

## DimCustomer Transformations

### Customer Data Standardization
```m
let
    Source = Sql.Database("SERVER", "AdventureWorks"){[Schema="dbo",Item="DimCustomer"]}[Data],
    
    // Combine first and last names
    AddFullName = Table.AddColumn(Source, "FullName", 
        each Text.Combine({[FirstName], [LastName]}, " "), 
        type text),
    
    // Standardize email (lowercase)
    LowerEmail = Table.TransformColumns(AddFullName, 
        {{"EmailAddress", Text.Lower}}),
    
    // Add age calculation (if DOB available)
    AddAge = Table.AddColumn(LowerEmail, "Age", 
        each Number.RoundDown(
            (Date.From(DateTime.LocalNow()) - [DateOfBirth]) / #duration(365.25, 0, 0, 0)
        ),
        Int64.Type),
    
    // Type appropriately
    Typed = Table.TransformColumnTypes(AddAge, {
        {"CustomerKey", Int64.Type},
        {"FirstName", type text},
        {"LastName", type text},
        {"EmailAddress", type text},
        {"Phone", type text},
        {"DateOfBirth", type date},
        {"Age", Int64.Type}
    })
in
    Typed
```

---

## DimGeography Transformations

### Geography Dimension
```m
let
    Source = Sql.Database("SERVER", "AdventureWorks"){[Schema="dbo",Item="DimGeography"]}[Data],
    
    // Standardize state/country names
    StandardizeState = Table.TransformColumns(Source, 
        {{"StateProvinceName", Text.Proper}}),
    
    StandardizeCountry = Table.TransformColumns(StandardizeState, 
        {{"CountryRegionName", Text.Proper}}),
    
    // Type columns
    Typed = Table.TransformColumnTypes(StandardizeCountry, {
        {"GeographyKey", Int64.Type},
        {"City", type text},
        {"StateProvinceCode", type text},
        {"StateProvinceName", type text},
        {"CountryRegionCode", type text},
        {"CountryRegionName", type text},
        {"PostalCode", type text},
        {"Latitude", type number},
        {"Longitude", type number}
    })
in
    Typed
```

---

## Common Transformation Patterns

### Pattern 1: Removing Null Rows
```m
Table.SelectRows(Source, each [ImportantColumn] <> null)
```

### Pattern 2: Conditional Column
```m
Table.AddColumn(Source, "NewColumn", 
    each if [Amount] > 10000 then "High" else "Low", 
    type text)
```

### Pattern 3: Unpivoting (Wide to Long)
```m
Table.Unpivot(Source, 
    {"Column1", "Column2"},  // Columns to unpivot
    "Period",                 // Attribute column name
    "Value")                  // Value column name
```

### Pattern 4: Merging/Joining Tables
```m
Table.NestedJoin(
    FactTable, {"ForeignKey"}, 
    DimensionTable, {"PrimaryKey"}, 
    "NewColumnName", 
    JoinKind.LeftOuter)
```

### Pattern 5: Grouping & Aggregation
```m
Table.Group(Source, 
    {"GroupByColumn"}, 
    {{"Sum", each List.Sum([AmountColumn]), type number},
     {"Count", Table.RowCount(_), Int64.Type}})
```

---

## Error Handling

### Try-Catch Pattern
```m
let
    Source = try Sql.Database("SERVER", "Database") 
             otherwise "Error: Could not connect to database"
in
    Source
```

---

## Performance Optimization Tips

### Query Folding Consideration
```m
// This will fold (execute at source):
Table.SelectRows(Source, each [Status] = "Active")

// This will NOT fold (executes in Power Query):
Table.AddColumn(Source, "Calculated", each [Price] * [Quantity])
```

### Recommended Practices
1. **Filter early:** Apply source filters before adding calculated columns
2. **Use native functions:** SQL functions > Power Query equivalents
3. **Avoid complex calculations:** Keep ETL simple; move complex logic to DAX
4. **Document transformations:** Use comments for non-obvious logic

---

## Incremental Refresh Pattern (Future)

```m
let
    Source = Sql.Database("SERVER", "AdventureWorks"),
    FactTable = Source{[Schema="dbo",Item="FactResellerSales"]}[Data],
    
    // Filter to recent changes (for incremental refresh)
    FilteredByDate = Table.SelectRows(FactTable, 
        each [OrderDateKey] >= RangeStart and [OrderDateKey] <= RangeEnd),
    
    Typed = Table.TransformColumnTypes(FilteredByDate, 
        {{"OrderDateKey", Int64.Type}})
in
    Typed
```

**Note:** Requires RangeStart and RangeEnd parameters defined in Power BI refresh settings

---

## Testing Queries

### Validation Query (Run in SQL Server)
```sql
-- Verify row counts after transformation
SELECT 
    'FactResellerSales' AS TableName,
    COUNT(*) AS RowCount
FROM AdventureWorks.dbo.FactResellerSales

UNION ALL

SELECT 
    'FactInternetSales',
    COUNT(*)
FROM AdventureWorks.dbo.FactInternetSales

UNION ALL

SELECT 
    'DimDate',
    COUNT(*)
FROM [DW_Database].dbo.DimDate
```

---

## Resources & Documentation

- [Microsoft Power Query M Reference](https://docs.microsoft.com/en-us/powerquery-m/power-query-m-reference)
- [Power BI Data Transformation Best Practices](https://docs.microsoft.com/en-us/power-bi/guidance/power-query-tutorials)
- [SQL to M Code Mapping Guide](https://docs.microsoft.com/power-bi/)

---

**Created:** [Date]  
**Last Updated:** [Date]  
**Maintained By:** [Name/Team]
