Of course. Preparing data for loading into a dimensional model is one of the most fundamental and important tasks in data warehousing. This process, often part of the "Silver-to-Gold" transformation in a Medallion Architecture, involves cleansing, structuring, and transforming raw data into clean, conformed fact and dimension tables.

Here is a comprehensive guide on how to prepare data for a dimensional model in Microsoft Fabric, using a practical case study and code examples.

### The Goal: Creating a Star Schema

The end goal of this process is to create a **star schema**. A star schema consists of:

*   **Fact Tables:** These tables contain the "what happened"—the quantitative measures (facts) of a business process (e.g., `SalesAmount`, `QuantitySold`). They also contain foreign keys that link to the dimension tables. Fact tables are typically long and narrow.
*   **Dimension Tables:** These tables contain the "who, what, where, when, why"—the descriptive context (attributes) for the facts (e.g., `CustomerName`, `ProductCategory`, `StoreLocation`, `Date`). Dimension tables are typically wide and shallow.

This structure is highly optimized for analytical queries.

### Case Study: Building a Sales Star Schema

**Objective:**
We need to transform our raw, denormalized sales data into a clean star schema consisting of `FactSales` and three dimension tables: `DimCustomer`, `DimProduct`, and `DimDate`.

**Source Data:**
We have a "Silver" layer table in our Lakehouse called `Silver_Sales_Transactions`. It's a denormalized table that looks like this:

| TransactionID | OrderDate  | CustomerID | CustomerName | CustomerCity | ProductID | ProductName | ProductCategory | Quantity | UnitPrice |
| :------------ | :--------- | :--------- | :----------- | :----------- | :-------- | :---------- | :---------------- | :------- | :-------- |
| txn-001       | 2023-11-18 | cust-101   | John Smith   | New York     | prod-abc  | Laptop      | Electronics       | 1        | 1200.00   |
| txn-002       | 2023-11-18 | cust-102   | Jane Doe     | London       | prod-xyz  | SQL Guide   | Books             | 2        | 45.00     |
| txn-003       | 2023-11-19 | cust-101   | John Smith   | New York     | prod-xyz  | SQL Guide   | Books             | 1        | 45.00     |

**Target (Gold Layer):**
*   `DimCustomer` (CustomerID, CustomerName, City)
*   `DimProduct` (ProductID, ProductName, Category)
*   `DimDate` (DateKey, FullDate, Year, Month, Day)
*   `FactSales` (DateKey, CustomerKey, ProductKey, Quantity, UnitPrice, SalesAmount)

Notice the `FactSales` table will use **surrogate keys** (`CustomerKey`, `ProductKey`) instead of the original business keys (`CustomerID`, `ProductID`). This is a critical best practice.
### Step-by-Step Data Preparation Process

We will use a **Fabric Notebook (PySpark)** for this transformation, as it's ideal for handling these types of structural changes and data cleansing operations.

#### Step 1: Create the Dimension Tables

The first step is to create the clean, unique dimension tables from the source data.

##### A. Preparing `DimCustomer`

1.  **Select Distinct Customers:** We need a unique list of customers and their attributes.
2.  **Generate Surrogate Keys:** We will create a new, unique, integer-based primary key for our dimension. This is called a **surrogate key**. It's better than using the original `CustomerID` (the "business key") because it's a stable integer (fast for joins) and can help handle historical changes (SCDs - Slowly Changing Dimensions).

```python
# In a Fabric Notebook
from pyspark.sql.functions import col, monotonically_increasing_id

# Load the source data
df_source = spark.read.table("Silver_Sales_Transactions")

# 1. Select distinct customer attributes
df_dim_customer_source = df_source.select("CustomerID", "CustomerName", "CustomerCity").distinct()

# 2. Generate a surrogate key
# We add 1 to the ID to start keys from 1 instead of 0
df_dim_customer = df_dim_customer_source.withColumn("CustomerKey", monotonically_increasing_id() + 1)

# Reorder columns for clarity
df_dim_customer = df_dim_customer.select("CustomerKey", "CustomerID", "CustomerName", "CustomerCity")

# Save as a Gold dimension table
df_dim_customer.write.mode("overwrite").format("delta").saveAsTable("DimCustomer")

display(df_dim_customer)
```
**Resulting `DimCustomer` Table:**
| CustomerKey | CustomerID | CustomerName | CustomerCity |
| :---------- | :--------- | :----------- | :----------- |
| 1           | cust-101   | John Smith   | New York     |
| 2           | cust-102   | Jane Doe     | London       |

##### B. Preparing `DimProduct`

We follow the exact same pattern for the product dimension.

