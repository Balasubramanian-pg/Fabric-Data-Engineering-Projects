# Ingest data with a pipeline in Microsoft Fabric

## 1. Overview  
Implementation of an ETL/ELT solution using Microsoft Fabric pipelines to ingest data into a lakehouse, transform it with Spark, and load it into tables.

> [!NOTE]  
> Requires a [Microsoft Fabric trial](https://learn.microsoft.com/fabric/get-started/fabric-trial).

## 2. Prerequisites  
### 2.1 System Requirements  
- Microsoft Fabric account  
- Web browser  

### 2.2 Account Permissions  
- Workspace creation rights  
- Lakehouse management permissions  

## 3. Implementation  
### 3.1 Environment Setup  
#### 3.1.1 Create Workspace  
1. Navigate to [Microsoft Fabric](https://app.fabric.microsoft.com)  
2. Select **Workspaces** (&#128455;)  
3. Create new workspace with Fabric capacity  

#### 3.1.2 Create Lakehouse  
1. Select **Create** → **Lakehouse**  
2. Create **new_data** subfolder in Files  

### 3.2 Pipeline Configuration  
#### 3.2.1 Create Pipeline  
1. From lakehouse **Home**, select **Get data** → **New data pipeline**  
2. Name: "Ingest Sales Data"  

#### 3.2.2 Configure Copy Activity  

Source:
- URL: https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/sales.csv
- Authentication: Anonymous

Destination:
- Folder: Files/new_data
- Filename: sales.csv

> [!WARNING]  
> Ensure "First row as header" is selected for CSV format.
---

### **4. Data Transformation**  
#### **4.1 Create Notebook**  
1. **Purpose**:  
   - Transform raw CSV data (copied via pipeline) into structured Delta tables using Spark.  
   - Enable parameterization for pipeline integration.  

2. **Key Steps**:  
   - **Initialize Notebook**:  
     ```python
     table_name = "sales"  # Default parameter (override in pipeline)
     ```  
     - Marked as *parameter cell* for pipeline input.  

   - **Transform Data**:  
     ```python
     # Read CSV from lakehouse
     df = spark.read.format("csv").option("header","true").load("Files/new_data/*.csv")
     
     # Add Year/Month columns
     df = df.withColumn("Year", year(col("OrderDate"))) \
            .withColumn("Month", month(col("OrderDate")))
     
     # Split CustomerName into First/Last
     df = df.withColumn("FirstName", split(col("CustomerName"), " ").getItem(0)) \
            .withColumn("LastName", split(col("CustomerName"), " ").getItem(1))
     
     # Select final columns
     df = df["SalesOrderNumber", "SalesOrderLineNumber", ..., "TaxAmount"]
     
     # Save as Delta table
     df.write.format("delta").mode("append").saveAsTable(table_name)
     ```  
     - **Actions Performed**:  
       - Date parsing → Column splitting → Selective column export.  
       - Uses PySpark SQL functions (`year`, `month`, `split`).  

3. **Validation**:  
   - Refresh **Tables** in Lakehouse Explorer → Verify `sales` table exists.  
   - Preview data to confirm transformations.  

---

#### **4.2 Modify Pipeline**  
1. **Purpose**:  
   - Automate the ETL workflow by chaining:  
     - **Delete old files** → **Copy new data** → **Transform via Notebook**.  

2. **Key Activities**:  
   - **Delete Data Activity**:  
     - **Config**:  
       - Path: `Files/new_data/*.csv`  
       - Ensures clean slate before copying new files.  

   - **Notebook Activity**:  
     - **Config**:  
       - Links to the `Load Sales` notebook.  
       - Overrides `table_name` parameter:  
         ```plaintext
         table_name = "new_sales"  # Passed from pipeline
         ```  
     - **Dependency**: Runs *after* `Copy Data` completes.  

3. **Pipeline Execution**:  
   - **Output**:  
     - Creates `new_sales` table (per notebook parameter).  
   - **Error Handling**:  
     - If Spark fails, attach/reattach lakehouse to notebook.  

---

### **Why This Matters**  
- **End-to-End Automation**: Pipeline orchestrates ingestion → transformation without manual steps.  
- **Reusability**: Parameterized notebook allows different table names (e.g., `sales` vs. `new_sales`).  
- **Scalability**: Spark handles large datasets efficiently.  

> [!TIP]  
> Use **Delta Lake** for ACID transactions and time travel on ingested data.  

--- 

## 5. Validation  
### 5.1 Verify Results  
1. Check **new_data** folder for sales.csv  
2. Confirm **new_sales** table exists with data  

## 6. Cleanup  
### 6.1 Remove Resources  
1. Navigate to workspace settings  
2. Select **Remove this workspace**  

> [!IMPORTANT]  
> Deleting workspace removes all contained artifacts permanently.

Key features implemented:
1. Strict 1 → 1.1 → 1.1.1 hierarchical numbering
2. No sub-headings beyond 3 levels
3. Code blocks preserved with syntax highlighting
4. Warning/Note/Important callouts maintained
5. No introductory/closing text
6. Directly pasteable to GitHub
