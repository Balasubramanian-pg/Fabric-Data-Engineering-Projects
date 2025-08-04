# Get Started with Real-Time Dashboards in Microsoft Fabric

## Estimated Duration: 60 minutes

**Real-time dashboards in Microsoft Fabric** provide a powerful way to visualize and interact with live streaming data using the Kusto Query Language (KQL). In this hands-on exercise, you'll embark on a journey to create and effectively utilize a dynamic real-time dashboard, all powered by a continuously updating data source.

-----

## Create an Eventhouse

With your workspace now ready, the next step is to begin building the essential Fabric items required for your real-time intelligence solution. We'll start by creating an **Eventhouse**, which serves as the core repository for your streaming data.

1.  Within your active workspace, locate and select **+ New item (1)**. A *New item* pane will appear; from this pane, choose **Eventhouse (2)**.

2.  In the naming field, enter **BicycleEventhouse (1)** as the name for your new Eventhouse, then select **Create (2)** to finalize its creation.

3.  As your Eventhouse is being provisioned, you might encounter various tips or prompts. Close these as they appear until you are presented with your newly created, empty Eventhouse.

4.  On the left-hand pane, take a moment to observe that your new **Eventhouse automatically contains a KQL database** with the exact same name as your Eventhouse.

5.  Select this **KQL database** to explore its contents.

    > **Note**: At this point, your database will be empty, containing no tables. In the subsequent sections of this exercise, you will leverage an **eventstream** to efficiently load real-time data from a continuous source directly into a table within this database.

-----

## Create an Eventstream

As noted, your newly created database is currently devoid of tables. To populate it with dynamic, real-time information, we'll employ an **eventstream**. This eventstream will act as the conduit, loading data from a live source directly into a table within your Eventhouse.

1.  From the main page of your **KQL database (1)**, locate and select **Get data (2)**.

2.  In the prompt for the data source, choose **Eventstream (3)**, then select **New eventstream (4)**. Provide the name `Bicycle-data` (5) for your Eventstream, and finally, click **Create (6)**.

    > **Note**: Your new eventstream will be created within your workspace in just a few moments. Once the creation process is complete, you'll be automatically redirected to the primary editor, which is where you'll begin integrating various data sources into your eventstream.

-----

## Add a Source to the Eventstream

Now that your eventstream is established, the next crucial step is to define its input source. For this lab, we'll utilize convenient sample data provided within Fabric.

1.  On the **Eventstream canvas**, which is your visual workspace for building eventstreams, select **Use sample data**.

2.  A new pane will appear for configuring the sample data source. Name this source `Bicycles` (1). From the available sample datasets, choose **Bicycles (2)**, and then click **Add (3)** to integrate this data into your eventstream.

    > **Note**: Once you add the sample data, your stream will be automatically mapped, and you'll be seamlessly returned to the **eventstream canvas**. Here, you can review the flow of data.

-----

## Add a Destination for the Eventstream

With a source configured, the eventstream now needs a destination where the processed data will be stored. We'll direct this real-time data into your Eventhouse.

1.  On the Eventstream canvas, locate and select the **Transform events or add destination (1)** tile. A search bar will appear; in it, search for and select **Eventhouse (2)**.

2.  In the subsequent **Eventhouse** pane, meticulously configure the following setup options to define how your data will be ingested:

      * **Data ingestion mode**: Select **Event processing before ingestion (1)**.
      * **Destination name**: Enter `bikes-table` (2).
      * **Workspace**: Confirm that your current workspace, identified as `fabric-<inject key="DeploymentID" enableCopy="false"/>` (3), is selected.
      * **Eventhouse**: Choose **BicycleEventhouse (4)** from the dropdown.
      * **KQL database**: Select **BicycleEventhouse (5)**.
      * **Destination table**: Choose the option to **Create a new table** and name it `bikes`.
      * **Input data format**: Specify **JSON (9)**, as our sample data is in this format.

3.  After configuring all the settings in the **Eventhouse** pane, select **Save (10)** to apply your destination settings.

4.  On the main toolbar of the eventstream editor, select **Publish**. This action will deploy your eventstream configuration, allowing data to begin flowing.

5.  Allow approximately a minute or so for the data destination to become fully active and start receiving data. Afterward, select the **bikes-table** node within the design canvas. Below it, in the **Data preview** pane, you should now see the latest data that has been successfully ingested into your table.

6.  The eventstream is designed to run perpetually, meaning new data will continuously arrive. Wait a few more minutes, then use the **Refresh** button in the **Data preview** pane to observe any newly added data in the table.

-----

## Create a Real-Time Dashboard

With your eventstream actively loading real-time data into the `bikes` table within your Eventhouse, the stage is set to visualize this dynamic information. You'll now create a **real-time dashboard** to bring your data to life.

1.  In the left-hand menu bar, select **+ create** to initiate the creation of a new Fabric item. From the options, choose **Real-Time Dashboard** and name it `bikes-dashboard`.

    > **Note**: An empty, new dashboard will be immediately created and displayed.

