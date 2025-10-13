Designing effective full and incremental loading patterns is absolutely fundamental to building efficient and scalable data warehouses.

Let's dive deep into this with a real-world case study, covering the design and implementation in Microsoft Fabric using T-SQL in the Synapse Data Warehouse.

### Case Study: Syncing an E-Commerce Database

**Source System:** An operational **Azure SQL Database** that powers an e-commerce website. This is a classic Online Transaction Processing (OLTP) system.

**Target System:** A **Synapse Data Warehouse** in Microsoft Fabric, designed for analytics (OLAP).

**Objective:**
We need to load two key tables from the source database into our Data Warehouse:
1.  **`Products` Table:** A dimension table containing product information (name, category, price). This is a "Type 1" dimension, meaning changes to a product (e.g., a price update) should overwrite the existing record.
2.  **`Orders` Table:** A fact table containing transaction records. This table is append-only; new orders are always added, and old ones are never updated.

We need a robust process to perform an initial **full load** to populate the warehouse, followed by daily **incremental loads** to keep it synchronized with the source.

### End-to-End Design: The High-Watermark Pattern

For incremental loads, one of the most common and reliable methods is the **High-Watermark Pattern**.

**How it works:**
1.  We store the maximum value of a "last updated" timestamp or an incrementing ID from our last successful load. This value is our "high-watermark."
2.  For the next incremental load, we query the source system for only the records that have a timestamp or ID **greater than** our stored high-watermark.
3.  After a successful load, we update the high-watermark to the maximum value from the data we just processed.

This ensures we only process new or updated records, making our daily loads fast and efficient.

**Architectural Diagram:**

```
[Azure SQL DB (Source)]
     |
     | (Fabric Pipeline)
     v
[Copy Data Activity] --(reads from source)--> [Staging Area in Lakehouse/Warehouse]
                                                       |
                                                       | (Stored Procedure)
                                                       v
                                            [Synapse Data Warehouse (Target)]
                                                 |       |
                                                 |       +-----> [DimProducts]
                                                 |       +-----> [FactOrders]
                                                 |
                                                 +-----> [ControlTable_Watermarks]
```

### Step-by-Step Implementation in Microsoft Fabric

#### Step 1: Set Up the Target Warehouse and Control Table

First, let's create the target tables and our control table in the Synapse Data Warehouse.

**Connect to your Warehouse and run this T-SQL:**

```sql
-- 1. Create the target Dimension table for Products
CREATE TABLE dbo.DimProducts (
    ProductID INT PRIMARY KEY,
    ProductName VARCHAR(255) NOT NULL,
    Category VARCHAR(100),
    UnitPrice DECIMAL(10, 2),
    LastModifiedDate DATETIME2
);

-- 2. Create the target Fact table for Orders
CREATE TABLE dbo.FactOrders (
    OrderID INT PRIMARY KEY,
    OrderDate DATETIME2 NOT NULL,
    ProductID INT,
    Quantity INT,
    TotalAmount DECIMAL(18, 2)
);

-- 3. Create the Control Table to store our high-watermarks
CREATE TABLE dbo.ControlTable_Watermarks (
    TableName VARCHAR(128) PRIMARY KEY,
    HighWatermarkValue DATETIME2
);

-- Initialize the watermark values to a very old date to ensure the first load is a full load
INSERT INTO dbo.ControlTable_Watermarks (TableName, HighWatermarkValue)
VALUES
('Products', '1900-01-01'),
('Orders', '1900-01-01');
```

#### Step 2: Full Load Implementation

The initial full load will bring all existing data into the warehouse. Our watermark table is initialized to `1900-01-01`, so our first "incremental" run will naturally act as a full load because all source data will be "newer" than the watermark.

We will use a **Stored Procedure** to contain the merging logic. This is a best practice as it's reusable, secure, and easy to manage.

**Create the Stored Procedure for `Products` (Type 1 Dimension):**

