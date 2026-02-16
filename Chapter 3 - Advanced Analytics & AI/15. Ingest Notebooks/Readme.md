# **Lab: Ingest Data with Spark and Microsoft Fabric Notebooks**  
### **Module: Ingest Data with Spark and Microsoft Fabric Notebooks**  


## **Lab Overview**  
**Objective:**  
Learn to ingest and optimize data in Microsoft Fabric using **Spark notebooks**, **PySpark**, and **Delta tables**.  

**Learning Outcomes:**  
1. Connect to external data sources (Azure Blob Storage)  
2. Load data into a Fabric lakehouse with write optimizations  
3. Transform data and create Delta tables  
4. Query data using Spark SQL  

**Estimated Time:** **30 minutes**  

> **Prerequisite:**  
> - A [Microsoft Fabric trial](https://learn.microsoft.com/fabric/get-started/fabric-trial) account.  


## **Lab Structure**  
1. **Set Up Workspace & Lakehouse** → Create a Fabric workspace and lakehouse.  
2. **Ingest External Data** → Connect to Azure Blob Storage and load Parquet files.  
3. **Transform & Load to Delta** → Clean data and store in optimized Delta tables.  
4. **Query with Spark SQL** → Analyze data using SQL syntax in PySpark.  
5. **Clean Up** → Delete the workspace post-lab.  


## **Step 1: Set Up Workspace & Lakehouse**  
**Purpose:** Prepare a Fabric environment for data ingestion.  

### **Task A: Create a Workspace**  
1. Sign in to [Microsoft Fabric](https://app.fabric.microsoft.com).  
2. Navigate to **Workspaces** → **New Workspace**.  
3. Name it (e.g., `DataIngestionLab`), select a **Fabric-enabled** license (Trial/Premium), and create.  

![Empty workspace](./Images/new-workspace.png)  

### **Task B: Create a Lakehouse**  
1. In the workspace, select **New Item** → **Lakehouse**.  
2. Name it (e.g., `TaxiData_Lakehouse`) and create.  
3. In the **Files** section, create a subfolder named **RawData**.  
4. Copy the **ABFS path** of the folder (e.g., `abfss://.../RawData`) for later use.  

![New lakehouse](./Images/new-lakehouse.png)  


## **Step 2: Ingest External Data**  
**Purpose:** Load NYC taxi data from Azure Blob Storage into the lakehouse.  

### **Task A: Create a Notebook**  
1. Open the lakehouse → **New Notebook**.  
2. Ensure the language is set to **PySpark (Python)**.  

### **Task B: Connect to Blob Storage**  
1. Run the following code to read Parquet data:  
   ```python
   # Azure Blob Storage access info
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   
   # Construct and print connection path
   wasbs_path = f'wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}'
   print(wasbs_path)
   
   # Read data into DataFrame
   blob_df = spark.read.parquet(wasbs_path)
   ```  
   **Expected Output:** `wasbs://nyctlc@azureopendatastorage.blob.core.windows.net/yellow`  

### **Task C: Write Data to Lakehouse**  
1. Replace `**InsertABFSPathHere**` with your **RawData** ABFS path and run:  
   ```python
   file_name = "yellow_taxi"
   output_parquet_path = f"abfss://.../RawData/{file_name}"  # Use your path
   print(output_parquet_path)
   
   # Write 1000 rows to Parquet
   blob_df.limit(1000).write.mode("overwrite").parquet(output_parquet_path)
   ```  
2. Refresh the **Files** section to see `yellow_taxi.parquet`.  


## **Step 3: Transform & Load to Delta**  
**Purpose:** Clean data and store it in a Delta table for optimized queries.  

### **Task A: Transform Data**  
Run this code to:  
- Add a load timestamp.  
- Filter out NULL values.  
- Save to a Delta table.  

```python
from pyspark.sql.functions import col, current_timestamp

# Read Parquet data
raw_df = spark.read.parquet(output_parquet_path)   

# Add timestamp and filter NULLs
filtered_df = raw_df.withColumn("dataload_datetime", current_timestamp()) \
                   .filter(col("storeAndFwdFlag").isNotNull())

# Write to Delta table
table_name = "yellow_taxi"
filtered_df.write.format("delta").mode("append").saveAsTable(table_name)

# Display a sample
display(filtered_df.limit(1))
```  

**Expected Output:** A single row with the new `dataload_datetime` column.  

![Transformed data output](./Images/notebook-transform-result.png)  


## **Step 4: Query with Spark SQL**  
**Purpose:** Use SQL syntax to analyze the Delta table.  

### **Task A: Run a SQL Query**  
```python
# Create a temp SQL view
filtered_df.createOrReplaceTempView("yellow_taxi_temp")

# Query with Spark SQL
results = spark.sql('SELECT * FROM yellow_taxi_temp')
display(results.limit(10))
```  

**Key Insight:**  
- Spark SQL integrates seamlessly with PySpark.  
- Temporary views enable familiar SQL workflows.  


## **Step 5: Clean Up**  
**Purpose:** Remove resources to avoid unnecessary costs.  

1. Navigate to **Workspace Settings** → **Remove this Workspace**.  
2. Confirm deletion.  


## **Key Takeaways**  
1. **PySpark** simplifies connecting to external data (e.g., Azure Blob).  
2. **Delta tables** optimize storage and query performance.  
3. **Spark SQL** bridges the gap between Python and SQL workflows.  

**Next Steps:** Explore [Delta Lake optimizations](https://learn.microsoft.com/fabric/data-engineering/delta-optimizations) for large-scale data.  


**Feedback?** Rate this lab or suggest improvements [here](#).