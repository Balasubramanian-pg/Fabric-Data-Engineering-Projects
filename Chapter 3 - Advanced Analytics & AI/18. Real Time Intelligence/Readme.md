# Getting Started with Real-Time Intelligence in Microsoft Fabric  

## Lab Overview  
This lab demonstrates how to:  
1. Create an eventstream to ingest real-time data  
2. Store streaming data in an eventhouse  
3. Query and visualize real-time data
4. Set up alerts for data changes  

**Estimated Time**: 30 minutes  

> [!IMPORTANT]  
> You need a [Microsoft Fabric tenant](https://learn.microsoft.com/fabric/get-started/fabric-trial) to complete this exercise.

---

## 1. Create a Workspace  

1. Sign in to [Microsoft Fabric](https://app.fabric.microsoft.com)  
2. Select **Workspaces** (icon: &#128455;) from the left menu  
3. Create a new workspace:  
   - Assign a name  
   - Choose a licensing mode with Fabric capacity (*Trial*, *Premium*, or *Fabric*)  

> [!NOTE]  
> The workspace will initially appear empty until we add our components.

---

## 2. Set Up Real-Time Data Pipeline  

### Create an Eventstream  
1. Navigate to the **Real-Time Hub**  
2. Select **Data sources** and connect to the **Stock market** sample  
3. Name the source `stock` and eventstream `stock-data`  

![Eventstream configuration](./Images/name-eventstream.png)  

> [!TIP]  
> The default stream will be named *stock-data-stream* automatically.

### Create an Eventhouse  
1. Select **Create** > **Eventhouse**  
2. Name your eventhouse  

> [!CAUTION]  
> If **Create** isn't visible, select the ellipsis (**...**) first.

![New eventhouse](./Images/create-eventhouse.png)  

### Connect Eventstream to Eventhouse  
1. In your KQL database, select **Get data** > **Eventstream**  
2. Create a table named `stock`  
3. Configure connection to your `stock-data` eventstream  

![Data connection setup](./Images/configure-destination.png)  

> [!NOTE]  
> This creates a live pipeline from source to storage.

---

## 3. Work with Real-Time Data  

### Query the Data  
Run KQL queries against your stock data:  

```kql
// View sample data
stock | take 100

// Calculate 5-minute averages
stock
| where ["time"] > ago(5m)
| summarize avgPrice = avg(todecimal(bidPrice)) by symbol
| project symbol, avgPrice
```

![Query results](./Images/kql-stock-query.png)  

> [!TIP]  
> Run queries multiple times to see real-time updates.

### Create a Dashboard  
1. Pin your average price query to a new **Stock Dashboard**  
2. Change visualization to **Column chart**  

![Dashboard visualization](./Images/stock-dashboard-chart.png)  

---

## 4. Configure Alerts  

1. In your dashboard, select **Set alert**  
2. Configure to trigger when:  
   - Any stock's average price increases by $100  
   - Checking every 5 minutes  
   - Grouped by stock symbol  
3. Set action to email notification  

![Alert configuration](./Images/configure-activator.png)  

> [!WARNING]  
> Alerts may take several minutes to appear in history depending on data changes.

---

## 5. Clean Up Resources  

> [!IMPORTANT]  
> To avoid unnecessary charges:  

1. Navigate to your workspace  
2. Select **Workspace settings**  
3. Choose **Remove this workspace**  

> [!CAUTION]  
> This action permanently deletes all lab resources.

---

## Key Learnings  

> [!NOTE]  
> Real-Time Intelligence provides:  
> - Low-latency data ingestion  
> - Temporal data analysis  
> - Live visualizations  
> - Proactive alerting  

> [!TIP]  
> For production scenarios:  
> - Add data transformations in the eventstream  
> - Create multiple dashboards for different stakeholders  
> - Configure more sophisticated alert conditions  

![End-to-end architecture](./Images/eventstream-destination.png)
