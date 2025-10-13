The term "Eventhouse" in Microsoft Fabric refers to a top-level item in the Real-Time Intelligence workload that acts as a container for your real-time analytics components. When you create an Eventhouse, Fabric provisions two key, tightly integrated artifacts for you:

1.  **An Eventstream:** This is the "front door" for your data—it handles the ingestion and routing of your real-time event streams.
2.  **A KQL Database:** This is the high-performance analytics engine and storage layer where your data lands and is queried.

Therefore, troubleshooting "Eventhouse errors" means troubleshooting errors in one of these two components. Let's walk through a case study to identify and resolve common errors in both.

---

### Case Study: A Broken Real-Time IoT Dashboard

**Objective:**
We have an Eventhouse monitoring temperature data from IoT sensors in a factory. An Eventstream captures the data from an Azure IoT Hub and sends it to a KQL Database. A Power BI report connected to the KQL Database shows a live dashboard of average temperatures by sensor.

**The Symptom:** One morning, the operations manager reports that the **dashboard is blank or is not updating with new data**.

This is our starting point. We need to follow the data flow to diagnose the problem.

---

### Step 1: Isolate the Point of Failure (Ingestion vs. Query)

The first question is: **Is data failing to get *into* the KQL Database, or is the data there but we are failing to *query* it?**

1.  **Check the KQL Database Directly:**
    *   Go to your Fabric workspace and open the KQL Queryset for your database.
    *   Run a simple query to see the most recent data:
        ```kql
        YourTableName
        | take 100 // Get the 100 most recently ingested records
        ```
        or more specifically:
        ```kql
        YourTableName
        | top 10 by IngestionTime() desc
        ```
    *   **Scenario A:** The query returns recent data (from the last few minutes). This means the problem is likely in the Power BI report or the query it's using.
    *   **Scenario B:** The query returns no data, or the latest record is hours old. This tells you the problem is in the **ingestion path**—either the Eventstream or the KQL Database's ingestion process.

Let's assume we are in **Scenario B**, as it's the most common and complex issue. Now we need to investigate the ingestion path.

---

### Step 2: Troubleshoot the Eventstream

The Eventstream is the first component in the chain. Errors here are often related to source connectivity or data format.

1.  **Open the Eventstream Editor:** Go to your workspace and open the Eventstream artifact for editing.
2.  **Look for Visual Cues:** The editor provides a visual diagram of your stream. Look for **red warning icons** on your source or destination nodes.

#### Common Eventstream Error 1: Source Connection Failure

*   **How to Identify:** The "Source" node (e.g., your Azure IoT Hub) has a red icon. Clicking on it reveals an error message like "AuthenticationFailed" or "Endpoint not found."
*   **Root Cause:**
    *   **Expired Credentials:** The access key or Shared Access Signature (SAS) token used to connect to the IoT Hub has expired or been regenerated.
    *   **Network/Firewall Issues:** A firewall rule is blocking Fabric from reaching the IoT Hub endpoint.
    *   **Deleted Source:** The source IoT Hub itself has been deleted.
*   **Solution:**
    1.  Click on the source node in the Eventstream editor.
    2.  In the configuration pane, check the connection details.
    3.  Click **"Manage connections"** and re-enter or update the credentials (e.g., provide a new connection string or access key).
    4.  Verify that the IoT Hub resource still exists in Azure and that its networking settings allow access from Fabric services.

#### Common Eventstream Error 2: Malformed Incoming Data

*   **How to Identify:** The stream preview in the Eventstream editor shows garbled data, or downstream processing fails. The error might not appear in the Eventstream itself but in the destination that's trying to parse the data.
*   **Root Cause:** The source system is sending data that is not in the expected format. The most common issue is **invalid JSON**.
    *   *Example Bad JSON:* `{"Timestamp": "2023-11-18T10:00:00Z", "SensorID": "T-101", "Temperature" 23.5}` (Missing a colon after "Temperature").
