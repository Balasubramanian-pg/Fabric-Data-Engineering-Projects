
### **Lab: Monitor Fabric Activity in the Monitoring Hub**
### **Module: Monitoring Fabric**

---

# Monitor Fabric Activity in the Monitoring Hub

The **monitoring hub** is your central command center in Microsoft Fabric for overseeing all activity. It provides a comprehensive view of events, allowing you to track the status, history, and performance of items you have permission to access.

This lab will take approximately **30 minutes** to complete.

> **Note:** To complete this exercise, you will need access to a [Microsoft Fabric tenant](https://learn.microsoft.com/fabric/get-started/fabric-trial).

## Create a Workspace

First, you'll need a workspace to contain the Fabric items you'll create and monitor.

1.  Sign into the [Microsoft Fabric home page](https://app.fabric.microsoft.com/home?experience=fabric) at `https://app.fabric.microsoft.com/home?experience=fabric`.
2.  In the left navigation pane, select **Workspaces** (the icon resembles stacked papers).
3.  Create a **New workspace**. Give it a descriptive name.
4.  Expand the **Advanced** section and select a licensing mode that includes Fabric capacity (such as *Trial*, *Premium*, or *Fabric*).
5.  Once your new workspace opens, it will be empty and ready for you to add items.

    ![Screenshot of an empty workspace in Fabric.](./Images/new-workspace.png)

## Create a Lakehouse

Now that you have a workspace, let's create a lakehouse to store and manage our data.

1.  In the left navigation pane, select **Create**.
    > **Note:** If **Create** is not pinned to the pane, select the Fabric icon at the bottom left to open the item creation menu.
2.  In the **Data Engineering** section, select **Lakehouse**. Assign it a unique name of your choice.
3.  After a moment, your new lakehouse will be created. In the **Lakehouse explorer** pane on the left, you can browse the **Tables** and **Files** within your lakehouse. Currently, it contains no data.

    ![Screenshot of a new lakehouse.](./Images/new-lakehouse.png)

## Create and Monitor a Dataflow

Dataflows (Gen2) in Fabric are powerful tools for ingesting and transforming data. You will now create a dataflow to load product data from a CSV file into a table in your new lakehouse.

1.  On your lakehouse's **Home** page, select **New Dataflow Gen2** from the **Get data** menu. A new dataflow, named **Dataflow 1**, will open in the editor.
2.  At the top left, select the **Dataflow 1** name to open its properties pane, and rename it to **Get Product Data**.
3.  In the dataflow editor, select **Import from a Text/CSV file**.
4.  In the connection settings, paste the following URL and proceed with anonymous authentication:
    ```
    https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/products.csv
    ```
5.  Once the connection is established, a preview of the product data will appear in the editor.
    ![Screenshot of a dataflow query.](./Images/data-flow-query.png)
6.  **Publish** the dataflow to save and run it.
7.  In the left navigation pane, select **Monitor** to open the monitoring hub. You should see your **Get Product Data** dataflow with an "In progress" status. If not, refresh the view.
    ![Screenshot of the monitoring hub with a dataflow in-progress.](./Images/monitor-dataflow.png)
8.  Wait a few moments and refresh the page again until the dataflow status changes to **Succeeded**.
9.  Return to your lakehouse by selecting it from the navigation pane. Expand the **Tables** folder (you may need to refresh it) to confirm that a new table named **products** has been successfully created.

    ![Screenshot of the products table in the lakehouse page.](./Images/products-table.png)

## Create and Monitor a Spark Notebook

Next, you'll use a Spark notebook to query the data you just loaded. Notebooks in Fabric provide an interactive environment for running Spark code.

1.  Navigate to the **Data Engineering** home page and create a new **Notebook**.
2.  A new notebook, named **Notebook 1**, will open. At the top left, select its name and rename it to **Query Products**.
3.  In the **Explorer** pane, select **Lakehouses** and add the lakehouse you created earlier to the notebook.
4.  In the **Explorer** pane, find the **products** table. Select the **...** menu next to it and choose **Load data > Spark**. This will automatically generate and add a new code cell to your notebook.
    ![Screenshot of a notebook with code to query a table.](./Images/load-spark.png)
5.  Select the **Run all** button to execute the code. The Spark session will start, and after a moment, the query results will appear below the cell.
    ![Screenshot of a notebook with query results.](./Images/notebook-output.png)
6.  On the toolbar, select the **Stop session** button (**&#9723;**) to release the Spark resources.
7.  Return to the **Monitor** hub in the left navigation pane. You will now see the activity from your notebook run listed.
    ![Screenshot of the monitoring hub with a notebook activity.](./Images/monitor-notebook.png)

## Monitor Item History

Many items in a workspace are run multiple times. The monitoring hub allows you to easily view the complete run history for any item.

1.  Navigate back to your workspace. Find your **Get Product Data** dataflow and select its **Refresh now** icon (**&#8635;**) to run it again.
2.  Go back to the **Monitor** hub and confirm that the dataflow is "In progress."
3.  Find the **Get Product Data** dataflow in the list. Select its **...** menu and choose **Historical runs**. This will display the complete run history for the dataflow.
    ![Screenshot of the monitoring hub historical runs view.](./Images/historical-runs.png)
4.  From the history list, select the **...** menu for any run and choose **View detail** to inspect its specific details.
5.  Close the **Details** pane and select **Back to main view** to return to the main monitoring hub page.

## Customize Monitoring Hub Views

In a busy Fabric environment, finding specific events can be challenging. The monitoring hub provides powerful filtering and column customization options to help you focus on what matters.

1.  In the monitoring hub, select the **Filter** button and apply the following criteria:
    *   **Status**: Succeeded
    *   **Item type**: Dataflow Gen2
    *   The view will update to show only successful runs of dataflows.
    ![Screenshot of the monitoring hub with a filter applied.](./Images/monitor-filter.png)

2.  Next, select the **Column Options** button to customize the displayed columns. Add the following columns to your view and select **Apply**:
    *   Activity name
    *   Status
    *   Item type
    *   Start time
    *   Submitted by
    *   Location
    *   End time
    *   Duration
    *   Refresh type
    *   You can now scroll horizontally to see the detailed, customized view.
    ![Screenshot of the monitoring hub with custom columns.](./Images/monitor-columns.png)

## Clean Up Resources

You have successfully created a lakehouse, a dataflow, and a Spark notebook, and used the monitoring hub to track their activity. If you are finished exploring, you can delete the workspace to clean up all associated resources.

1.  In the left navigation pane, select your workspace to view its contents.
2.  In the toolbar, select the **...** menu and choose **Workspace settings**.
3.  In the **General** section, select **Remove this workspace** and confirm the deletion.
