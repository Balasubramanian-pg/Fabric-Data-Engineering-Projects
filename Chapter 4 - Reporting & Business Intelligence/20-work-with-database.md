# Hands-on Lab: Working with SQL Database in Microsoft Fabric

## Introduction

Welcome to this hands-on lab where you'll explore SQL Database capabilities in Microsoft Fabric. In this 30-minute session, you'll create a workspace, build a SQL database with sample data, execute queries, integrate external data sources, and implement security controls.

## Prerequisites

- A Microsoft Fabric trial account
- Basic familiarity with SQL concepts

## Lab Setup

### Step 1: Create a Workspace

1. Sign in to [Microsoft Fabric](https://app.fabric.microsoft.com)
2. Select **New workspace** from the left navigation
3. Name your workspace (e.g., "Fabric-SQL-Lab")
4. Choose a licensing mode (Trial, Premium, or Fabric)
5. Click **Create**

![Empty workspace ready for database creation](Images/new-workspace.png)

### Step 2: Create a SQL Database with Sample Data

1. In your new workspace, click **Create**
2. Under Databases, select **SQL database**
3. Name your database "AdventureWorksLT"
4. Click **Create**
5. Once created, load the sample data from the **Sample data** card

![Database populated with sample data](Images/sql-database-sample.png)

## Working with Data

# **Exercise 1: Querying the SQL Database in Microsoft Fabric**

In this expanded exercise, you will dive deeper into querying the **AdventureWorksLT** database in Microsoft Fabric. You will explore different SQL operations, including filtering, aggregations, joins, and subqueries to extract meaningful business insights.

---

## **1.1 Basic Querying: Retrieving Data**
### **1.1.1 Simple SELECT Statements**
Start with basic queries to understand the data structure.

#### **Query 1: Retrieve All Products**
```sql
SELECT * FROM SalesLT.Product;
```
- Lists all columns from the `Product` table.
- Helps understand available fields (e.g., `ProductID`, `Name`, `ProductNumber`, `Color`, `StandardCost`, `ListPrice`).

#### **Query 2: Get Specific Columns (Product Name & Price)**
```sql
SELECT 
    Name AS ProductName, 
    ListPrice 
FROM SalesLT.Product;
```
- Returns only product names and their list prices.

---

## **1.2. Filtering Data with WHERE Clause**
### **1.2.1. Filtering by Price Range**
```sql
SELECT 
    Name AS ProductName, 
    ListPrice 
FROM SalesLT.Product
WHERE ListPrice > 1000;
```
- Retrieves only high-value products (price > $1000).

### **1.2.2. Filtering by Product Category**
```sql
SELECT 
    p.Name AS ProductName, 
    p.ListPrice,
    pc.Name AS CategoryName
FROM SalesLT.Product p
JOIN SalesLT.ProductCategory pc 
    ON p.ProductCategoryID = pc.ProductCategoryID
WHERE pc.Name = 'Mountain Bikes';
```
- Returns only products in the "Mountain Bikes" category.

---

## **1.3. Sorting Results with ORDER BY**
### **1.3.1. Sorting by Price (High to Low)**
```sql
SELECT 
    Name AS ProductName, 
    ListPrice 
FROM SalesLT.Product
ORDER BY ListPrice DESC;
```
- Lists products from most to least expensive.

### **1.3.2. Sorting by Name (Alphabetical)**
```sql
SELECT 
    Name AS ProductName, 
    ListPrice 
FROM SalesLT.Product
ORDER BY Name ASC;
```
- Returns products in alphabetical order.

---

## **1.4. Aggregating Data with GROUP BY**
### **1.4.1. Average Price by Product Category**
```sql
SELECT 
    pc.Name AS CategoryName,
    AVG(p.ListPrice) AS AvgPrice
FROM SalesLT.Product p
JOIN SalesLT.ProductCategory pc 
    ON p.ProductCategoryID = pc.ProductCategoryID
GROUP BY pc.Name;
```
- Computes average price for each product category.

### **1.4.2. Count of Products per Category**
```sql
SELECT 
    pc.Name AS CategoryName,
    COUNT(p.ProductID) AS ProductCount
FROM SalesLT.Product p
JOIN SalesLT.ProductCategory pc 
    ON p.ProductCategoryID = pc.ProductCategoryID
GROUP BY pc.Name
ORDER BY ProductCount DESC;
```
- Shows which categories have the most products.

---

## **1.5. Advanced Joins**
### **1.5.1. Retrieve Customer Orders**
```sql
SELECT 
    c.FirstName,
    c.LastName,
    soh.SalesOrderID,
    soh.OrderDate,
    soh.TotalDue
FROM SalesLT.Customer c
JOIN SalesLT.SalesOrderHeader soh 
    ON c.CustomerID = soh.CustomerID
ORDER BY soh.OrderDate DESC;
```
- Lists customers along with their orders.

### **1.5.2. Order Details with Product Info**
```sql
SELECT 
    soh.SalesOrderID,
    p.Name AS ProductName,
    sod.OrderQty,
    sod.UnitPrice,
    (sod.OrderQty * sod.UnitPrice) AS LineTotal
FROM SalesLT.SalesOrderHeader soh
JOIN SalesLT.SalesOrderDetail sod 
    ON soh.SalesOrderID = sod.SalesOrderID
JOIN SalesLT.Product p 
    ON sod.ProductID = p.ProductID
WHERE soh.SalesOrderID = 71774;  -- Example order ID
```
- Shows detailed line items for a specific order.

---

## **1.6. Subqueries for Complex Filtering**
### **1.6.1. Find Products Priced Above Average**
```sql
SELECT 
    Name AS ProductName, 
    ListPrice
FROM SalesLT.Product
WHERE ListPrice > (SELECT AVG(ListPrice) FROM SalesLT.Product);
```
- Retrieves products priced higher than the average.

### **1.6.2. Customers Who Placed Orders**
```sql
SELECT 
    FirstName,
    LastName,
    EmailAddress
FROM SalesLT.Customer
WHERE CustomerID IN (SELECT DISTINCT CustomerID FROM SalesLT.SalesOrderHeader);
```
- Lists only customers who have made purchases.

---

## **1.7. Practical Business Insights**
### **1.7.1. Top 5 Most Expensive Products**
```sql
SELECT TOP 5
    Name AS ProductName,
    ListPrice
FROM SalesLT.Product
ORDER BY ListPrice DESC;
```
- Helps identify premium products.

### **1.7.2. Sales Revenue by Customer**
```sql
SELECT 
    c.FirstName + ' ' + c.LastName AS CustomerName,
    SUM(soh.TotalDue) AS TotalSpent
FROM SalesLT.Customer c
JOIN SalesLT.SalesOrderHeader soh 
    ON c.CustomerID = soh.CustomerID
GROUP BY c.FirstName, c.LastName
ORDER BY TotalSpent DESC;
```
- Identifies high-value customers.

---

## **Summary of Key Learnings**
✅ **Basic SELECT queries** – Retrieve data from tables.  
✅ **Filtering with WHERE** – Narrow down results.  
✅ **Sorting with ORDER BY** – Organize output.  
✅ **Aggregations with GROUP BY** – Summarize data.  
✅ **Joins** – Combine data from multiple tables.  
✅ **Subqueries** – Perform complex filtering.  
✅ **Business insights** – Extract actionable data.  

---

### Exercise 2: Data Integration

In this exercise, you will explore **data integration** techniques in Microsoft Fabric's SQL Database. You will learn how to:
- **Import external datasets** into your database
- **Enrich existing data** with new information
- **Combine multiple sources** for deeper analysis
- **Automate data refreshes** (optional)

---

## **2.1. Setting Up External Data Sources**
Before integrating data, you need to **prepare the external dataset**. In this lab, we'll use a **Public Holidays** dataset.

### **2.1.1. Create the PublicHolidays Table**
```sql
CREATE TABLE SalesLT.PublicHolidays (
    HolidayID INT IDENTITY(1,1) PRIMARY KEY,
    CountryOrRegion NVARCHAR(50) NOT NULL,
    HolidayName NVARCHAR(100) NOT NULL,
    Date DATE NOT NULL,
    IsPaidTimeOff BIT DEFAULT 1,
    ModifiedDate DATETIME DEFAULT GETDATE()
);
```
- Adds a **primary key** (`HolidayID`) for better data integrity.
- Uses `NOT NULL` constraints to ensure required fields are populated.
- Includes `ModifiedDate` for tracking changes.

### **2.1.2. Insert Sample Holiday Data**
```sql
INSERT INTO SalesLT.PublicHolidays (CountryOrRegion, HolidayName, Date)
VALUES
    ('United States', 'New Year''s Day', '2024-01-01'),
    ('United States', 'Independence Day', '2024-07-04'),
    ('United States', 'Thanksgiving Day', '2024-11-28'),
    ('Canada', 'Canada Day', '2024-07-01'),
    ('United Kingdom', 'Christmas Day', '2024-12-25'),
    ('United Kingdom', 'Boxing Day', '2024-12-26');
```

---

## **2.2. Enriching Sales Data with Holiday Information**
Now, let’s **link sales orders to public holidays** to analyze seasonal trends.

### **2.2.1. Add Sample Sales Orders**
```sql
-- Insert new customers (if needed)
INSERT INTO SalesLT.Customer (FirstName, LastName, EmailAddress, Phone)
VALUES
    ('John', 'Doe', 'john.doe@example.com', '555-1001'),
    ('Jane', 'Smith', 'jane.smith@example.com', '555-1002');

-- Insert new addresses
INSERT INTO SalesLT.Address (AddressLine1, City, StateProvince, CountryRegion, PostalCode)
VALUES
    ('123 Main St', 'New York', 'NY', 'United States', '10001'),
    ('456 Maple Ave', 'Toronto', 'ON', 'Canada', 'M5V 2H1'),
    ('789 Oxford St', 'London', NULL, 'United Kingdom', 'W1D 1BS');

-- Insert holiday-related sales orders
INSERT INTO SalesLT.SalesOrderHeader (
    OrderDate, DueDate, CustomerID, ShipToAddressID, SubTotal, TaxAmt, Freight, TotalDue
)
VALUES
    ('2024-07-04', '2024-07-11', 1, 1, 500.00, 50.00, 25.00, 575.00), -- US Independence Day
    ('2024-12-25', '2024-12-31', 2, 3, 750.00, 75.00, 40.00, 865.00), -- UK Christmas Day
    ('2024-11-28', '2024-12-05', 1, 1, 300.00, 30.00, 15.00, 345.00);  -- US Thanksgiving
```

### **2.2.2. Query: Find Sales on Public Holidays**
```sql
SELECT 
    soh.SalesOrderID,
    soh.OrderDate,
    ph.HolidayName,
    ph.CountryOrRegion,
    soh.TotalDue
FROM SalesLT.SalesOrderHeader soh
JOIN SalesLT.Address a ON soh.ShipToAddressID = a.AddressID
JOIN SalesLT.PublicHolidays ph 
    ON soh.OrderDate = ph.Date 
    AND a.CountryRegion = ph.CountryOrRegion;
```
**Expected Output:**
| SalesOrderID | OrderDate   | HolidayName       | CountryOrRegion | TotalDue |
|-------------|------------|------------------|----------------|---------|
| 1001        | 2024-07-04 | Independence Day | United States   | 575.00  |
| 1002        | 2024-12-25 | Christmas Day    | United Kingdom  | 865.00  |
| 1003        | 2024-11-28 | Thanksgiving Day | United States   | 345.00  |

---

## **2.3. Advanced Integration: Combining Multiple Data Sources**
Let’s **import a CSV file** containing **discount promotions** and analyze its impact on sales.

### **2.3.1. Create a Promotions Table**
```sql
CREATE TABLE SalesLT.Promotions (
    PromotionID INT IDENTITY(1,1) PRIMARY KEY,
    PromotionName NVARCHAR(100) NOT NULL,
    DiscountPercentage DECIMAL(5,2) NOT NULL,
    StartDate DATE NOT NULL,
    EndDate DATE NOT NULL,
    ApplicableCategory NVARCHAR(50) NULL
);
```

### **2.3.2. Insert Promotions Data**
```sql
INSERT INTO SalesLT.Promotions (PromotionName, DiscountPercentage, StartDate, EndDate, ApplicableCategory)
VALUES
    ('Summer Sale', 15.00, '2024-06-01', '2024-08-31', 'Mountain Bikes'),
    ('Black Friday', 20.00, '2024-11-25', '2024-11-29', NULL),
    ('Holiday Special', 10.00, '2024-12-20', '2024-12-31', 'Accessories');
```

### **2.3.3. Query: Sales with Applied Discounts**
```sql
SELECT 
    p.Name AS ProductName,
    pc.Name AS CategoryName,
    pr.PromotionName,
    pr.DiscountPercentage,
    sod.UnitPrice,
    (sod.UnitPrice * (1 - pr.DiscountPercentage/100)) AS DiscountedPrice
FROM SalesLT.SalesOrderDetail sod
JOIN SalesLT.Product p ON sod.ProductID = p.ProductID
JOIN SalesLT.ProductCategory pc ON p.ProductCategoryID = pc.ProductCategoryID
JOIN SalesLT.Promotions pr 
    ON sod.ModifiedDate BETWEEN pr.StartDate AND pr.EndDate
    AND (pr.ApplicableCategory IS NULL OR pc.Name = pr.ApplicableCategory)
WHERE pr.PromotionName IS NOT NULL;
```
**Expected Output:**
| ProductName         | CategoryName    | PromotionName  | DiscountPercentage | UnitPrice | DiscountedPrice |
|--------------------|----------------|---------------|-------------------|----------|----------------|
| Mountain-100 Silver| Mountain Bikes  | Summer Sale   | 15.00             | 3399.99  | 2889.99        |
| HL Road Frame      | Road Frames     | Black Friday  | 20.00             | 1431.50  | 1145.20        |

---

## **2.4. Automating Data Integration (Optional)**
Microsoft Fabric allows **scheduled refreshes** of external data.

### **2.4.1. Using Power Query in Fabric**
1. Go to **Data Engineering** or **Data Factory** in Fabric.
2. Create a **new data pipeline**.
3. Add a **SQL query** to pull external data.
4. Set a **refresh schedule** (daily/weekly).

### **2.4.2. Example: Auto-Update Holidays Table**
```sql
-- Step 1: Create a stored procedure to refresh data
CREATE PROCEDURE SalesLT.sp_RefreshHolidays
AS
BEGIN
    -- Clear old data
    TRUNCATE TABLE SalesLT.PublicHolidays;
    
    -- Insert new data (could be from an API or CSV)
    INSERT INTO SalesLT.PublicHolidays (CountryOrRegion, HolidayName, Date)
    VALUES
        ('United States', 'Labor Day', '2024-09-02'),
        ('Canada', 'Labour Day', '2024-09-02');
END;
```

---

## **2.5. Key Takeaways**
✅ **Import external datasets** into SQL Database.  
✅ **Enrich transactional data** with contextual information (e.g., holidays, promotions).  
✅ **Combine multiple sources** for deeper business insights.  
✅ **Automate data refreshes** to keep reports up-to-date.  

---

### Exercise 3: Implementing Security

In this expanded exercise, we'll dive deep into **data security** in Microsoft Fabric's SQL Database. You'll learn how to implement:
- **Role-based access control (RBAC)**
- **Row-level security (RLS)**
- **Column-level security**
- **Dynamic data masking**
- **Auditing and compliance tracking**

---

## **3.1. Role-Based Access Control (RBAC)**
RBAC restricts database access based on user roles.

### **3.1.1. Creating Database Roles**
```sql
-- Create roles for different departments
CREATE ROLE SalesTeam;
CREATE ROLE FinanceTeam;
CREATE ROLE ReadOnlyUsers;
```

### **3.1.2. Granting Permissions**
```sql
-- Sales team can read/write customer and order data
GRANT SELECT, INSERT, UPDATE ON SalesLT.Customer TO SalesTeam;
GRANT SELECT, INSERT, UPDATE ON SalesLT.SalesOrderHeader TO SalesTeam;

-- Finance team can access pricing and financial data
GRANT SELECT ON SalesLT.Product TO FinanceTeam;
GRANT SELECT ON SalesLT.SalesOrderHeader TO FinanceTeam;
GRANT SELECT ON SalesLT.SalesOrderDetail TO FinanceTeam;

-- Read-only users can only view data
GRANT SELECT ON SCHEMA::SalesLT TO ReadOnlyUsers;
```

### **3.1.3. Adding Users to Roles**
```sql
-- Add existing users to roles
EXEC sp_addrolemember 'SalesTeam', 'john@company.com';
EXEC sp_addrolemember 'FinanceTeam', 'jane@company.com';
EXEC sp_addrolemember 'ReadOnlyUsers', 'guest@company.com';
```

---

## **3.2. Row-Level Security (RLS)**
RLS restricts which rows users can see based on filters.

### **3.2.1. Create a Security Predicate Function**
```sql
CREATE FUNCTION SalesLT.fn_securitypredicate(@CountryRegion AS NVARCHAR(50))
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN SELECT 1 AS fn_securitypredicate_result
WHERE @CountryRegion = USER_NAME() OR USER_NAME() = 'admin@company.com';
```

### **3.2.2. Create Security Policy**
```sql
CREATE SECURITY POLICY SalesLT.CountryFilter
ADD FILTER PREDICATE SalesLT.fn_securitypredicate(CountryRegion)
ON SalesLT.Customer,
ADD BLOCK PREDICATE SalesLT.fn_securitypredicate(CountryRegion)
ON SalesLT.Customer;
```

### **3.2.3. Test RLS**
```sql
-- Create test users
CREATE USER [CanadaUser] WITHOUT LOGIN;
CREATE USER [USUser] WITHOUT LOGIN;

-- Grant access
GRANT SELECT ON SalesLT.Customer TO [CanadaUser], [USUser];

-- Set user context and test
EXECUTE AS USER = 'CanadaUser';
SELECT * FROM SalesLT.Customer; -- Only sees Canadian customers
REVERT;

EXECUTE AS USER = 'USUser';
SELECT * FROM SalesLT.Customer; -- Only sees US customers
REVERT;
```

---

## **3.3. Column-Level Security**
Restrict access to sensitive columns.

### **3.3.1. Grant Partial Access**
```sql
-- Create view without sensitive columns
CREATE VIEW SalesLT.SafeCustomerView AS
SELECT 
    CustomerID,
    FirstName,
    LastName,
    CompanyName
FROM SalesLT.Customer;

-- Grant access to view instead of table
GRANT SELECT ON SalesLT.SafeCustomerView TO SalesTeam;
```

### **3.3.2. Dynamic Data Masking**
```sql
-- Add masking to sensitive columns
ALTER TABLE SalesLT.Customer
ALTER COLUMN EmailAddress ADD MASKED WITH (FUNCTION = 'email()');

ALTER TABLE SalesLT.Customer
ALTER COLUMN Phone ADD MASKED WITH (FUNCTION = 'partial(2, "XXX-XXX", 2)');

-- Test masking
EXECUTE AS USER = 'ReadOnlyUsers';
SELECT EmailAddress, Phone FROM SalesLT.Customer; -- Shows masked data
REVERT;
```

---

## **3.4. Auditing and Compliance**
Track who accesses what data.

### **3.4.1. Enable Database Auditing**
```sql
-- Create audit specification
CREATE DATABASE AUDIT SPECIFICATION [SalesDBAudit]
FOR SERVER AUDIT [FabricAudit]
ADD (SELECT, INSERT, UPDATE, DELETE ON SCHEMA::SalesLT BY PUBLIC),
ADD (EXECUTE ON DATABASE::AdventureWorksLT BY PUBLIC);
```

### **3.4.2. View Audit Logs**
```sql
SELECT 
    event_time,
    server_principal_name,
    object_name,
    statement
FROM sys.fn_get_audit_file('https://fabricaudit.blob.core.windows.net/logs/*', NULL, NULL);
```

---

## **3.5. Implementing a Complete Security Model**

### **3.5.1. Create Security Schema**
```sql
-- Schema for security objects
CREATE SCHEMA Security;
GO

-- Function to check department access
CREATE FUNCTION Security.fn_department_access(@DeptName NVARCHAR(50))
RETURNS BIT
AS
BEGIN
    IF IS_MEMBER(@DeptName) = 1 OR IS_SRVROLEMEMBER('sysadmin') = 1
        RETURN 1;
    RETURN 0;
END;
```

### **3.5.2. Secure Stored Procedures**
```sql
CREATE PROCEDURE SalesLT.GetCustomerOrders
WITH EXECUTE AS OWNER
AS
BEGIN
    IF Security.fn_department_access('SalesTeam') = 1
        SELECT * FROM SalesLT.SalesOrderHeader;
    ELSE
        RAISERROR('Access denied', 16, 1);
END;
```

---

## **3.6. Key Takeaways**
✅ **RBAC** - Control access by organizational roles  
✅ **Row-Level Security** - Filter data by user attributes  
✅ **Column Security** - Protect sensitive columns with views or masking  
✅ **Auditing** - Track all database access for compliance  
✅ **Defense-in-Depth** - Combine multiple security layers  

---
# **Exercise 4: Performance Tuning in Microsoft Fabric SQL Database**

In this comprehensive performance tuning lab, you'll optimize your SQL database in Microsoft Fabric through:

## **4.1. Indexing Strategies**
### **4.1.1 Creating Basic Indexes**
```sql
-- Create clustered index on primary key (if not existing)
CREATE CLUSTERED INDEX IX_Customer_CustomerID 
ON SalesLT.Customer(CustomerID);

-- Create nonclustered indexes for frequent queries
CREATE NONCLUSTERED INDEX IX_Product_ProductCategoryID
ON SalesLT.Product(ProductCategoryID)
INCLUDE (Name, ListPrice);

CREATE NONCLUSTERED INDEX IX_SalesOrderHeader_CustomerID_OrderDate
ON SalesLT.SalesOrderHeader(CustomerID, OrderDate DESC)
INCLUDE (TotalDue);
```

### **4.1.2 Indexing for JOIN Operations**
```sql
-- Covering index for common join patterns
CREATE NONCLUSTERED INDEX IX_SalesOrderDetail_ProductID
ON SalesLT.SalesOrderDetail(ProductID)
INCLUDE (OrderQty, UnitPrice, UnitPriceDiscount);
```

### **4.1.3 Filtered Indexes**
```sql
-- Optimize for active products
CREATE NONCLUSTERED INDEX IX_Product_Active
ON SalesLT.Product(ProductCategoryID, ListPrice)
WHERE DiscontinuedDate IS NULL;
```

## **4.2. Query Performance Analysis**
### **4.2.1 Using Execution Plans**
```sql
-- Enable actual execution plan (Ctrl+M)
SELECT 
    p.Name,
    SUM(sod.LineTotal) AS TotalSales
FROM SalesLT.SalesOrderDetail sod
JOIN SalesLT.Product p ON sod.ProductID = p.ProductID
GROUP BY p.Name
ORDER BY TotalSales DESC;
```

### **4.2.2 Query Store Analysis**
```sql
-- Check top resource-consuming queries
SELECT 
    qt.query_sql_text,
    qrs.avg_duration,
    qrs.avg_logical_io_reads
FROM sys.query_store_query qq
JOIN sys.query_store_query_text qt ON qq.query_text_id = qt.query_text_id
JOIN sys.query_store_plan qp ON qq.query_id = qp.query_id
JOIN sys.query_store_runtime_stats qrs ON qp.plan_id = qrs.plan_id
ORDER BY qrs.avg_duration DESC;
```

## **4.3. Partitioning Large Tables**
### **4.3.1 Creating Partition Function**
```sql
-- Partition SalesOrderHeader by year
CREATE PARTITION FUNCTION pf_OrderDateYear(DATE)
AS RANGE RIGHT FOR VALUES 
    ('2019-01-01', '2020-01-01', '2021-01-01', '2022-01-01');
```

### **4.3.2 Creating Partition Scheme**
```sql
CREATE PARTITION SCHEME ps_OrderDateYear
AS PARTITION pf_OrderDateYear
ALL TO ([PRIMARY]);
```

### **4.3.3 Rebuilding Table with Partitioning**
```sql
-- Create partitioned clustered index
CREATE CLUSTERED INDEX IX_SalesOrderHeader_OrderDate
ON SalesLT.SalesOrderHeader(OrderDate)
ON ps_OrderDateYear(OrderDate);
```

## **4.4. Statistics and Cardinality Estimation**
### **4.4.1 Updating Statistics**
```sql
-- Update statistics with full scan
UPDATE STATISTICS SalesLT.Product WITH FULLSCAN;

-- Create filtered statistics
CREATE STATISTICS stats_Product_HighPrice
ON SalesLT.Product(ListPrice)
WHERE ListPrice > 1000;
```

### **4.4.2 Optimizer Hints**
```sql
-- Force a specific join type
SELECT p.Name, sod.SalesOrderID
FROM SalesLT.Product p
INNER LOOP JOIN SalesLT.SalesOrderDetail sod
    ON p.ProductID = sod.ProductID
OPTION (MAXDOP 4);
```

## **4.5. In-Memory Optimization**
### **4.5.1 Memory-Optimized Tables**
```sql
-- Create memory-optimized version of ShoppingCart
CREATE TABLE SalesLT.ShoppingCart (
    CartID INT IDENTITY PRIMARY KEY NONCLUSTERED,
    CustomerID INT NOT NULL,
    ProductID INT NOT NULL,
    Quantity INT NOT NULL,
    DateCreated DATETIME2 NOT NULL,
    INDEX IX_CustomerID HASH (CustomerID) WITH (BUCKET_COUNT = 100000)
) WITH (MEMORY_OPTIMIZED = ON, DURABILITY = SCHEMA_AND_DATA);
```

### **4.5.2 Natively Compiled Stored Procedures**
```sql
CREATE PROCEDURE SalesLT.usp_AddToCart
    @CustomerID INT,
    @ProductID INT,
    @Quantity INT
WITH NATIVE_COMPILATION, SCHEMABINDING
AS
BEGIN ATOMIC WITH
    (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = 'us_english')
    
    INSERT INTO SalesLT.ShoppingCart
    VALUES (@CustomerID, @ProductID, @Quantity, GETUTCDATE());
END;
```

## **4.6. Monitoring and Maintenance**
### **4.6.1 Automated Index Maintenance**
```sql
-- Create index maintenance procedure
CREATE PROCEDURE dbo.usp_IndexMaintenance
AS
BEGIN
    -- Rebuild indexes with >30% fragmentation
    DECLARE @SQL NVARCHAR(MAX) = '';
    
    SELECT @SQL = @SQL + 
        'ALTER INDEX ' + i.name + ' ON ' + OBJECT_NAME(i.object_id) + 
        CASE WHEN avg_fragmentation_in_percent > 30 
             THEN ' REBUILD' 
             ELSE ' REORGANIZE' END + ';' + CHAR(13)
    FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'LIMITED') ps
    JOIN sys.indexes i ON ps.object_id = i.object_id AND ps.index_id = i.index_id
    WHERE avg_fragmentation_in_percent > 10;
    
    EXEC sp_executesql @SQL;
END;
```

### **4.6.2 Performance Dashboard Queries**
```sql
-- Top 10 CPU-intensive queries
SELECT TOP 10
    q.query_id,
    qt.query_sql_text,
    qrs.avg_cpu_time,
    qrs.last_cpu_time
FROM sys.query_store_query q
JOIN sys.query_store_query_text qt ON q.query_text_id = qt.query_text_id
JOIN sys.query_store_plan qp ON q.query_id = qp.query_id
JOIN sys.query_store_runtime_stats qrs ON qp.plan_id = qrs.plan_id
ORDER BY qrs.avg_cpu_time DESC;
```

## **Key Performance Concepts Demonstrated**
✅ **Strategic Indexing** - Right indexes for your workload  
✅ **Query Optimization** - Execution plans and hints  
✅ **Advanced Partitioning** - Manage large datasets  
✅ **In-Memory OLTP** - High-throughput scenarios  
✅ **Automated Maintenance** - Keep performance consistent  

# **Exercise 5: Creating a Semantic Model in Microsoft Fabric** (This portion is completely option, you need not continue if needed)

This comprehensive lab will guide you through building an enterprise-ready semantic model in Microsoft Fabric, transforming your SQL database into a powerful analytics engine.

## **1. Setting Up the Semantic Model**

### **1.1 Creating a New Semantic Model**
1. Navigate to your Fabric workspace
2. Select **New** → **Semantic model**
3. Choose **Import from SQL database** and select your AdventureWorksLT database
4. Name your model "AdventureWorks Analytics"

![Creating a new semantic model in Fabric](Images/new-semantic-model.png)

### **1.2 Initial Data Import**
```powerquery
let
    Source = Sql.Database("your-server.database.windows.net", "AdventureWorksLT"),
    SalesLT = Source{[Schema="SalesLT"]}[Data]
in
    SalesLT
```

## **2. Dimensional Modeling Best Practices**

### **2.1 Creating a Star Schema**

**Dimension Tables:**
```powerquery
// Products Dimension
let
    Source = Sql.Database("your-server.database.windows.net", "AdventureWorksLT"),
    Product = Source{[Schema="SalesLT",Item="Product"]}[Data],
    ProductCategory = Source{[Schema="SalesLT",Item="ProductCategory"]}[Data],
    Merged = Table.NestedJoin(Product, {"ProductCategoryID"}, ProductCategory, {"ProductCategoryID"}, "ProductCategory", JoinKind.LeftOuter),
    Expanded = Table.ExpandTableColumn(Merged, "ProductCategory", {"Name"}, {"CategoryName"}),
    RemovedColumns = Table.RemoveColumns(Expanded,{"ProductCategoryID"}),
    Renamed = Table.RenameColumns(RemovedColumns, {{"Name", "ProductName"}})
in
    Renamed
```

**Fact Tables:**
```powerquery
// Sales Fact
let
    Source = Sql.Database("your-server.database.windows.net", "AdventureWorksLT"),
    Header = Source{[Schema="SalesLT",Item="SalesOrderHeader"]}[Data],
    Detail = Source{[Schema="SalesLT",Item="SalesOrderDetail"]}[Data],
    Merged = Table.NestedJoin(Header, {"SalesOrderID"}, Detail, {"SalesOrderID"}, "Details", JoinKind.Inner),
    Expanded = Table.ExpandTableColumn(Merged, "Details", {"ProductID", "OrderQty", "UnitPrice", "UnitPriceDiscount", "LineTotal"}, {"ProductID", "Quantity", "UnitPrice", "Discount", "LineTotal"})
in
    Expanded
```

### **2.2 Implementing Hierarchies**
1. In Model view, create these hierarchies:
   - **Product Hierarchy**: Category → Product Name
   - **Date Hierarchy**: Year → Quarter → Month → Day
   - **Geography Hierarchy**: Country → State → City

2. Set appropriate data types for each column

## **3. Advanced DAX Measures**

### **3.1 Core Business Metrics**
```dax
// Sales Metrics
Total Sales = SUM(Sales[LineTotal])

YTD Sales = 
TOTALYTD(
    [Total Sales],
    'Date'[Date],
    "12/31"
)

YoY Growth = 
VAR CurrentYear = [Total Sales]
VAR PriorYear = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date]))
RETURN
    DIVIDE(CurrentYear - PriorYear, PriorYear)

// Inventory Metrics
Days Inventory = 
DIVIDE(
    AVERAGE(Inventory[QuantityOnHand]),
    [Daily Sales],
    0
)
```

### **3.2 Time Intelligence**
```dax
// Rolling 30-Day Sales
Rolling 30 Days = 
CALCULATE(
    [Total Sales],
    DATESINPERIOD(
        'Date'[Date],
        MAX('Date'[Date]),
        -30,
        DAY
    )
)

// Same Store Sales
Same Store Sales = 
CALCULATE(
    [Total Sales],
    FILTER(
        ALL('Store'),
        [First Sale Date] <= MIN('Date'[Date]) && 
        NOT ISBLANK([First Sale Date])
    )
)
```

## **4. Performance Optimization**

### **4.1 Aggregations Setup**
1. Create aggregation tables:
   ```sql
   CREATE TABLE Aggregates.DailySales (
       DateKey INT PRIMARY KEY,
       TotalSales DECIMAL(18,2),
       OrderCount INT
   );
   ```
   
2. Configure in Fabric:
   - Navigate to **Model** → **Aggregations**
   - Map base tables to aggregation tables
   - Set storage mode as "Dual" for critical tables

### **4.2 Partitioning Strategy**
```powerquery
// Incremental refresh setup
let
    Source = Sql.Database("your-server.database.windows.net", "AdventureWorksLT"),
    Sales = Source{[Schema="SalesLT",Item="SalesOrderHeader"]}[Data],
    Filtered = Table.SelectRows(Sales, each [OrderDate] >= RangeStart and [OrderDate] < RangeEnd)
in
    Filtered
```

## **5. Security Implementation**

### **5.1 Row-Level Security (RLS)**
```dax
// Region Security Filter
[Region Access] = 
SWITCH(
    USERNAME(),
    "east_manager@company.com", "East",
    "west_manager@company.com", "West",
    "ALL"  // Admin sees all
)
```

### **5.2 Object-Level Security**
1. Create roles:
   - **Sales Managers**: Full access to sales data
   - **Inventory Team**: Only product and inventory tables
   - **Executives**: Read-only access to all data

2. Configure in Fabric portal under **Model security**

## **6. Deployment Pipeline**

### **6.1 CI/CD Setup**
1. Create three environments:
   - **Development** (for modeling)
   - **Test** (for validation)
   - **Production** (for end-users)

2. Configure deployment pipeline in Fabric:
   ```powershell
   # Sample deployment script
   fabric-cli model deploy \
       --source-workspace "Dev-Workspace" \
       --target-workspace "Prod-Workspace" \
       --model "AdventureWorks Analytics" \
       --overwrite
   ```

## **7. Monitoring and Usage Analytics**

### **7.1 Built-in Monitoring**
1. Access the **Metrics** tab to track:
   - Query performance
   - Refresh times
   - User activity

2. Set up alerts for:
   - Failed refreshes
   - Long-running queries
   - Storage threshold breaches

### **7.2 Advanced Telemetry**
```kusto
// Sample KQL query for usage analysis
SemanticModelEvents
| where Timestamp > ago(30d)
| where OperationName == "QueryExecution"
| summarize 
    AvgDuration=avg(DurationMs),
    QueryCount=count()
    by bin(Timestamp, 1d), UserName
| render timechart
```

## **Key Takeaways**
✅ **Star Schema Design** - Optimized for analytics  
✅ **Advanced DAX** - Powerful business calculations  
✅ **Performance Tuning** - Aggregations and partitioning  
✅ **Enterprise Security** - RLS and object-level controls  
✅ **DevOps Integration** - Professional deployment pipelines  

## Cleanup

When finished, remove your workspace:

1. Navigate to workspace settings
2. Select **Remove this workspace**

## Conclusion

In this lab, you've:
- Created a SQL database in Microsoft Fabric
- Executed analytical queries
- Integrated external data sources
- Implemented data security controls

This demonstrates how Microsoft Fabric provides a comprehensive platform for data management and analytics.





