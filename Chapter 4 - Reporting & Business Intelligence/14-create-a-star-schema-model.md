# Create and Explore a Semantic Model in Microsoft Fabric

This exercise will guide you through the process of developing a robust data model within Microsoft Fabric, leveraging the sample NY Taxi data stored in a data warehouse. By the end of this lab, you'll be proficient in constructing a custom semantic model, defining crucial relationships between tables, organizing your data model diagram for clarity, and directly exploring your data within the Fabric environment.

You'll gain practical experience in:

  * **Creating a custom semantic model** from an existing Fabric data warehouse.
  * **Establishing relationships** between tables and **organizing the model diagram** for optimal understanding.
  * **Exploring your data** within the newly created semantic model directly in the Microsoft Fabric interface.

This lab is designed to take approximately **30 minutes** to complete.

> **Note**: To successfully complete this exercise, you will need access to a [Microsoft Fabric trial](https://learn.microsoft.com/fabric/get-started/fabric-trial) environment.

-----

## Create a Workspace

Before you can begin working with data and creating semantic models in Microsoft Fabric, the foundational step is to create a dedicated workspace. This workspace will serve as your collaborative environment and must have the Fabric trial enabled.

1.  Open your web browser and navigate to the [Microsoft Fabric home page](https://app.fabric.microsoft.com/home?experience=fabric). Once there, proceed to **sign in using your Microsoft Fabric credentials**.
2.  On the left-hand side of the interface, within the menu bar, locate and select the **Workspaces** icon (which typically resembles 🗇). This will open your workspace management area.
3.  Initiate the creation of a new workspace. You can choose any name you prefer for this workspace. Critically, ensure you select a **licensing mode that includes Fabric capacity**, such as **Trial**, **Premium**, or **Fabric**, to enable all necessary features for this lab.
4.  Once your new workspace is created and opens, you should see that it is currently **empty**, ready for you to populate with data and items.

-----

## Create a Data Warehouse and Load Sample Data

With your workspace now successfully established, the next logical step is to create a data warehouse. This data warehouse will be the backbone for storing and managing the data we'll use to build our semantic model.

1.  On the left-hand menu bar, select **Create**. On the *New* page that appears, under the *Data Warehouse* section, select **Warehouse**. You will be prompted to give your new warehouse a **unique name of your choice**.

    > **Note**: If the **Create** option is not immediately visible or pinned to your sidebar, you may need to first select the ellipsis (**...**) option to reveal more creation choices.

    After approximately a minute or so, your new data warehouse will be provisioned and ready:

2.  In the central area of the data warehouse user interface, you'll observe various methods for loading data. For this exercise, select **Sample data** to automatically load the convenient **NYC Taxi data** into your newly created data warehouse. This process may take a couple of minutes to complete.

3.  Once the sample data has finished loading, utilize the **Explorer** pane located on the left side of the interface. This pane allows you to intuitively browse and examine the tables and views that have been populated within your sample data warehouse.

4.  With your data loaded, navigate to the **Reporting** tab of the ribbon at the top of the interface. From the options presented, choose **New semantic model**. This action initiates the creation of a tailored semantic model, allowing you to selectively include specific tables and views from your data warehouse, optimizing it for reporting and analysis by data teams and business users.

5.  Provide a name for your semantic model; we'll call it **Taxi Revenue**. Ensure that it is correctly associated with the workspace you created earlier. Then, carefully select the following tables to be included in your semantic model:

      * **Date**
      * **Trip**
      * **Geography**
      * **Weather**

-----

## Create Relationships Between Tables

Now that you've selected the tables for your semantic model, the crucial next step is to establish relationships between them. These relationships are fundamental for accurately analyzing and visualizing your data, ensuring that queries across different tables yield meaningful results. If you're familiar with creating relationships in Power BI Desktop, this process will feel very intuitive.

1.  Navigate back to your workspace and visually confirm that your new semantic model, named **Taxi Revenue**, is listed. Pay attention to its item type, which should be **Semantic model**, distinct from the **Semantic model (default)** that is automatically generated when you create a data warehouse.

    > *Note: It's important to understand the distinction. A **default semantic model** is automatically created when you establish a Warehouse or SQL analytics endpoint in Microsoft Fabric. It inherently inherits the underlying business logic from its parent Lakehouse or Warehouse. In contrast, the **semantic model that you create yourself**, as we are doing here, is a **custom model**. This custom model grants you full control to design and modify it precisely according to your specific analytical needs and preferences. You have the flexibility to create custom semantic models using various tools, including Power BI Desktop, the Power BI service directly, or other tools capable of connecting to Microsoft Fabric.*

2.  From the ribbon within your workspace, select **Open data model**. This action will launch the model view, where you can visually define and manage relationships.

    Now, you'll proceed to create the necessary relationships between your tables. As mentioned, for those familiar with Power BI Desktop, this interface and process will be quite familiar\!

    *To frame our approach, let's briefly review the **star schema concept**. We will organize the tables in our model into a central **Fact table** and several surrounding **Dimension tables**. In the context of this specific model, the **Trip** table will serve as our core fact table, containing transactional data. Our dimension tables, providing descriptive attributes, will be **Date**, **Geography**, and **Weather**.*

3.  Begin by creating a relationship between the **Date** table and the **Trip** table. This relationship will link trip records to specific dates.

      * **Select the `DateID` column** in the **Date** table.
      * Then, **drag and drop it directly on top of the `DateID` column in the Trip table**.
      * Crucially, ensure that the established relationship is a **One to many** relationship, flowing from the **Date** table (the "one" side) to the **Trip** table (the "many" side). This signifies that one date can be associated with multiple trips.

4.  Next, create two additional relationships, both connecting to the **Trip** fact table, to enrich your trip data with geographical and weather information:

      * Connect **Geography [GeographyID]** to **Trip [DropoffGeographyID]**. This should also be a **1:Many** relationship.
      * Connect **Weather [GeographyID]** to **Trip [DropoffGeographyID]**. Similarly, this must also be a **1:Many** relationship.

    > **Note**: For both of these new relationships, you will need to manually **change the relationship's default cardinality to 1:Many** within the relationship properties or dialog box that appears after dragging and dropping the columns.

5.  To visually optimize your model for clarity and adherence to the star schema, drag the tables into their appropriate positions on the diagram. Arrange them so that the **Trip** fact table is centrally located at the bottom of the diagram, with the remaining tables—your dimension tables (**Date**, **Geography**, and **Weather**)—positioned intuitively around the fact table.

    *Your star schema model is now successfully created. At this point, you have a solid foundation. There are numerous other modeling configurations you could apply to further enhance this model, such as adding hierarchies for drill-down capabilities, creating calculated columns or measures (DAX expressions) for advanced analytics, and setting properties like column visibility for improved user experience in reports.*

    > **Tip**: To make your model even more user-friendly, especially for those who will be building reports, open the **Properties pane** of the window. There, you can **toggle *Pin related fields to top of card* to Yes**. This simple adjustment will visually highlight the fields involved in relationships at a glance, making it easier to understand the model's structure. Additionally, the properties pane allows you to interact with the fields in your tables. For instance, if you want to confirm that data types are set correctly for numerical or date fields, you can select a specific field and review its format within the properties pane.

-----

## Explore Your Data

With your semantic model now built atop your data warehouse and all necessary relationships thoughtfully established for effective reporting, it's time to delve into your data. You'll use the intuitive **Explore data** feature within Fabric to gain initial insights.

1.  Navigate back to your workspace and select your **Taxi Revenue semantic model**.

2.  In the main window for your semantic model, locate and select **Explore this data** from the ribbon. This will launch a focused experience designed specifically for data exploration, allowing you to examine your data in a tabular format without the immediate need to create a full-fledged Power BI report.

3.  In the "Rearrange data" section or equivalent interface, add **YearName** and **MonthName** to the rows area to structure your data by time. Then, in the values field well, explore the **average number of passengers**, **average trip amount**, and **average trip duration**.

    *When you drag and drop a numeric field into the explore pane, it will typically default to summarizing the number (e.g., counting instances). To change the aggregation from **Summarize** to **Average**, simply select the field within the values area and then change the aggregation option in the small popup window that appears.*

4.  While a matrix provides a detailed view, to quickly visualize this data more dynamically, select **Visual** at the bottom of the window. Then, choose a **bar chart** from the available visual types.

    *It's important to note that a simple bar chart may not always be the optimal way to represent every dataset. Feel free to experiment\! Play around with the different visual types available and adjust the fields you're looking at within the "Rearrange data" section of the Data pane on the right side of the screen. This iterative exploration will help you discover the most effective visualizations for your data.*

5.  Once you are satisfied with your exploration view, you can save it to your workspace by clicking the **Save** button located in the top left corner of the window. You also have the convenient option to **Share** this data exploration with colleagues by selecting **Share** in the upper right corner, fostering collaborative insights.

6.  After you have successfully saved your exploration, navigate back to your workspace. You should now see a comprehensive list of your created items: your data warehouse, the automatically generated default semantic model, the custom semantic model you specifically designed, and your newly saved data exploration view.
