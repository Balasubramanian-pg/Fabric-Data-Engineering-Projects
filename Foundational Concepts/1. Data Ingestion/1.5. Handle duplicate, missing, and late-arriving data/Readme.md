Handling imperfect data is not just a best practice; it's a daily reality for any data professional. Duplicate, missing, and late-arriving data can corrupt analytics, skew machine learning models, and erode trust in your data platform.

Let's break down how to handle each of these challenges in Microsoft Fabric, using our e-commerce case study with practical examples in both T-SQL and Spark.

### Case Study Recap

**Source:** An operational e-commerce database.
**Target:** A Fabric Lakehouse and Warehouse.
**Data:** We are processing `Orders` data, which includes `OrderID`, `OrderDate`, `CustomerID`, and `TotalAmount`.

### Challenge 1: Handling Duplicate Data

Duplicates can occur for many reasons: network retries, application bugs, or re-running a data load pipeline. The goal is to ensure each unique record appears only once in our final "Gold" tables.

#### The Problem

Imagine our staging table `stg_Orders` receives the following data for today's load. Notice `OrderID` 1006 is a duplicate.

| OrderID | OrderDate  | CustomerID | TotalAmount |
| :------ | :--------- | :--------- | :---------- |
| 1006    | 2023-11-18 | 201        | 50.00       |
| **1006**| **2023-11-18**| **201**  | **50.00**   |
| 1007    | 2023-11-18 | 202        | 150.00      |

If we simply `INSERT` this data, our analytics will be wrong (e.g., `SUM(TotalAmount)` will be inflated).

#### Solution: Deduplication using Window Functions

The best way to handle this is to identify and remove duplicates *before* loading the data into the final target table. We can use the `ROW_NUMBER()` window function to assign a unique rank to each record within a group of duplicates.

#### Method 1: T-SQL in a Stored Procedure (Warehouse)

This is a common pattern inside a stored procedure that processes a staging table.

```sql
-- This logic would be inside a stored procedure like sp_Insert_FactOrders
-- It assumes data is already in a staging table: stg_Orders

-- Step 1: Use a Common Table Expression (CTE) to identify duplicates
WITH RankedOrders AS (
    SELECT
        OrderID,
        OrderDate,
        CustomerID,
        TotalAmount,
        -- Assign a rank to each order. If OrderIDs are the same, it ranks them 1, 2, 3...
        ROW_NUMBER() OVER(PARTITION BY OrderID ORDER BY OrderDate DESC) as rn
    FROM
        stg_Orders
)
-- Step 2: Insert into the final table, selecting only the first instance of each record (rn = 1)
INSERT INTO dbo.FactOrders (OrderID, OrderDate, CustomerID, TotalAmount)
SELECT
    OrderID,
    OrderDate,
    CustomerID,
    TotalAmount
FROM
    RankedOrders
WHERE
    rn = 1
    -- And ensure it doesn't already exist in the target table from a previous load
    AND NOT EXISTS (SELECT 1 FROM dbo.FactOrders t WHERE t.OrderID = RankedOrders.OrderID);

-- After inserting, clear the staging table
TRUNCATE TABLE stg_Orders;
```
**Explanation:**
1.  `PARTITION BY OrderID`: This groups all rows with the same `OrderID` together.
2.  `ROW_NUMBER() ... as rn`: Within each group, it assigns a sequential number. The first row gets `rn = 1`, the second gets `rn = 2`, etc.
3.  `WHERE rn = 1`: This is the magic. It filters the result to keep only the first row for each `OrderID`, effectively removing all duplicates.

#### Method 2: Spark in a Notebook (Lakehouse)

The same logic applies beautifully in PySpark.

```python
from pyspark.sql.window import Window
from pyspark.sql.functions import row_number

# Assume df_stg is the DataFrame loaded from the staging table
# df_stg = spark.read.table("stg_Orders")

# Define the window specification to partition by the primary key
windowSpec = Window.partitionBy("OrderID").orderBy(col("OrderDate").desc())

# Deduplicate the DataFrame by keeping only the first row in each partition
df_deduplicated = df_stg.withColumn("rn", row_number().over(windowSpec)).where("rn = 1").drop("rn")

# Now, append this clean DataFrame to your final Gold table
(df_deduplicated.write
 .insertInto("Gold_FactOrders", overwrite=False)) # Or use a merge operation
```
### Challenge 2: Handling Missing Data (Nulls)

