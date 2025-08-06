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

1. Create a table for public holidays data:

```sql
CREATE TABLE SalesLT.PublicHolidays (
    CountryOrRegion NVARCHAR(50),
    HolidayName NVARCHAR(100),
    Date DATE,
    IsPaidTimeOff BIT
);
```

2. Populate the holidays table:

```sql
INSERT INTO SalesLT.PublicHolidays VALUES
    ('Canada', 'Victoria Day', '2024-02-19', 1),
    ('United Kingdom', 'Christmas Day', '2024-12-25', 1),
    ('United Kingdom', 'Spring Bank Holiday', '2024-05-27', 1),
    ('United States', 'Thanksgiving Day', '2024-11-28', 1);
```

3. Add sample addresses and orders:

```sql
INSERT INTO SalesLT.Address (AddressLine1, City, StateProvince, CountryRegion, PostalCode, rowguid, ModifiedDate)
VALUES
    ('123 Main St', 'Seattle', 'WA', 'United States', '98101', NEWID(), GETDATE()),
    ('456 Maple Ave', 'Toronto', 'ON', 'Canada', 'M5H 2N2', NEWID(), GETDATE()),
    ('789 Oak St', 'London', 'England', 'United Kingdom', 'EC1A 1BB', NEWID(), GETDATE());

INSERT INTO SalesLT.SalesOrderHeader VALUES
    (1001, 1, '2024-12-25', '2024-12-30', '2024-12-26', 1, 1, 'PO12345', 'AN123', 1, 
     (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '789 Oak St'), 
     (SELECT TOP 1 AddressID FROM SalesLT.Address WHERE AddressLine1 = '123 Main St'), 
     'Ground', '12345', 100.00, 10.00, 5.00, 'New Order 1', NEWID(), GETDATE()),
    -- Additional orders omitted for brevity
```

4. Analyze holiday sales:

```sql
SELECT DISTINCT soh.SalesOrderID, soh.OrderDate, ph.HolidayName, ph.CountryOrRegion
FROM SalesLT.SalesOrderHeader AS soh
INNER JOIN SalesLT.Address a ON a.AddressID = soh.ShipToAddressID
INNER JOIN SalesLT.PublicHolidays AS ph ON soh.OrderDate = ph.Date AND a.CountryRegion = ph.CountryOrRegion
```

### Exercise 3: Implementing Security

1. Create a filtered view:

```sql
CREATE VIEW SalesLT.vw_SalesOrderHoliday AS
SELECT DISTINCT soh.SalesOrderID, soh.OrderDate, ph.HolidayName, ph.CountryOrRegion
FROM SalesLT.SalesOrderHeader AS soh
INNER JOIN SalesLT.Address a ON a.AddressID = soh.ShipToAddressID
INNER JOIN SalesLT.PublicHolidays AS ph ON soh.OrderDate = ph.Date AND a.CountryRegion = ph.CountryOrRegion
WHERE a.CountryRegion = 'United Kingdom';
```

2. Set up role-based access:

```sql
CREATE ROLE SalesOrderRole;
GRANT SELECT ON SalesLT.vw_SalesOrderHoliday TO SalesOrderRole;
```

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

