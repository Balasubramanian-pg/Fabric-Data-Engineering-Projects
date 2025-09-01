Creating windowing functions is a fundamental and powerful technique for advanced data analysis and transformation. In Microsoft Fabric, you can create them in the two primary compute engines: **T-SQL** (in the Data Warehouse) and **Spark** (in Notebooks).

This guide will explain everything you need to know, from the basic concepts to practical examples in both environments.

### What is a Windowing Function?

A windowing function performs a calculation across a set of table rows that are somehow related to the current row. This set of rows is the "window."

**The Key Difference from `GROUP BY`:**
*   A `GROUP BY` clause aggregates rows into a single output row, collapsing the original rows.
*   A **window function** performs a calculation on a set of rows but returns a value for **every single row**. It doesn't collapse the result.

Think of it like this: you're looking through a "window" at a specific slice of your data (e.g., all sales for one product) to calculate something (e.g., the rank of the current sale), and then you add that calculation back to the original row.

### The Anatomy of a Window Function

A window function always uses the `OVER()` clause. The structure is:

```sql
FUNCTION_NAME() OVER (
    [PARTITION BY column_list]
    [ORDER BY column_list]
    [ROWS/RANGE BETWEEN start AND end]
)
```

*   **`FUNCTION_NAME()`**: The function to apply (e.g., `SUM()`, `AVG()`, `ROW_NUMBER()`, `RANK()`, `LAG()`).
*   **`OVER()`**: The keyword that defines the window.
*   **`PARTITION BY` (Optional)**: Divides the rows into "partitions" or groups. The window function is applied independently to each partition. This is the most important clause. (e.g., `PARTITION BY ProductCategory`).
*   **`ORDER BY` (Optional but common)**: Sorts the rows *within* each partition. This is essential for ranking functions (`ROW_NUMBER`) and running totals.
*   **`ROWS/RANGE BETWEEN` (Optional)**: Specifies the frame or subset of rows within the partition to include in the calculation (e.g., "the preceding row and the current row"). This is used for things like moving averages.

### Method 1: Using T-SQL in a Synapse Data Warehouse

This is the classic, relational way to perform windowing functions. It's ideal for transforming structured data that already resides in your Warehouse or is accessible from a Lakehouse.

**Scenario:** Imagine you have a table `DailySales` in your Warehouse.

**Setup Data:**
```sql
-- Run this in your Data Warehouse query editor
CREATE TABLE DailySales (
    SaleDate DATE,
    ProductCategory VARCHAR(50),
    ProductName VARCHAR(50),
    SalesAmount DECIMAL(10, 2)
);

INSERT INTO DailySales VALUES
('2023-11-01', 'Electronics', 'Laptop', 1200.00),
('2023-11-01', 'Electronics', 'Mouse', 25.00),
('2023-11-01', 'Books', 'SQL Guide', 45.00),
('2023-11-02', 'Electronics', 'Laptop', 1300.00),
('2_23-11-02', 'Books', 'Fabric Guide', 55.00),
('2023-11-02', 'Books', 'Data Science', 70.00),
('2023-11-03', 'Electronics', 'Mouse', 27.00);
```

#### Example 1: Ranking Sales within Each Category (`ROW_NUMBER`)

**Goal:** Find the rank of each product's sales amount within its own category.

```sql
SELECT
    SaleDate,
    ProductCategory,
    ProductName,
    SalesAmount,
    ROW_NUMBER() OVER (PARTITION BY ProductCategory ORDER BY SalesAmount DESC) AS SalesRank
FROM DailySales;
```

**Result:**

| SaleDate   | ProductCategory | ProductName    | SalesAmount | **SalesRank** |
| :--------- | :-------------- | :------------- | :---------- | :------------ |
| 2023-11-02 | Books           | Data Science   | 70.00       | **1**         |
| 2023-11-02 | Books           | Fabric Guide   | 55.00       | **2**         |
| 2023-11-01 | Books           | SQL Guide      | 45.00       | **3**         |
| 2023-11-02 | Electronics     | Laptop         | 1300.00     | **1**         |
| 2023-11-01 | Electronics     | Laptop         | 1200.00     | **2**         |
| 2023-11-03 | Electronics     | Mouse          | 27.00       | **3**         |
| 2023-11-01 | Electronics     | Mouse          | 25.00       | **4**         |

#### Example 2: Calculating a Running Total (`SUM`)

**Goal:** Calculate the cumulative sales for each product category over time.

```sql
SELECT
    SaleDate,
    ProductCategory,
    SalesAmount,
    SUM(SalesAmount) OVER (PARTITION BY ProductCategory ORDER BY SaleDate ASC) AS RunningTotal
FROM DailySales;
```

**Result:**