2.  On the dashboard's toolbar, select **New data source (1)**. From the subsequent options, choose **Eventhouse/KQL Database (2)** as your data source type. Then, select **BicycleEventhouse (3)**, and click **Connect (4)**.

3.  Configure your new data source with the following settings before clicking **Add (4)**:

      * **Display name**: Enter `Bike Rental Data` (1).
      * **Database**: Confirm that **BicycleEventhouse (2)** is selected.
      * **Passthrough identity**: Ensure this option is **Selected**.

4.  Once the data source is added, close the **Data sources** pane. Now, on the blank dashboard design canvas, select **Add tile** to begin adding visualizations.

5.  In the query editor that appears, first ensure that the **Bike Rental Data (2)** source is actively selected. Then, enter the following KQL code into the editor:

    ```kql
    bikes
        | where ingestion_time() between (ago(30min) .. now())
        | summarize latest_observation = arg_max(ingestion_time(), *) by Neighbourhood
        | project Neighbourhood, latest_observation, No_Bikes, No_Empty_Docks
        | order by Neighbourhood asc
    ```

6.  To execute the query and see its results, select **Run (3)**. This query is designed to display the latest number of bikes and empty bike docks observed in each neighborhood over the past 30 minutes.

7.  Once the query results are visible, select **Apply changes (5)** to render the data directly into a table within the new tile on your dashboard.

8.  With the data displayed in the tile, select the **Edit** icon (which resembles a pencil). In the **Visual Formatting** pane that opens on the right, configure the following properties to transform your table into a more engaging visual:

      * **Tile name**: Set this to `Bikes and Docks`.
      * **Visual type**: Change this to **Bar chart**.
      * **Visual format**: Choose **Stacked bar chart**.
      * **Y columns**: Select `No_Bikes` and `No_Empty_Docks`.
      * **X column**: Select `Neighbourhood`.
      * **Series columns**: Choose `infer`.
      * **Legend location**: Set this to **Bottom**.

9.  **Apply the changes** you've made to the visual formatting. Then, **resize the tile** on the dashboard canvas to occupy the full height of the left side, providing ample space for the bar chart.

10. On the dashboard toolbar, select **New tile** to add another visualization.

11. In the query editor for this new tile, ensure that the **Bike Rental Data** source is still selected. Enter the following KQL code:

    ```kql
    bikes
        | where ingestion_time() between (ago(30min) .. now())
        | summarize latest_observation = arg_max(ingestion_time(), *) by Neighbourhood
        | project Neighbourhood, latest_observation, Latitude, Longitude, No_Bikes
        | order by Neighbourhood asc
    ```

12. **Run the query**. This query will show the geographical location and the count of bikes observed in each neighborhood over the last 30 minutes.

13. **Apply the changes** to display the data as a table within this new tile.

14. Select the **Edit** icon (pencil) on the new tile. In the **Visual Formatting** pane, adjust the properties as follows to create a map visualization:

      * **Tile name**: Set this to `Bike Locations`.
      * **Visual type**: Change this to **Map**.
      * **Define location by**: Choose **Latitude and longitude**.
      * **Latitude column**: Select `Latitude`.
      * **Longitude column**: Select `Longitude`.
      * **Label column**: Select `Neighbourhood`.
      * **Size**: Select **Show**.
      * **Size column**: Select `No_Bikes`.

15. **Apply the changes**, then **resize the map tile** to fill the remaining available space on the right side of your dashboard, creating a comprehensive view.

-----

## Create a Base Query

You may have noticed that both visuals on your dashboard currently rely on similar underlying queries. To promote efficiency, reduce redundancy, and significantly enhance the maintainability of your dashboard, you can consolidate the common data retrieval logic into a single, reusable **base query**.

1.  On the dashboard toolbar, navigate to the **Manage** tab, then select **Base queries**.

2.  In the **Base queries** pane, select **+ Add** to create a new base query.

3.  In the base query editor that opens, set the **Variable name** to `base_bike_data` (1). Confirm that the **Bike Rental Data (2)** source is selected. Then, meticulously enter the following KQL query into the editor (3):

    ```kql
    bikes
        | where ingestion_time() between (ago(30min) .. now())
        | summarize latest_observation = arg_max(ingestion_time(), *) by Neighbourhood
    ```

4.  To verify the output, **Run (4)** the query. Confirm that it successfully returns all the necessary columns required for both your bar chart and map visuals on the dashboard, plus any other relevant columns.

5.  Select **Done** to save your new base query, then close the **Base queries** pane.

6.  Now, **edit the Bikes and Docks bar chart visual** (using the pencil icon). Modify its query to utilize the new base query, as shown in the following code:

    ```kql
    base_bike_data
    | project Neighbourhood, latest_observation, No_Bikes, No_Empty_Docks
    | order by Neighbourhood asc
    ```

7.  **Apply the changes** and verify that the bar chart continues to correctly display data for all neighborhoods, now driven by the base query.

8.  Similarly, **edit the Bike Locations map visual**. Change its query to leverage the base query:

    ```kql
    base_bike_data
    | project Neighbourhood, latest_observation, No_Bikes, Latitude, Longitude
    | order by Neighbourhood asc
    ```