```python
# 1. Select distinct product attributes
df_dim_product_source = df_source.select("ProductID", "ProductName", "ProductCategory").distinct()

# 2. Generate surrogate key
df_dim_product = df_dim_product_source.withColumn("ProductKey", monotonically_increasing_id() + 1)

# Reorder columns
df_dim_product = df_dim_product.select("ProductKey", "ProductID", "ProductName", "ProductCategory")

# Save as a Gold dimension table
df_dim_product.write.mode("overwrite").format("delta").saveAsTable("DimProduct")

display(df_dim_product)
```

##### C. Preparing `DimDate`

A Date dimension is special. It's usually not derived from the source data but is generated independently to contain all possible dates in a given range. It's a standard practice in data warehousing.

```python
from pyspark.sql.functions import year, month, dayofmonth, date_format

# Generate a date range (e.g., for 10 years)
start_date = '2020-01-01'
end_date = '2030-12-31'

# Create a DataFrame with a sequence of dates
df_dim_date = spark.sql(f"SELECT sequence(to_date('{start_date}'), to_date('{end_date}'), interval 1 day) as DateRange") \
    .withColumn("FullDate", explode(col("DateRange")))

# Create date attributes and a surrogate key (e.g., YYYYMMDD)
df_dim_date = df_dim_date.withColumn("DateKey", date_format(col("FullDate"), "yyyyMMdd").cast("int")) \
                         .withColumn("Year", year(col("FullDate"))) \
                         .withColumn("Month", month(col("FullDate"))) \
                         .withColumn("Day", dayofmonth(col("FullDate"))) \
                         .withColumn("DayOfWeek", date_format(col("FullDate"), "E"))

# Select final columns
df_dim_date = df_dim_date.select("DateKey", "FullDate", "Year", "Month", "Day", "DayOfWeek")

# Save as a Gold dimension table
df_dim_date.write.mode("overwrite").format("delta").saveAsTable("DimDate")

display(df_dim_date.head(5))
```

#### Step 2: Create the Fact Table

Now that we have our dimensions and their surrogate keys, we can build the `FactSales` table. This involves:
1.  Starting with the source transaction data.
2.  Joining back to our new dimension tables to look up the surrogate keys.
3.  Calculating any new measures (facts).
4.  Selecting only the key columns and measure columns.

```python
# Load the newly created dimension tables
df_dim_customer = spark.read.table("DimCustomer")
df_dim_product = spark.read.table("DimProduct")
df_dim_date = spark.read.table("DimDate")

# 1. Start with the source transactional data
df_fact_source = df_source.select("OrderDate", "CustomerID", "ProductID", "Quantity", "UnitPrice")

# 2. Join to DimCustomer to get the CustomerKey
df_fact_joined_cust = df_fact_source.join(
    df_dim_customer,
    df_fact_source.CustomerID == df_dim_customer.CustomerID,
    "inner"
).select(
    "OrderDate",
    df_dim_customer.CustomerKey, # Get the surrogate key
    "ProductID",
    "Quantity",
    "UnitPrice"
)

# 3. Join the result to DimProduct to get the ProductKey
df_fact_joined_prod = df_fact_joined_cust.join(
    df_dim_product,
    df_fact_joined_cust.ProductID == df_dim_product.ProductID,
    "inner"
).select(
    "OrderDate",
    "CustomerKey",

    df_dim_product.ProductKey, # Get the surrogate key
    "Quantity",
    "UnitPrice"
)

# 4. Join the result to DimDate to get the DateKey
df_fact_joined_date = df_fact_joined_prod.withColumn("JoinDate", col("OrderDate")) # Alias OrderDate to avoid ambiguity
df_fact_joined_date = df_fact_joined_date.join(
    df_dim_date,
    df_fact_joined_date.JoinDate == df_dim_date.FullDate,
    "inner"
).select(
    df_dim_date.DateKey, # Get the surrogate key
    "CustomerKey",
    "ProductKey",
    "Quantity",
    "UnitPrice"
)

# 5. Calculate new measures and select final columns
df_fact_sales = df_fact_joined_date.withColumn("SalesAmount", col("Quantity") * col("UnitPrice"))

# Final selection of columns for the fact table
df_fact_sales = df_fact_sales.select("DateKey", "CustomerKey", "ProductKey", "Quantity", "UnitPrice", "SalesAmount")

# Save as a Gold fact table
df_fact_sales.write.mode("overwrite").format("delta").saveAsTable("FactSales")

display(df_fact_sales)
```
**Resulting `FactSales` Table:**
| DateKey  | CustomerKey | ProductKey | Quantity | UnitPrice | SalesAmount |
| :------- | :---------- | :--------- | :------- | :-------- | :---------- |
| 20231118 | 1           | 1          | 1        | 1200.00   | 1200.00     |
| 20231118 | 2           | 2          | 2        | 45.00     | 90.00       |
| 20231119 | 1           | 2          | 1        | 45.00     | 45.00       |

You have now successfully transformed your raw data into a clean, performant, and analytics-ready star schema.