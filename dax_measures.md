#  DAX Measures – E-Commerce BI Project

This document contains the full DAX measures used across different pages of the Power BI report. Measures are grouped by the report section where they are used.

---

### Overview

**Revenue (This Period)**  
Uses CALCULATE to establish the latest reporting period and then sum the revenue for the matching period

```DAX 
Revenue (This Period) = 
VAR LatestPeriodRank = 
    CALCULATE(
        MAX(DimDate[Reporting Period Number])
    )
RETURN
CALCULATE(
    SUM('Fact Table'[Revenue EUR]),
    DimDate[Reporting Period Number] = LatestPeriodRank
)
```


**No of Brands**  
Counts the number of distinct brands in the current filter context

```DAX
No of Brands = 
DISTINCTCOUNT('Fact Table'[SubBrandKey])
```

**No of Agencies**  
Counts the number of distinct agencies in the current filter context

```DAX
No of Agencies = 
DISTINCTCOUNT('Fact Table'[AgencyKey])
```

**No of Countries**  
Counts the number of distinct countries in the current filter context

```DAX
DISTINCTCOUNT('Fact Table'[CountryKey])
```
 --- 

### Category Deep Dive

**% of Total Revenue**  
Uses DIVIDE to calculate the filtered revenue as a percentage of the overall revenue

```DAX
% of Total Revenue = 
DIVIDE(
SUM('Fact Table'[Revenue EUR]),
CALCULATE(SUM('Fact Table'[Revenue EUR]),ALL('Fact Table'))
)
```

**Top Sub-category (by Revenue)**  
Sums the revenue for each sub-category, ranks the sub-categories by revenue and returns the top ranked sub-category

```DAX
Top Sub-category (by Revenue) = 
VAR SubRevenue = 
ADDCOLUMNS(
    VALUES(DimBrand[Sub-Category]),
    "Sub Category Revenue",
    CALCULATE(SUM('Fact Table'[Revenue EUR]))
)

RETURN
SELECTCOLUMNS(
    TOPN(1,SubRevenue,[Sub Category Revenue],DESC),"Top Sub-Category",
    [Sub-Category])
```

**Top Country (by Revenue) %**  
Sums the revenue for each country, ranks the countries by revenue and returns the top ranked country

```DAX
Top Country (by Revenue) = 
VAR RevenuebyCountry =
ADDCOLUMNS(
    VALUES(DimGeo[Country]),
    "Country Revenue",
    CALCULATE(SUM('Fact Table'[Revenue EUR]))
)

RETURN
SELECTCOLUMNS(TOPN(1,RevenuebyCountry,[Country Revenue],DESC),"Top Country",[Country])
```
 --- 

### Regional Analysis

**Top Agencies by Revenue**  
Sums the revenue for each agencies, ranks them by revenue and returns the top ranked agency


```DAX
Top Agencies by Revenue = 
VAR AgenciesbyRevenue =
ADDCOLUMNS(VALUES(DimAgency[Agency Name]),"Agency Revenue", CALCULATE(SUM('Fact Table'[Revenue EUR])))

RETURN

SELECTCOLUMNS(TOPN(1,AgenciesbyRevenue,[Agency Revenue],DESC),"Top Agency",DimAgency[Agency Name])
```

**2RE 2025**  
Uses CALCULATE to sum the revenue for 2RE 2025

```DAX
2RE 2025 = 
CALCULATE(
    SUM('Fact Table'[Revenue EUR]),
    DimDate[Year Reporting Period] = "2025 2RE")
```

**2RE 2024**  
Uses CALCULATE to sum the revenue for 2RE 2024

```DAX
2RE 2024 = 
CALCULATE(
    SUM('Fact Table'[Revenue EUR]),
    DimDate[Year Reporting Period] = "2024 2RE")
```

**1RE 2025**  
Uses CALCULATE to sum the revenue for 1RE 2025

```DAX
1RE 2025 = 
CALCULATE(
    SUM('Fact Table'[Revenue EUR]),
    DimDate[Year Reporting Period] = "2025 1RE")
```

**YoY %**  
Uses DIVIDE to calculate the growth % between 2RE 2024 and 2RE 2025

```DAX
YoY % = 
DIVIDE(
    [2RE 2025] - [2RE 2024],
    [2RE 2024])
```

**QoQ %**  
Uses DIVIDE to calculate the growth % between 1RE 2025 and 2RE 2025

```DAX
QoQ % = 
DIVIDE(
    [2RE 2025] - [1RE 2025],
    [1RE 2025]
)
```
