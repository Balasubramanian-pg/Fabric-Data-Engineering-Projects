Monitoring data transformation is a crucial step that goes beyond simple ingestion monitoring. Here, you're not just checking if data arrived; you're verifying that the business logic was applied correctly, the data quality is high, and the performance is acceptable.

In Microsoft Fabric, monitoring transformations involves a combination of observing pipeline and notebook runs, querying metadata, and implementing data quality checks.

---

### The Core Principle: What Are You Monitoring?

When monitoring data transformations, you are primarily concerned with three things:

1.  **Operational Health:** Did the transformation job (e.g., a notebook or a stored procedure) run successfully and on time?
2.  **Performance:** Did the job complete within an acceptable duration? Is its performance degrading over time?
3.  **Data Quality & Integrity:** Did the transformation produce the expected results? Are there nulls, duplicates, or incorrect values in the output? Did it process the correct number of rows?

---

### Monitoring Different Transformation Methods

Let's break down how to monitor the main transformation tools in Fabric.

#### 1. Monitoring Notebooks (Spark Transformations)

Notebooks are the workhorse for complex ETL/ELT. Monitoring them involves checking the run status and digging into Spark's rich diagnostic information.

**Case Study:** We are monitoring our `N_Clean_Customer_Data` notebook, which transforms raw customer data from a Bronze table to a clean Silver table.

**Step-by-Step Monitoring Process:**

1.  **High-Level Status (Monitoring Hub):**
    *   Go to the **Monitoring Hub** and filter by "Notebook".
    *   You'll see a list of all notebook runs.
    *   **What to check:**
        *   **Status:** "Succeeded" or "Failed".
        *   **Duration:** A sudden spike in duration could mean an increase in data volume or an inefficient transformation.
        *   **Spark Application ID:** A link to the detailed Spark UI for deep-dive performance tuning.

2.  **Drill-Down into the Notebook Run View:**
    *   Click on a specific notebook run from the Monitoring Hub.
    *   This opens a read-only view of the notebook as it ran, showing the output of each cell.
    *   **What to check:**
        *   **Cell-Level Status:** You can see which specific cell failed if the run was unsuccessful. The error traceback will be printed directly below the failing cell.
        *   **Cell Output:** Review any `print()` statements or `display()` outputs you included for logging purposes. This is a crucial, simple way to monitor your transformation logic.

3.  **Deep Dive with the Spark UI (For Performance Tuning):**
    *   From the notebook run view or the Monitoring Hub, click on the **Spark Application link**. This opens the detailed Apache Spark UI. This is an advanced tool for engineers.
    *   **What to check:**
        *   **Jobs & Stages:** See how your notebook code was broken down into Spark jobs and stages. You can identify which stage is taking the most time.
        *   **Event Timeline:** Visualize the execution timeline of tasks across different executors.
        *   **Data Skew:** Look for stages where one task takes much longer than others, which can indicate data skew (one partition being much larger than the others).

4.  **Implementing Custom Logging within the Notebook (Best Practice):**
    *   Don't rely solely on the UI. Make your notebook self-monitoring.
    *   **Log Row Counts:** Before and after your transformation, log the row counts. This is the single most important data quality check.
        ```python
        # In your notebook
        df_bronze = spark.read.table("Bronze_Customers")
        bronze_count = df_bronze.count()
        print(f"Read {bronze_count} rows from Bronze table.")

        # --- Your transformation logic here ---
        # e.g., df_silver = df_bronze.filter(...).dropna(...)

        silver_count = df_silver.count()
        print(f"Writing {silver_count} rows to Silver table.")

        # Optional: Add an assertion to fail the notebook if data quality is bad
        if silver_count < (bronze_count * 0.9): # Example: Fail if we lost more than 10% of rows
            raise Exception("Significant data loss during transformation! Check filter/join logic.")
        
        df_silver.write.mode("overwrite").saveAsTable("Silver_Customers")
        ```
    *   This simple logging will appear in your notebook run history, giving you an immediate, easy-to-read summary of the transformation's impact.

