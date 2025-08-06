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

1. Create a table from your file:
   - Select the sales.csv file
   - Choose **Load to Tables** > **New table**
   - Name the table "sales"

2. Verify table creation:
   - Refresh the Tables folder if needed
   - View the table data and underlying files

3. Query the table using SQL:
   - Switch to the SQL analytics endpoint
   - Create a new SQL query
   - Run a revenue calculation query:
     ```sql
     SELECT Item, SUM(Quantity * UnitPrice) AS Revenue
     FROM sales
     GROUP BY Item
     ORDER BY Revenue DESC;
     ```

4. Create a visual query:
   - Build a query showing line items per sales order
   - Group by SalesOrderNumber
   - Count distinct SalesOrderLineNumber values

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
