Ingesting data using pipelines is the most common and robust method for traditional batch and micro-batch data loading in Microsoft Fabric. It provides a scalable, repeatable, and schedulable way to move data from virtually any source into OneLake.

This guide will walk you through the entire process using a practical case study, covering the design, implementation, and best practices.

### The Core Concept: What is a Data Pipeline in Fabric?

A Fabric Data Pipeline is a workflow orchestration tool powered by the **Azure Data Factory** engine. Think of it as a visual canvas where you can design a sequence of steps (called "activities") to perform a data-related task.

The most fundamental activity in any ingestion pipeline is the **Copy data** activity.

**Copy data Activity:**
*   **Source:** Defines where the data is coming from (e.g., a file in a data lake, a table in a database, a REST API).
*   **Sink:** Defines where the data is going to (e.g., a new table in a Lakehouse or Data Warehouse).
*   **Mapping:** Defines how columns from the source should be mapped to columns in the sink.

### Case Study: Daily Ingestion of Customer Data

**Objective:**
Every day, our marketing department drops a new CSV file containing the previous day's new customer sign-ups into a specific folder in an **Azure Data Lake Storage (ADLS) Gen2** account. We need to create an automated daily pipeline to ingest this file into our Fabric Lakehouse.

*   **Source:** Azure Data Lake Storage Gen2.
    *   Container: `marketing-data`
    *   Folder Structure: `customers/daily/{yyyy}/{MM}/{dd}/new_customers.csv`
    *   Example path: `customers/daily/2023/11/18/new_customers.csv`
*   **Target (Sink):** A Fabric Lakehouse named `LH_Marketing_Analytics`.
*   **Table Name:** The data should be loaded into a Delta table named `Bronze_New_Customers`.

### Step-by-Step Implementation Guide

#### Step 1: Create a New Data Pipeline

1.  Navigate to your Fabric workspace.
2.  Click the **"+ New"** button and select **Data pipeline**.
3.  Give your pipeline a descriptive name, for example, `PL_Ingest_Daily_Customers_CSV`.

You are now presented with a blank pipeline canvas.

#### Step 2: Configure the `Copy data` Activity

1.  From the "Activities" pane, drag a **Copy data** activity onto the canvas.
2.  Select the new activity. The configuration pane will appear at the bottom.
3.  In the **"General"** tab, give the activity a descriptive name, like `Copy_Customers_CSV_to_Lakehouse`.

#### Step 3: Configure the Source (Azure Data Lake)

1.  Go to the **"Source"** tab.
2.  **Data store type:** Select **"External"**.
3.  **Connection:**
    *   If you have an existing connection to your ADLS Gen2 account, select it.
    *   If not, click **"+ New"**. You will need to provide:
        *   The URL of your storage account (e.g., `https://yourstorage.dfs.core.windows.net`).
        *   A connection name.
        *   An authentication method (Service Principal is recommended for production).
4.  **File path type:** Select **"Wildcard file path"**. This is crucial for handling daily files.
5.  **Wildcard paths:**
    *   **Folder path:** `marketing-data/customers/daily/`
    *   **File name:** `*/*/*/new_customers.csv`
    *   *Note:* The wildcards `*` will match the `yyyy`, `MM`, and `dd` folders, but this isn't dynamic yet. We'll make it dynamic later. For now, this will copy all files that match the pattern.
6.  **File format:** Select **"DelimitedText"**.
7.  **File format settings:**
    *   Click "Settings" next to the format.
    *   Check **"First row as header"**. This tells the pipeline to use the first row of the CSV as column names.
    *   You can also specify the delimiter, encoding, etc., if they are non-standard.

#### Step 4: Configure the Sink (Fabric Lakehouse)

1.  Go to the **"Sink"** tab.
2.  **Data store type:** Select **"Workspace"**.
3.  **Workspace data store type:** Select **"Lakehouse"**.
4.  **Lakehouse:** Select your `LH_Marketing_Analytics` Lakehouse from the dropdown.
5.  **Root folder:** Choose **"Tables"**.
6.  **Table name:** Enter `Bronze_New_Customers`.
7.  **Table action:** Select **"Append"**. This will add the new data to the table each day. If you wanted to replace the table entirely each run, you would choose "Overwrite".

#### Step 5: Configure the Mapping

1.  Go to the **"Mapping"** tab.
2.  Click the **"Import schemas"** button.
3.  Fabric will connect to both the source and sink to infer the schema. It will automatically try to map columns with the same name.
4.  Review the mapping. You can manually change mappings, delete columns you don't want to load, or change the data types for the destination columns. This is a critical step for ensuring data quality.

### Step 6: Making the Pipeline Dynamic (Advanced but Essential)

Right now, our pipeline would try to copy *all* historical CSV files. We only want to copy *yesterday's* file. To do this, we use **pipeline parameters** and **dynamic content**.

1.  **Create a Pipeline Parameter:**
    *   Click on the blank canvas area of your pipeline (not on an activity).
    *   Go to the **"Parameters"** tab in the bottom pane.
    *   Click **"+ New"** and create a parameter named `TargetDate` with a default value like `2023-11-18`.

2.  **Use the Parameter in the Source Path:**
    *   Go back to the `Copy data` activity's **"Source"** tab.
    *   Instead of using wildcards for the file path, we will build the path dynamically.
    *   Change the **Folder path** setting. Click "Add dynamic content".
    *   In the expression builder, enter the following expression:
        ```
        @concat('marketing-data/customers/daily/', formatDateTime(pipeline().parameters.TargetDate, 'yyyy/MM/dd'))
        ```
    *   Change the **File name** to `new_customers.csv`.

    **Explanation of the Expression:**
    *   `pipeline().parameters.TargetDate`: This accesses the `TargetDate` parameter we created.
    *   `formatDateTime(...)`: This function formats the date into the `yyyy/MM/dd` string needed for our folder structure.
    *   `@concat(...)`: This function joins all the strings together to form the full, dynamic path.

### Step 7: Validate, Run, and Schedule

1.  **Validate:** Click the **"Validate"** button on the top ribbon. Fabric will check your pipeline for any configuration errors.
2.  **Debug Run:** Click **"Run" -> "Debug"**. This will execute the pipeline immediately using the default parameter value. You can watch the progress in the "Output" pane at the bottom. This is how you test your logic.
3.  **Check the Output:** After the run succeeds, go to your `LH_Marketing_Analytics` Lakehouse and query the `Bronze_New_Customers` table to verify that the data has been loaded correctly.
4.  **Schedule the Pipeline:**
    *   Click the **"Schedule"** button on the top ribbon.
    *   Configure the schedule to run **Daily** at a specific time (e.g., 3:00 AM UTC).
    *   **Important:** Now you need to make the `TargetDate` parameter truly dynamic for the schedule. In the schedule configuration, you can override the parameter value with a system function. For the `TargetDate` parameter, you would set its value to:
        ```
        @formatDateTime(addDays(utcNow(), -1), 'yyyy-MM-dd')
        ```
        This expression tells the scheduled run to always use **yesterday's date** (`utcNow()` minus one day).

Now you have a fully automated, dynamic, and robust pipeline that ingests your daily customer files into your Fabric Lakehouse without any manual intervention.