#### 2. Monitoring Stored Procedures (T-SQL Transformations)

Stored procedures are used for transformations within the Synapse Data Warehouse, often for the "Silver-to-Gold" layer.

**Case Study:** We are monitoring our `sp_Load_Daily_Sales_Summary` procedure.

**Step-by-Step Monitoring Process:**

1.  **Monitoring the Orchestrator (Data Pipeline):**
    *   Typically, a stored procedure is executed by a **Stored Procedure activity** in a Fabric Data Pipeline.
    *   You monitor the run of this *activity* in the **Monitoring Hub** as described in the pipeline monitoring guide.
    *   **What to check:**
        *   Did the activity succeed or fail?
        *   If it failed, the error message from SQL Server (e.g., "Divide by zero," "Primary key violation") will be displayed directly in the activity's error details.

2.  **Using Dynamic Management Views (DMVs) in the Warehouse:**
    *   For performance monitoring, you can query Fabric's built-in DMVs. This is a more advanced technique.
    *   **What to check:**
        *   **Query History:** Find the exact query that was run by the stored procedure and analyze its performance.
            ```sql
            SELECT *
            FROM sys.dm_pdw_exec_requests
            WHERE [label] LIKE '%sp_Load_Daily_Sales_Summary%'
            ORDER BY start_time DESC;
            ```
        *   This DMV gives you the `status`, `total_elapsed_time`, `command` text, and more for all queries run against the warehouse.

3.  **Implementing Custom Logging in T-SQL (Best Practice):**
    *   Just like with notebooks, make your stored procedure self-monitoring.
    *   **Create a Log Table:**
        ```sql
        CREATE TABLE dbo.PipelineLog (
            LogID INT IDENTITY(1,1) PRIMARY KEY,
            ProcedureName VARCHAR(128),
            LogTimestamp DATETIME2 DEFAULT GETUTCDATE(),
            RowsRead INT,
            RowsWritten INT,
            Status VARCHAR(50),
            LogMessage VARCHAR(MAX)
        );
        ```
    *   **Instrument Your Stored Procedure:**
        ```sql
        CREATE OR ALTER PROCEDURE sp_Load_Daily_Sales_Summary (...)
        AS
        BEGIN
            DECLARE @RowsRead INT, @RowsWritten INT;

            -- Your transformation logic...
            -- For example, after loading data into a temp table:
            SELECT @RowsRead = COUNT(*) FROM #TempStagingTable;

            -- After your final INSERT/MERGE statement:
            SET @RowsWritten = @@ROWCOUNT; -- @@ROWCOUNT gets the number of rows affected by the last statement

            -- Log the result
            INSERT INTO dbo.PipelineLog (ProcedureName, RowsRead, RowsWritten, Status, LogMessage)
            VALUES ('sp_Load_Daily_Sales_Summary', @RowsRead, @RowsWritten, 'Success', 'Daily summary loaded successfully.');

        END;
        -- Don't forget to add a TRY...CATCH block to log failures too!
        ```
    *   Now, you can simply query your `PipelineLog` table to get a perfect, auditable history of all your transformation runs.

### Proactive Monitoring with Data Activator

For the ultimate proactive monitoring of data transformations, you can use **Data Activator**.

*   **Scenario:** You want to be alerted if the number of rows written by your transformation pipeline drops by more than 20% compared to the previous day.
*   **Implementation:**
    1.  Your `PipelineLog` table (from the T-SQL example) now contains the history of `RowsWritten` for each run.
    2.  Create a Power BI report on top of this `PipelineLog` table. Create a measure that calculates the percent change in `RowsWritten` day-over-day.
    3.  From a visual in that report, **set a Data Activator alert**.
    4.  Configure the trigger: `WHEN PercentChange < -0.2`.
    5.  Set the action: **Send a Teams message** to the data engineering channel.

Now, Fabric will actively monitor the *output* of your transformation and automatically alert you to data quality anomalies, moving you from a reactive to a proactive monitoring posture.