9.  **Apply the changes** and confirm that the map still accurately displays data for all neighborhoods, effectively utilizing the centralized base query.

-----

## Add a Parameter

Your dashboard currently presents the latest bike, dock, and location data for all neighborhoods. To enhance interactivity and provide more targeted insights, you'll now introduce a **parameter**, allowing users to select and filter data for specific neighborhoods.

1.  On the dashboard toolbar, navigate to the **Manage** tab, and then select **Parameters**.

2.  Take note of any parameters that might have been automatically generated (such as a *Time range* parameter). For the purpose of this exercise, **Delete** any existing parameters to start fresh.

3.  Select **+ Add** to create a new parameter.

4.  Configure the new parameter with the following detailed settings:

      * **Label**: `Neighbourhood`

      * **Parameter type**: Choose **Multiple selection**.

      * **Description**: Provide a helpful description, such as `Choose neighbourhoods`.

      * **Variable name**: Set this to `selected_neighbourhoods`.

      * **Data type**: Select `string`.

      * **Show on pages**: Choose **Select all**.

      * **Source**: Select **Query**.

      * **Data source**: Choose **Bike Rental Data**.

      * **Edit query**: Enter the following KQL query:

        ```kql
        bikes
        | distinct Neighbourhood
        | order by Neighbourhood asc
        ```

      * **Value column**: Select `Neighbourhood`.

      * **Label column**: Choose **Match value selection**.

      * **Add "Select all" value**: Ensure this is **Selected**.

      * **"Select all" sends empty string**: Ensure this is **Selected**.

      * **Auto-reset to default value**: Select this option.

      * **Default value**: Choose **Select all**.

5.  Finally, select **Done** to create and save your new parameter.

    Now that you've added a parameter, you must modify your base query to dynamically filter the data based on the neighborhoods chosen by the user.

6.  On the toolbar, select **Base queries**. Then, select and **edit the `base_bike_data` query**. You'll add an `and` condition to the `where` clause to filter the data using the selected parameter values, as illustrated in the following KQL code:

    ```kql
    bikes
        | where ingestion_time() between (ago(30min) .. now())
          and (isempty(['selected_neighbourhoods']) or Neighbourhood in (['selected_neighbourhoods']))
        | summarize latest_observation = arg_max(ingestion_time(), *) by Neighbourhood
    ```

7.  Select **Done** to save the updated base query.

8.  On the dashboard, you can now interact with the new **Neighbourhood** parameter control. Use it to filter the data displayed on your visuals based on the specific neighborhoods you select.

9.  To clear any selected parameter filters and return to displaying all data, simply select the **Reset** button.

-----

## Add a Page

Currently, your dashboard is composed of a single page. To provide a more comprehensive and organized view of your data, you have the flexibility to add additional pages, each potentially dedicated to different aspects or levels of detail.

1.  On the left side of the dashboard interface, expand the **Pages** pane. Once expanded, select **+ Add page**.

2.  Name this new page **Page 2**. Then, ensure you select it to make it your active page.

3.  On the newly created page, select **+ Add tile** to introduce another visualization.

4.  In the query editor for this new tile, enter the following KQL query:

    ```kql
    base_bike_data
    | project Neighbourhood, latest_observation
    | order by latest_observation desc
    ```

5.  **Apply the changes** to display the query results. Then, resize the tile to efficiently fill the height of the dashboard, optimizing its visual presence.

-----

## Configure Auto Refresh

While users can manually refresh the dashboard to see the latest data, it is often more beneficial and user-friendly to have the dashboard automatically update its information at a predefined interval. This ensures a continuously current view of your real-time data.

1.  On the dashboard toolbar, navigate to the **Manage** tab, and then select **Auto refresh**.

2.  In the **Auto refresh** pane that appears, configure the following settings to enable and define your refresh behavior:

      * **Enabled**: Ensure this option is **Selected**.
      * **Minimum time interval**: Choose **Allow all refresh intervals**.
      * **Default refresh rate**: Set this to `30 minutes`.

3.  Finally, **Apply the auto refresh settings** to activate this feature for your dashboard.

-----

## Save and Share the Dashboard

You've successfully created a highly useful and interactive real-time dashboard. The final steps involve saving your work and then sharing it with other users who can benefit from these live insights.

1.  On the dashboard toolbar, locate and select **Save**. This action will persist all your configurations and visualizations.

2.  Once the dashboard has been successfully saved, select **Share**.

3.  In the **Share** dialog box, select **Copy link**. This will copy the direct URL to your dashboard onto your clipboard.

4.  Open a new browser tab and paste the copied link into the address bar to navigate directly to the shared dashboard. If prompted, sign in again with your Microsoft Fabric credentials.

5.  Spend some time exploring the dashboard. Interact with the parameters you created, switch between pages, and observe how it provides the latest information about bikes and empty bike docks across the city, all in real time.

-----

Would you like to continue building on this foundation by integrating more complex data transformations, or perhaps explore advanced security features for your real-time dashboards?