Data can arrive with `NULL` values in important fields. How you handle this depends on the column and the business requirements.

#### The Problem

Our `stg_Orders` table has a row with a `NULL` `TotalAmount`.

| OrderID | OrderDate  | CustomerID | TotalAmount |
| :------ | :--------- | :--------- | :---------- |
| 1008    | 2023-11-18 | 203        | **NULL**    |
| 1009    | 2023-11-18 | 204        | 75.00       |

#### Solutions:

1.  **Impute a Default Value:** Replace the `NULL` with a sensible default (like 0). This is good for numeric fields where a `NULL` would break calculations like `SUM()`.
2.  **Exclude the Record:** If the missing value makes the record unusable, you might choose to filter it out and not load it.
3.  **Flag for Review:** Load the record but set a flag in a separate column (e.g., `is_imputed = 1`) so analysts know the data was incomplete.

#### Method 1: T-SQL (using `ISNULL` or `COALESCE`)

```sql
-- Impute a default value of 0 for TotalAmount
INSERT INTO dbo.FactOrders (OrderID, OrderDate, CustomerID, TotalAmount)
SELECT
    OrderID,
    OrderDate,
    CustomerID,
    ISNULL(TotalAmount, 0.00) AS TotalAmount -- If TotalAmount is NULL, use 0.00 instead
FROM
    stg_Orders
WHERE
    CustomerID IS NOT NULL; -- Exclude records where the CustomerID itself is missing
```

#### Method 2: PySpark (using `.fillna()`)

```python
# Impute a default value of 0 for the 'TotalAmount' column
df_clean = df_stg.fillna(0, subset=["TotalAmount"])

# Exclude rows where 'CustomerID' is null
df_final = df_clean.dropna(subset=["CustomerID"])

# Now write df_final to the target table
(df_final.write
 .insertInto("Gold_FactOrders", overwrite=False))
```

### Challenge 3: Handling Late-Arriving Data

This is a more complex problem. A record that belongs to a past period (e.g., a sale from yesterday) arrives in today's data load. If you've already calculated yesterday's aggregates, they are now incorrect.

#### The Problem

*   On Nov 17th, we calculated the total sales for Nov 16th as $5,000.
*   On Nov 18th, a new order arrives with an `OrderDate` of **Nov 16th**.
*   Our total for Nov 16th is now stale.

#### Solution: Designing Idempotent Pipelines and Recalculating Aggregates

The key is to design your data loading process to be **idempotent**, meaning you can re-run it for a specific period without causing side effects or duplicates. This allows you to reprocess a time slice (like a day or an hour) to include the late-arriving data.

**Strategy:**

1.  **Use a `MERGE` statement:** Instead of simple `INSERT`s, use `MERGE` (or its Spark equivalent). This can handle both new and existing records gracefully. The deduplication logic we saw earlier is a perfect fit here.
2.  **Partition Your Data:** Structure your target tables (especially large fact tables) to be partitioned by date. This makes it extremely efficient to delete and replace data for a single day without touching the rest of the table.
3.  **Re-run and Recalculate:** When late data is detected, trigger a re-run of your pipeline specifically for the affected date(s).

#### Implementation Example (Conceptual T-SQL)

Let's imagine a master stored procedure that can handle reprocessing.

```sql
CREATE PROCEDURE sp_Process_Sales_For_Date (@ProcessDate DATE)
AS
BEGIN
    -- This procedure is designed to be re-runnable for a single day.

    -- Step 1: Delete any existing aggregates for this specific day.
    -- This clears the way for a fresh calculation.
    DELETE FROM dbo.DailySalesSummary WHERE SaleDate = @ProcessDate;

    -- Step 2: Load the raw data for that
