# **Lab: Monitor Fabric Activity in the Monitoring Hub**  
**Module: Monitoring Fabric**  

---

## **Lab Overview**  
**Objective:**  
Gain hands-on experience using the **Microsoft Fabric Monitoring Hub** to track, analyze, and optimize data workflows.  

**Learning Outcomes:**  
1. Understand how to monitor **dataflows, notebooks, and lakehouses** in Fabric.  
2. Learn to filter and customize monitoring views for better insights.  
3. Track historical runs and troubleshoot activity logs.  

**Estimated Time:** **30 minutes**  

> **Prerequisite:**  
> - A [Microsoft Fabric trial](https://learn.microsoft.com/fabric/get-started/fabric-trial) account.  

---

## **Lab Structure**  
1. **Set Up a Workspace** → Create a Fabric workspace for monitoring.  
2. **Ingest Data with a Lakehouse & Dataflow** → Load data and track execution.  
3. **Run & Monitor a Spark Notebook** → Analyze data and observe job status.  
4. **Analyze Historical Runs** → Review past executions for auditing.  
5. **Customize Monitoring Views** → Apply filters and column settings.  
6. **Clean Up Resources** → Delete the workspace post-lab.  

---

## **Step 1: Set Up a Workspace**  
**Purpose:** Create a dedicated workspace to organize and monitor Fabric items.  

1. **Sign in** to [Microsoft Fabric](https://app.fabric.microsoft.com).  
2. Navigate to **Workspaces** (left pane).  
3. Select **New Workspace** → Name it (e.g., "MonitoringLab").  
4. Under **Advanced**, choose a **Fabric-enabled** license (Trial/Premium).  
5. Confirm creation—your workspace is now ready.  

![Empty workspace in Fabric](./Images/new-workspace.png)  

---

## **Step 2: Ingest Data with a Lakehouse & Dataflow**  
**Purpose:** Load product data into a lakehouse and monitor the ETL process.  

### **Task A: Create a Lakehouse**  
1. In the workspace, select **Create** → **Lakehouse** (under *Data Engineering*).  
2. Name it (e.g., "SalesLakehouse").  

### **Task B: Build & Monitor a Dataflow**  
1. Open the lakehouse → **New Dataflow Gen2** (via *Get Data*).  
2. Rename it to **"Load_Products"**.  
3. Use **Import from Text/CSV** with this URL:  
   ```plaintext
   https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/products.csv
   ```  
4. Authenticate anonymously → Preview data → **Publish**.  

**Monitor Execution:**  
- Go to **Monitoring Hub** (left pane).  
- Track the dataflow’s status (Pending → In Progress → Succeeded).  
- Return to the lakehouse → Verify the **products** table exists.  

![Dataflow in Monitoring Hub](./Images/monitor-dataflow.png)  

---

## **Step 3: Run & Monitor a Spark Notebook**  
**Purpose:** Query the ingested data and monitor Spark job performance.  

1. Create a **Notebook** (from *Data Engineering* home).  
2. Rename it to **"Analyze_Products"**.  
3. **Attach your lakehouse** (via *Explorer* pane).  
4. Auto-generate a Spark query:  
   - Right-click the **products** table → **Load Data > Spark**.  
5. **Run all cells** → Observe query results.  
6. **Stop the session** to release resources.  

**Monitor the Notebook:**  
- Check the **Monitoring Hub** for execution logs.  
- Note the **duration, status, and initiator**.  

![Notebook execution logs](./Images/monitor-notebook.png)  

---

## **Step 4: Analyze Historical Runs**  
**Purpose:** Audit past executions for debugging or compliance.  

1. Re-run the **Load_Products** dataflow (click **↻ Refresh** in the workspace).  
2. In **Monitoring Hub**:  
   - Select **…** → **Historical Runs**.  
   - Compare timestamps, durations, and statuses.  
   - Drill into a run → **View Details** for granular logs.  

![Historical runs view](./Images/historical-runs.png)  

---

## **Step 5: Customize Monitoring Views**  
**Purpose:** Tailor the monitoring dashboard for efficiency.  

1. **Apply Filters:**  
   - Click **Filter** → Set:  
     - *Status = Succeeded*  
     - *Item Type = Dataflow Gen2*  
2. **Adjust Columns:**  
   - Add: *Start Time, Duration, Submitted By, Refresh Type*.  
   - Remove non-essential fields.  

![Customized monitoring view](./Images/monitor-columns.png)  

---

## **Step 6: Clean Up**  
**Purpose:** Remove lab resources to avoid unnecessary costs.  

1. Open **Workspace Settings** (via **…** menu).  
2. Select **Remove this Workspace** → Confirm deletion.  

---

## **Key Takeaways**  
1. The **Monitoring Hub** centralizes tracking for all Fabric activities.  
2. **Filters and columns** optimize troubleshooting.  
3. **Historical runs** provide audit trails for data workflows.  

**Next Steps:** Explore **alerting** and **Power BI metrics** for proactive monitoring.  

--- 

**Feedback?** Rate this lab or suggest improvements [here](#).