*   **Solution:**
    1.  Examine the live data preview in the Eventstream. Copy a sample message.
    2.  Use an online JSON validator to check its structure.
    3.  **The fix is almost always in the source system.** You must work with the team managing the IoT devices to correct the firmware or application that generates the JSON payload. Fabric cannot fix structurally broken JSON on the fly in the Eventstream.

---

### Step 3: Troubleshoot the KQL Database Ingestion

If the Eventstream shows data flowing correctly but the KQL table is still empty, the problem lies with the KQL Database's ingestion process.

#### Common KQL Database Error: Schema Mismatch

This is the most frequent KQL ingestion error. The structure or data types of the incoming data from the Eventstream do not match the schema of the target table.

*   **How to Identify:** The most powerful tool here is the `.show ingestion failures` command.
    *   In your KQL Queryset, run this command:
        ```kql
        .show ingestion failures
        | take 100
        ```
    *   This will return a table of failed ingestion operations with a `FailureReason` column that gives you the exact error. A common reason is: `Failed to parse a 'real' from a 'string'`.

*   **Root Cause and Example:**
    *   Your KQL table `SensorData` is defined like this:
        ```kql
        .create table SensorData (Timestamp: datetime, SensorID: string, Temperature: real)
        ```
    *   But the JSON arriving from the Eventstream looks like this:
        ```json
        {"Timestamp": "2023-11-18T10:00:00Z", "SensorID": "T-101", "Temperature": "23.5"}
        ```
        Notice the `Temperature` is a **string** (`"23.5"`), not a number (`23.5`). The KQL engine cannot automatically convert this string to a `real` number during ingestion, so it rejects the record.

*   **Solution (Two Options):**

    *   **Option A (Best Practice): Fix the Source.** The best long-term solution is to modify the source system to send the data in the correct format (i.e., send `Temperature` as a JSON number).

    *   **Option B (Fabric-Side Fix): Use an Update Policy.** If you cannot change the source, you can pre-process the data *as it arrives* in KQL using an update policy.
        1.  **Create a Staging Table:** Create a staging table that accepts all data as strings.
            ```kql
            .create table SensorData_Staging (RawData: dynamic)
            ```
        2.  **Point the Eventstream to this staging table.**
        3.  **Create a Function to Transform the Data:**
            ```kql
            .create function TransformSensorData() {
                SensorData_Staging
                | extend ParsedData = todynamic(RawData)
                | project
                    Timestamp = todatetime(ParsedData.Timestamp),
                    SensorID = tostring(ParsedData.SensorID),
                    Temperature = toreal(ParsedData.Temperature) // Explicitly convert to real
            }
            ```
        4.  **Create an Update Policy:** This policy tells the final table to automatically run the function whenever new data arrives in the staging table.
            ```kql
            .alter table SensorData policy update
            @'[{"IsEnabled": true, "Source": "SensorData_Staging", "Query": "TransformSensorData()", "IsTransactional": false}]'
            ```
        This creates a robust pipeline that cleanses the data on the fly before it's stored in the final queryable table.

### Summary of the Troubleshooting Flow

1.  **Symptom:** Dashboard is not working.
2.  **Isolate:** Run a query directly against the KQL database.
    *   **Data is recent?** -> Problem is in Power BI. Check the report's query, filters, or refresh settings.
    *   **Data is old/missing?** -> Problem is in the ingestion path.
3.  **Investigate Eventstream:**
    *   Check for red icons on source/destination nodes (connectivity/credential errors).
    *   Check the live data preview for malformed data (e.g., bad JSON).
4.  **Investigate KQL Ingestion:**
    *   Run `.show ingestion failures` to get specific error reasons.
    *   Most common error is a **schema mismatch** (e.g., string sent to a number column).
    *   **Resolve** by either fixing the source system (preferred) or implementing a server-side transformation with an update policy.