The `MERGE` statement is the perfect tool for this. It can `INSERT` new rows, `UPDATE` existing rows, and even `DELETE` rows in the target that have been removed from the source (though we won't implement deletes here for simplicity).

```sql
CREATE PROCEDURE sp_Merge_DimProducts
AS
BEGIN
    SET NOCOUNT ON;

    -- The MERGE statement is the heart of the incremental load for Type 1 dimensions
    MERGE dbo.DimProducts AS Target
    USING stg_Products AS Source  -- We assume data has been copied to a staging table 'stg_Products'
    ON (Target.ProductID = Source.ProductID)

    -- For records that match on ProductID, UPDATE them if the data has changed
    WHEN MATCHED AND Target.LastModifiedDate < Source.LastModifiedDate THEN
        UPDATE SET
            Target.ProductName = Source.ProductName,
            Target.Category = Source.Category,
            Target.UnitPrice = Source.UnitPrice,
            Target.LastModifiedDate = Source.LastModifiedDate

    -- For records in the Source that are NOT IN the Target, INSERT them
    WHEN NOT MATCHED BY TARGET THEN
        INSERT (ProductID, ProductName, Category, UnitPrice, LastModifiedDate)
        VALUES (Source.ProductID, Source.ProductName, Source.Category, Source.UnitPrice, Source.LastModifiedDate);

    -- After the merge, update the watermark table with the highest LastModifiedDate from this run
    UPDATE dbo.ControlTable_Watermarks
    SET HighWatermarkValue = (SELECT MAX(LastModifiedDate) FROM dbo.stg_Products)
    WHERE TableName = 'Products';

END;
```

**Create the Stored Procedure for `Orders` (Append-Only Fact):**

Since this table is append-only, we only need to `INSERT` new records. A simple `INSERT...SELECT` is more efficient than a `MERGE`.

```sql
CREATE PROCEDURE sp_Insert_FactOrders
AS
BEGIN
    SET NOCOUNT ON;

    -- Insert only the new records from the staging table
    INSERT INTO dbo.FactOrders (OrderID, OrderDate, ProductID, Quantity, TotalAmount)
    SELECT
        s.OrderID,
        s.OrderDate,
        s.ProductID,
        s.Quantity,
        s.TotalAmount
    FROM
        stg_Orders AS s
    LEFT JOIN
        dbo.FactOrders AS t ON s.OrderID = t.OrderID
    WHERE
        t.OrderID IS NULL; -- Ensure we only insert rows that do not already exist

    -- Update the watermark with the highest OrderDate from this run
    UPDATE dbo.ControlTable_Watermarks
    SET HighWatermarkValue = (SELECT MAX(OrderDate) FROM dbo.stg_Orders)
    WHERE TableName = 'Orders';

END;
```

#### Step 3: Incremental Load Implementation using a Fabric Pipeline

Now, we'll build a Fabric Data Pipeline to automate this process.

1.  **Create a New Pipeline:** In your Fabric workspace, create a `Data pipeline`.

2.  **Get the Old High-Watermark:**
    *   Add a **Lookup** activity named `Get_Products_Watermark`.
    *   Configure it to query your Data Warehouse.
    *   Use the query: `SELECT HighWatermarkValue FROM dbo.ControlTable_Watermarks WHERE TableName = 'Products'`.
    *   **Crucially, uncheck "First row only".**

3.  **Copy New/Updated Products Data:**
    *   Add a **Copy data** activity named `Copy_New_Products`.
    *   **Source:**
        *   Connection: Your Azure SQL Database.
        *   Use query: `SELECT * FROM dbo.Products WHERE LastModifiedDate > '@{activity('Get_Products_Watermark').output.firstRow.HighWatermarkValue}'`
        *   This dynamic expression injects the watermark value from the previous step, ensuring you only copy new/changed data.
    *   **Sink:**
        *   Connection: Your Fabric Warehouse.
        *   Select "Table" and specify a staging table name, e.g., `stg_Products`.
        *   Set the "Table option" to **"Recreate"** to ensure the staging table is fresh for each run.

4.  **Execute the Merge Logic:**
    *   Add a **Stored Procedure** activity named `Execute_sp_Merge_Products`.
    *   Configure it to connect to your Fabric Warehouse and execute `sp_Merge_DimProducts`.
    *   Drag a success dependency (green arrow) from the `Copy_New_Products` activity to this one.

**Repeat steps 2-4 for the `Orders` table**, using the `Orders` watermark and the `sp_Insert_FactOrders` stored procedure.

**Pipeline Flow:**

```
[Get_Products_Watermark] -> [Copy_New_Products] -> [Execute_sp_Merge_Products]

[Get_Orders_Watermark]   -> [Copy_New_Orders]   -> [Execute_sp_Insert_FactOrders]
```
*(These two flows can run in parallel)*

5.  **Schedule the Pipeline:**
    *   Add a **Schedule trigger** to the pipeline to run it daily at a specific time (e.g., 2 AM).

### Putting It All Together: A Walkthrough

*   **Day 1 (Full Load):**
    1.  The pipeline runs for the first time.
    2.  The `Get_Products_Watermark` activity fetches `'1900-01-01'`.
    3.  The `Copy_New_Products` activity runs `SELECT * FROM dbo.Products WHERE LastModifiedDate > '1900-01-01'`, copying **all** product data to the `stg_Products` table.
    4.  `sp_Merge_DimProducts` runs. Since the target `DimProducts` is empty, the `WHEN NOT MATCHED` clause inserts all rows.
    5.  Finally, the procedure updates the watermark table with the latest `LastModifiedDate` from the source (e.g., `'2023-11-16'`).
    6.  The same process happens for the `Orders` table.

*   **Day 2 (Incremental Load):**
    1.  The pipeline runs again.
    2.  `Get_Products_Watermark` now fetches `'2023-11-16'`.
    3.  `Copy_New_Products` runs `SELECT * FROM dbo.Products WHERE LastModifiedDate > '2023-11-16'`, copying **only the products that were added or changed today**.
    4.  `sp_Merge_DimProducts` runs. It uses the `WHEN MATCHED` clause to update prices for existing products and the `WHEN NOT MATCHED` clause to insert brand-new products.
    5.  The watermark is updated to today's date (e.g., `'2023-11-17'`).

This pattern provides a robust, repeatable, and efficient method for synchronizing your operational data with your analytical warehouse in Microsoft Fabric.