| SaleDate   | ProductCategory | SalesAmount | **RunningTotal** |
| :--------- | :-------------- | :---------- | :--------------- |
| 2023-11-01 | Books           | 45.00       | **45.00**        |
| 2023-11-02 | Books           | 125.00      | **170.00**       |
| 2023-11-01 | Electronics     | 1225.00     | **1225.00**      |
| 2023-11-02 | Electronics     | 1300.00     | **2525.00**      |
| 2023-11-03 | Electronics     | 27.00       | **2552.00**      |

#### Example 3: Comparing to a Previous Value (`LAG`)

**Goal:** Find the previous day's sales amount for each product category.

```sql
SELECT
    SaleDate,
    ProductCategory,
    TotalDailySales,
    LAG(TotalDailySales, 1, 0) OVER (PARTITION BY ProductCategory ORDER BY SaleDate ASC) AS PreviousDaySales
FROM (
    -- First, aggregate sales by day and category
    SELECT SaleDate, ProductCategory, SUM(SalesAmount) AS TotalDailySales
    FROM DailySales
    GROUP BY SaleDate, ProductCategory
) AS DailyAggregates;
```

**Result:**

| SaleDate   | ProductCategory | TotalDailySales | **PreviousDaySales** |
| :--------- | :-------------- | :-------------- | :------------------- |
| 2023-11-01 | Books           | 45.00           | **0.00**             |
| 2023-11-02 | Books           | 125.00          | **45.00**            |
| 2023-11-01 | Electronics     | 1225.00         | **0.00**             |
| 2023-11-02 | Electronics     | 1300.00         | **1225.00**          |
| 2023-11-03 | Electronics     | 27.00           | **1300.00**          |

### Method 2: Using Spark in a Notebook

This approach is perfect for data engineers and data scientists working in a Lakehouse. You can use either **Spark SQL** (with identical syntax to the T-SQL examples) or the **PySpark DataFrame API**.

**Scenario:** The same `DailySales` data now exists as a Delta table in your Lakehouse.

#### Option A: Spark SQL (Identical Syntax)

You can run the exact same T-SQL queries from above in a Notebook cell using the `%%sql` magic command.

```sql
%%sql
SELECT
    SaleDate,
    ProductCategory,
    ProductName,
    SalesAmount,
    ROW_NUMBER() OVER (PARTITION BY ProductCategory ORDER BY SalesAmount DESC) AS SalesRank
FROM DailySales
```

#### Option B: PySpark DataFrame API (More programmatic)

This is the idiomatic way to do it in Python. It's more verbose but highly flexible and integrates seamlessly with other Python code.

**Setup in a Notebook Cell:**
```python
from pyspark.sql.functions import row_number, sum, lag, col
from pyspark.sql.window import Window

# Assume 'df' is your DataFrame loaded from the DailySales Delta table
# df = spark.read.table("DailySales")
```

**Example 1: Ranking Sales (PySpark)**

```python
# Define the window specification
windowSpec_rank = Window.partitionBy("ProductCategory").orderBy(col("SalesAmount").desc())

# Apply the window function
df_ranked = df.withColumn("SalesRank", row_number().over(windowSpec_rank))

display(df_ranked)
```

**Example 2: Calculating a Running Total (PySpark)**

```python
# Define the window specification
windowSpec_running_total = Window.partitionBy("ProductCategory").orderBy("SaleDate")

# Apply the window function
df_running_total = df.withColumn("RunningTotal", sum("SalesAmount").over(windowSpec_running_total))

display(df_running_total)
```

**Example 3: Comparing to a Previous Value (PySpark)**
```python
# First, aggregate the data
df_daily_agg = df.groupBy("SaleDate", "ProductCategory").agg(sum("SalesAmount").alias("TotalDailySales"))

# Define the window specification
windowSpec_lag = Window.partitionBy("ProductCategory").orderBy("SaleDate")

# Apply the lag function
df_lagged = df_daily_agg.withColumn("PreviousDaySales", lag("TotalDailySales", 1, 0).over(windowSpec_lag))

display(df_lagged)
```

### How to Choose: T-SQL vs. Spark

| Factor | T-SQL in Data Warehouse | Spark in Notebook |
| :--- | :--- | :--- |
| **User Persona** | Data Analyst, BI Developer, SQL Developer | **Data Engineer**, Data Scientist |
| **Language** | **T-SQL** | **Python (PySpark)**, Scala, R, Spark SQL |
| **Data Format** | Optimized for structured, relational tables | **Handles any format** (Delta, Parquet, JSON, CSV) |
| **Use Case** | Final transformations for BI, building star schemas, ELT | Heavy-duty data prep, feature engineering for ML, complex ETL |
| **Development** | SQL query editor, Stored Procedures | Interactive Notebooks, can be parameterized and scheduled |

**Simple Rule of Thumb:**

*   If your data is already structured in a Warehouse and your goal is to create a final BI model, **use T-SQL**. It's direct, efficient, and familiar to analysts.
*   If you are transforming raw or semi-structured data in a Lakehouse, or if you need to integrate complex logic only possible in a full programming language, **use Spark in a Notebook**.
