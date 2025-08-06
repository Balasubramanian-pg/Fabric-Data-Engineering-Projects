# Lab: Create a Microsoft Fabric Lakehouse

## Introduction

In this lab, you'll explore how to create and work with a lakehouse in Microsoft Fabric. A lakehouse combines the best features of data lakes and data warehouses, providing scalable file storage with relational table capabilities.

## Objectives

By completing this lab, you will:
- Create a Microsoft Fabric workspace
- Build a lakehouse and understand its components
- Upload and manage data files
- Create tables from files
- Query data using SQL and visual tools
- Create a basic Power BI report

## Estimated Time

30 minutes

## Prerequisites

- Microsoft Fabric trial license
- Web browser with internet access

## Exercise 1: Create a Workspace

1. Navigate to [Microsoft Fabric](https://app.fabric.microsoft.com)
2. Sign in with your credentials
3. Select **Workspaces** from the left menu
4. Create a new workspace:
   - Choose a unique name
   - Select a licensing mode with Fabric capacity (Trial, Premium, or Fabric)

## Exercise 2: Create a Lakehouse

1. In your new workspace:
   - Select **Create** from the left menu
   - Choose **Lakehouse** under Data Engineering
   - Provide a unique name and create

2. Explore the lakehouse interface:
   - **Tables** folder: Contains Delta Lake format tables
   - **Files** folder: Stores raw data files and shortcuts

## Exercise 3: Upload and Manage Data

1. Download the sample data file:
   - Get [sales.csv](https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/sales.csv)
   - Save locally as "sales.csv"

2. In your lakehouse:
   - Create a new subfolder named "data"
   - Upload the sales.csv file to this folder

3. Explore shortcut options:
   - View available data source types
   - Note this capability for external data integration

## Exercise 4: Create and Query Tables

## 4.1 Create a Managed Table from Files

1. **Navigate to your uploaded file**:
   - In the Lakehouse explorer, expand the **Files** section
   - Open the **data** folder where you uploaded sales.csv
   - Right-click on **sales.csv** and select **Load to Tables** > **New table**

2. **Configure table creation**:
   - In the dialog box, name the table "sales"
   - Verify the file path is correct (/Files/data/sales.csv)
   - Review the automatic schema detection:
     - Confirm column names (SalesOrderNumber, SalesOrderLineNumber, etc.)
     - Verify data types (string, integer, decimal, etc.)
   - Click **Load** to create the table

3. **Understand what happens behind the scenes**:
   - Fabric creates a Delta Lake format table
   - Data is written to the Tables section in Parquet format
   - Transaction logs are created in the _delta_log folder
   - The table is registered in the metastore

## 4.2 Explore Table Properties

1. **View table details**:
   - Right-click the sales table and select **Properties**
   - Note the table format (Delta Lake)
   - Review the schema with column names and data types
   - Check the table location in OneLake

2. **Examine the physical files**:
   - Right-click the sales table and select **View files**
   - Observe the Parquet files containing your data
   - Explore the _delta_log folder containing transaction logs
   - Note the automatic partitioning (if any)

## 4.3 Advanced SQL Querying

1. **Basic query patterns**:
   ```sql
   -- Simple SELECT with filtering
   SELECT * FROM sales WHERE Quantity > 10;
   
   -- Aggregation with HAVING clause
   SELECT Item, COUNT(*) AS OrderCount
   FROM sales
   GROUP BY Item
   HAVING COUNT(*) > 5;
   ```

2. **Date operations**:
   ```sql
   -- Extract month/year from OrderDate (if present)
   SELECT 
     DATEPART(year, OrderDate) AS OrderYear,
     DATEPART(month, OrderDate) AS OrderMonth,
     SUM(UnitPrice * Quantity) AS MonthlyRevenue
   FROM sales
   GROUP BY DATEPART(year, OrderDate), DATEPART(month, OrderDate)
   ORDER BY OrderYear, OrderMonth;
   ```

3. **Window functions**:
   ```sql
   -- Running totals by date
   SELECT 
     SalesOrderNumber,
     OrderDate,
     UnitPrice * Quantity AS OrderValue,
     SUM(UnitPrice * Quantity) OVER (ORDER BY OrderDate) AS RunningTotal
   FROM sales;
   ```

## 4.4 Visual Query Deep Dive

1. **Create complex transformations**:
   - Add a calculated column for line item value
   - Create conditional columns for product categories
   - Implement error handling for data quality issues

2. **Advanced grouping**:
   - Create nested groupings (by region then by product)
   - Add multiple aggregation methods (sum, average, count distinct)
   - Configure custom aggregation formulas

3. **Query performance optimization**:
   - Use query folding indicators
   - Review query execution steps
   - Apply early filtering to improve performance

## 4.5 Delta Table Features

1. **Time travel queries**:
   ```sql
   -- Query previous versions of the table
   SELECT * FROM sales VERSION AS OF 1;
   
   -- Query at specific timestamp
   SELECT * FROM sales TIMESTAMP AS OF '2023-10-01';
   ```

2. **Table maintenance**:
   ```sql
   -- Optimize the table
   OPTIMIZE sales;
   
   -- Clean up old versions
   VACUUM sales;
   ```

3. **Schema evolution**:
   ```sql
   -- Add new column
   ALTER TABLE sales ADD COLUMN DiscountAmount DECIMAL(10,2);
   
   -- Modify column type
   ALTER TABLE sales ALTER COLUMN Quantity TYPE BIGINT;
   ```

## 4.6 Practical Challenges

1. **Data quality check**:
   - Write queries to identify:
     - Negative quantities or prices
     - Missing order numbers
     - Duplicate line items

2. **Business analysis**:
   - Calculate customer lifetime value
   - Identify best-selling products by region
   - Analyze sales trends over time

3. **Performance comparison**:
   - Compare query performance between:
     - Direct file queries vs. managed tables
     - SQL endpoint vs. visual queries
     - Different query formulations

## Best Practices

1. **Table design**:
   - Use appropriate data types
   - Consider partitioning strategies
   - Implement naming conventions

2. **Query optimization**:
   - Filter early in queries
   - Limit returned columns
   - Use appropriate join strategies

3. **Data governance**:
   - Document table schemas
   - Implement data lineage tracking
   - Set up access controls

This expanded exercise provides a comprehensive exploration of table creation, management, and querying in a Fabric lakehouse, giving you practical experience with both basic and advanced features.

## Exercise 5: Create a Report

1. Examine the default semantic model:
   - View the model layout
   - Note the automatic inclusion of lakehouse tables

2. Create a new report:
   - Add a visualization showing items and quantities
   - Convert to a clustered bar chart
   - Save as "Item Sales Report"

## Verification Steps

Confirm your workspace contains:
1. The lakehouse
2. SQL analytics endpoint
3. Default semantic model
4. Item Sales Report

## Clean Up

1. Navigate to workspace settings
2. Select **Remove this workspace**
3. Confirm deletion

## Summary

In this lab, you've:
- Created a complete lakehouse solution
- Worked with both files and tables
- Explored multiple query methods
- Built a simple report
- Gained hands-on experience with Microsoft Fabric's lakehouse capabilities
