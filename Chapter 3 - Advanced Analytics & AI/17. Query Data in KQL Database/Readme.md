# Working with Data in a Microsoft Fabric Eventhouse  

## Lab Overview  
This lab explores how to:  
1. Create and populate an Eventhouse  
2. Query data using Kusto Query Language (KQL)  
3. Query data using Transact-SQL (T-SQL)  

**Estimated Time**: 25 minutes  


## 1. Create a Workspace  
1. Sign in to [Microsoft Fabric](https://app.fabric.microsoft.com)  
2. Select **Workspaces** (icon: &#128455;) from the left menu  
3. Create a new workspace:  
   - Assign a name  
   - Choose a licensing mode with Fabric capacity (*Trial*, *Premium*, or *Fabric*)  
4. Verify the new workspace opens empty  

![Empty workspace](./Images/new-workspace.png)  


## 2. Create an Eventhouse with Sample Data  
1. Navigate to **Real-Time Intelligence** workload  
2. Select **Explore Real-Time Intelligence Sample**  
   - Automatically creates an Eventhouse named **RTISample**  
   - Includes a KQL database and **Bikestream** table  

![Eventhouse with sample data](./Images/create-eventhouse-sample.png)  


## 3. Query Data Using KQL  

### Basic Data Retrieval  
```kql
// Get 100 sample records
Bikestream | take 100

// Select specific columns
Bikestream | project Street, No_Bikes | take 10

// Rename columns
Bikestream | project Street, ["Number of Empty Docks"] = No_Empty_Docks | take 10
```

### Data Aggregation  
```kql
// Total bikes available
Bikestream | summarize ["Total Bikes"] = sum(No_Bikes)

// Bikes by neighborhood (with null handling)
Bikestream
| summarize ["Total Bikes"] = sum(No_Bikes) by Neighbourhood
| project Neighbourhood = case(
    isempty(Neighbourhood) or isnull(Neighbourhood), 
    "Unidentified", 
    Neighbourhood), 
    ["Total Bikes"]
```

### Sorting and Filtering  
```kql
// Sort results
| sort by Neighbourhood asc  // or 'order by'

// Filter for Chelsea neighborhood
| where Neighbourhood == "Chelsea"
```


## 4. Query Data Using T-SQL  

### Basic Queries  
```sql
-- Get 100 records
SELECT TOP 100 * FROM Bikestream

-- Specific columns with aliasing
SELECT TOP 10 
    Street, 
    No_Empty_Docks AS [Number of Empty Docks]
FROM Bikestream
```

### Aggregation and Grouping  
```sql
-- Total bikes
SELECT SUM(No_Bikes) AS [Total Bikes] FROM Bikestream

-- With neighborhood grouping
SELECT 
    CASE
        WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
        ELSE Neighbourhood
    END AS Neighbourhood,
    SUM(No_Bikes) AS [Total Bikes]
FROM Bikestream
GROUP BY CASE
    WHEN Neighbourhood IS NULL OR Neighbourhood = '' THEN 'Unidentified'
    ELSE Neighbourhood
END
ORDER BY Neighbourhood ASC
```

### Filtering  
```sql
-- Filter for Chelsea
HAVING Neighbourhood = 'Chelsea'
```


## 5. Clean Up Resources  
1. Navigate to your workspace  
2. Select **Workspace settings**  
3. Choose **Remove this workspace**  


## Key Takeaways  
- Eventhouses store real-time streaming data in KQL databases  
- KQL provides powerful query capabilities optimized for time-series data  
- T-SQL support enables compatibility with existing SQL-based tools  
- Both languages can filter, aggregate, and transform data effectively  

![Query comparison](./Images/kql-take-100-query.png)  

> **Note**: While T-SQL is supported, KQL is recommended for optimal performance in Eventhouses.