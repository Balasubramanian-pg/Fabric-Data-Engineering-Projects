# Lab: Work with API for GraphQL in Microsoft Fabric

## Introduction  
This hands-on lab will guide you through using Microsoft Fabric's API for GraphQL to efficiently query data from multiple sources. GraphQL provides a flexible query language that enables clients to request exactly the data they need in a single API call.

**Estimated Duration**: 30 minutes  
**Prerequisites**: Microsoft Fabric trial account

## Exercise 1: Set Up Your Environment

### Task 1: Create a Workspace
1. Sign in to [Microsoft Fabric](https://app.fabric.microsoft.com)
2. Select **New workspace** from the left navigation
3. Name your workspace (e.g., "GraphQL-Lab")
4. Choose a licensing mode with Fabric capacity (Trial, Premium, or Fabric)
5. Verify your new empty workspace appears

### Task 2: Create and Populate a SQL Database
1. In your workspace, select **Create** → **SQL database**
2. Name the database "AdventureWorksLT" and create it
3. Load sample data using the **Sample data** option
4. Confirm the database appears with populated tables

# Exercise 2: Deep Dive into SQL Data Exploration

## Objective
This expanded exercise will provide a more comprehensive exploration of the SQL database in Microsoft Fabric, including advanced query techniques and data analysis that will later inform our GraphQL API design.

## Task 2.1: Initial Data Exploration

### 2.1.1 Explore Database Schema
1. In your AdventureWorksLT database, navigate to the **Tables** section
2. Examine the available tables:
   - SalesLT.Product (ProductID, Name, ProductNumber, Color, etc.)
   - SalesLT.ProductCategory (ProductCategoryID, ParentCategoryID, Name)
   - SalesLT.ProductModel (ProductModelID, Name)
   - SalesLT.ProductModelProductDescription (ProductModelID, ProductDescriptionID)
   - SalesLT.ProductDescription (ProductDescriptionID, Description)

### 2.1.2 Basic Data Sampling
Run these queries to understand the data distribution:

```sql
-- Count products per category
SELECT 
    pc.Name AS Category,
    COUNT(p.ProductID) AS ProductCount
FROM 
    SalesLT.Product p
JOIN 
    SalesLT.ProductCategory pc ON p.ProductCategoryID = pc.ProductCategoryID
GROUP BY 
    pc.Name
ORDER BY 
    ProductCount DESC;

-- Price distribution analysis
SELECT 
    MIN(ListPrice) AS MinPrice,
    MAX(ListPrice) AS MaxPrice,
    AVG(ListPrice) AS AvgPrice,
    STDEV(ListPrice) AS PriceStdDev
FROM 
    SalesLT.Product
WHERE 
    ListPrice > 0;
```

## Task 2.2: Advanced Query Techniques

### 2.2.1 Hierarchical Category Query
```sql
-- Recursive CTE for category hierarchy
WITH CategoryHierarchy AS (
    -- Anchor member (top-level categories)
    SELECT 
        ProductCategoryID,
        ParentProductCategoryID,
        Name,
        0 AS Level
    FROM 
        SalesLT.ProductCategory
    WHERE 
        ParentProductCategoryID IS NULL
    
    UNION ALL
    
    -- Recursive member (subcategories)
    SELECT 
        pc.ProductCategoryID,
        pc.ParentProductCategoryID,
        pc.Name,
        ch.Level + 1
    FROM 
        SalesLT.ProductCategory pc
    JOIN 
        CategoryHierarchy ch ON pc.ParentProductCategoryID = ch.ProductCategoryID
)
SELECT 
    REPLICATE('    ', Level) + Name AS CategoryTree,
    Level
FROM 
    CategoryHierarchy
ORDER BY 
    Level, Name;
```

### 2.2.2 Product Attribute Analysis
```sql
-- Pivot table showing available sizes by category
SELECT 
    CategoryName,
    [S], [M], [L], [XL], [38], [40], [42], [44], [48], [52]
FROM (
    SELECT 
        pc.Name AS CategoryName,
        p.Size,
        p.ProductID
    FROM 
        SalesLT.Product p
    JOIN 
        SalesLT.ProductCategory pc ON p.ProductCategoryID = pc.ProductCategoryID
    WHERE 
        p.Size IS NOT NULL
) AS SourceTable
PIVOT (
    COUNT(ProductID)
    FOR Size IN ([S], [M], [L], [XL], [38], [40], [42], [44], [48], [52])
) AS PivotTable;
```

## Task 2.3: Data Quality Assessment

### 2.3.1 Missing Value Analysis
```sql
-- Check for missing critical data
SELECT
    SUM(CASE WHEN Name IS NULL THEN 1 ELSE 0 END) AS MissingNames,
    SUM(CASE WHEN ProductNumber IS NULL THEN 1 ELSE 0 END) AS MissingProductNumbers,
    SUM(CASE WHEN StandardCost IS NULL THEN 1 ELSE 0 END) AS MissingCosts,
    SUM(CASE WHEN ListPrice IS NULL THEN 1 ELSE 0 END) AS MissingPrices,
    COUNT(*) AS TotalProducts
FROM 
    SalesLT.Product;
```

### 2.3.2 Price Consistency Check
```sql
-- Find products where cost exceeds price (data anomaly)
SELECT 
    ProductID,
    Name,
    StandardCost,
    ListPrice,
    ListPrice - StandardCost AS Margin,
    (ListPrice - StandardCost)/NULLIF(StandardCost, 0) AS MarginPercentage
FROM 
    SalesLT.Product
WHERE 
    StandardCost > ListPrice
    OR StandardCost <= 0
ORDER BY 
    MarginPercentage;
```

## Task 2.4: Preparing for GraphQL Exposure

### 2.4.1 Identify Key Relationships
```sql
-- Many-to-many relationship between products and models
SELECT 
    pm.Name AS ModelName,
    STRING_AGG(p.Name, ', ') AS ProductsInModel
FROM 
    SalesLT.ProductModel pm
JOIN 
    SalesLT.ProductModelProductDescription pmpd ON pm.ProductModelID = pmpd.ProductModelID
JOIN 
    SalesLT.Product p ON p.ProductModelID = pm.ProductModelID
GROUP BY 
    pm.Name;
```

### 2.4.2 Determine Optimal GraphQL Types
Based on our analysis, we can identify these natural GraphQL types:
- Product (with fields: id, name, number, color, size, cost, price)
- ProductCategory (with hierarchy support)
- ProductModel (with product collections)

## Key Takeaways
1. The database contains a complete product catalog with hierarchical categories
2. Price data is generally clean but has a few anomalies to address
3. Product models group multiple product variants
4. Size information is available for apparel products
5. The data structure naturally maps to GraphQL types and relationships
---

## Exercise 3: Configure GraphQL API

### Task 1: Create GraphQL Endpoint
1. In your workspace, select **New item** → **API for GraphQL**
2. Name your API (e.g., "ProductsAPI") and create it
3. Select **Select data source**
4. Choose SSO authentication option
5. Connect to your AdventureWorksLT database
6. Select the `SalesLT.Product` table and load it
7. Note the API endpoint URL (optional)

### Task 2: Secure the API
1. In Schema Explorer, expand **Mutations**
2. For each mutation operation:
   - Select the **...** menu
   - Choose **Disable**
3. Confirm all mutations are now disabled

## Exercise 4: Query Data with GraphQL

### Task 1: Execute GraphQL Query
1. In the GraphQL query editor, enter:
```graphql
query {
  products(filter: { Name: { startsWith: "HL Road Frame" } }) {
    items {
      ProductModelID
      Name
      ListPrice
      Color
      Size
      ModifiedDate
    }
  }
}
```
2. Execute the query
3. Review the returned product data matching your filter

## Cleanup
1. Navigate to workspace settings
2. Select **Remove this workspace** to delete all resources

## Conclusion
You've successfully:
- Created a Fabric workspace with sample data
- Established a GraphQL API endpoint
- Configured secure read-only access
- Executed targeted GraphQL queries

For advanced features, explore the [Microsoft Fabric GraphQL documentation](https://learn.microsoft.com/fabric/data-engineering/api-graphql-overview).


