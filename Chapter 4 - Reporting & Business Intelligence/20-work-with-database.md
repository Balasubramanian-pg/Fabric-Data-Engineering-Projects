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

### Exercise 1: Querying the Database

1. In your AdventureWorksLT database, click **New query**
2. Run this query to analyze product pricing:

```sql
SELECT 
    p.Name AS ProductName,
    pc.Name AS CategoryName,
    p.ListPrice
FROM 
    SalesLT.Product p
INNER JOIN 
    SalesLT.ProductCategory pc ON p.ProductCategoryID = pc.ProductCategoryID
ORDER BY 
    p.ListPrice DESC;
```

3. Run this query to examine customer orders:

```sql
SELECT 
    c.FirstName,
    c.LastName,
    soh.OrderDate,
    soh.SubTotal
FROM 
    SalesLT.Customer c
INNER JOIN 
    SalesLT.SalesOrderHeader soh ON c.CustomerID = soh.CustomerID
ORDER BY 
    soh.OrderDate DESC;
```

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
