# Create DAX Calculations in Power BI Desktop

## **Context**

In this comprehensive lab, you'll dive deep into **Data Analysis Expressions (DAX)**, the powerful formula language used in Power BI. You'll gain hands-on experience by creating various DAX calculations, including calculated tables, calculated columns, and essential measures, all designed to enrich your data model and enable insightful analysis.

In this lab, you'll master how to:

  * **Create calculated tables** to expand your data model.
  * **Develop calculated columns** to add new attributes to your existing tables.
  * **Construct measures** to perform dynamic aggregations and calculations on your data.

**This lab is estimated to take approximately 45 minutes to complete.**

-----

## Get Started

To begin this exercise, you'll first need to download the starter Power BI Desktop file and extract its contents.

1.  Open your preferred web browser and enter the following URL to download the necessary zip folder:

    `https://github.com/MicrosoftLearning/mslearn-fabric/raw/main/Allfiles/Labs/14/14-create-dax.zip`

2.  Once the download is complete, **extract the contents of the zip folder** to the following directory on your computer: **C:\\Users\\Student\\Downloads\\14-create-dax**.

3.  Navigate to the extracted folder and **open the `14-Starter-Sales Analysis.pbix` file** using Power BI Desktop.

    > ***Note**: You might be prompted to sign in; you can simply dismiss this by selecting **Cancel**. Close any other informational windows that may appear. If prompted to apply changes, select **Apply Later**.*

-----

## Create the Salesperson Calculated Table

In this task, you'll create a new table named **Salesperson** using DAX. This calculated table will have a direct relationship to your **Sales** fact table, providing a clean dimension for sales analysis.

A calculated table is defined by first specifying the new table's name, followed by an equals sign (`=`), and then a DAX formula that produces a table. Remember, the chosen table name must not already exist in your data model. The formula bar in Power BI Desktop provides helpful features like auto-complete, Intellisense, and color-coding to make entering your DAX formulas quick and accurate.

1.  In Power BI Desktop, ensure you are in **Report view**. On the **Modeling** ribbon, within the **Calculations** group, select **New Table**.

2.  The formula bar will appear directly beneath the ribbon. Type **`Salesperson =`**, then press **Shift+Enter** to move to the next line for better readability. On the new line, type **`'Salesperson (Performance)'`**, and finally, press **Enter** to commit the DAX formula.

    > **Note**: *For your convenience, all DAX definitions used in this lab can be easily copied from the `Snippets.txt` file, which is located in the `14-create-dax` folder you extracted.*

    > *This particular DAX table definition effectively creates a **copy of the existing `Salesperson (Performance)` table**. It's important to understand that this operation only copies the data; model properties such as column visibility, data formatting, and other settings are not automatically transferred to the new calculated table.*

3.  In the **Data** pane (typically on the right side), observe the **Salesperson** table. You'll notice that its table icon now includes an **additional calculator symbol** in front of it, clearly denoting that it is a **calculated table**.

    > ***Note**: Calculated tables are powerful because they allow you to define new tables dynamically using DAX formulas that return a table. However, it’s crucial to understand that these tables **materialize and store their values within your data model**, which can increase the overall size of the model. They are recomputed whenever any of their formula dependencies are refreshed. For instance, in this data model, if new (future) date values are loaded into the underlying tables, this calculated table will be re-evaluated.*

    > *Unlike tables sourced from Power Query, calculated tables **cannot be used to load data from external data sources**. Their primary function is to transform and reshape data that has already been loaded into the data model.*

4.  Switch to **Model view** (by selecting the model icon on the left-hand navigation). Locate the newly created **Salesperson** table. (You might need to adjust your view or use the search functionality to find it if your model diagram is large.)

5.  Now, create a new relationship: **drag the `EmployeeKey` column from the `Salesperson` table** and **drop it onto the `EmployeeKey` column in the `Sales` table**. This establishes the direct link between your new dimension table and the sales facts.

6.  Locate the **inactive relationship** (indicated by a dashed line) between the original **`Salesperson (Performance)`** table and the **`Sales`** table. Right-click this inactive relationship and select **Delete**. When prompted to confirm the deletion, select **Yes**. This ensures that only your new, active relationship is used for sales analysis.

7.  In the **Salesperson** table, multi-select the following columns: **`EmployeeID`**, **`EmployeeKey`**, and **`UPN`**. Then, hide these columns by setting their **Is Hidden** property to **Yes** in the Properties pane. This cleans up the data pane for report authors.

8.  In the model diagram, select the **Salesperson** table.

9.  In the **Properties** pane (usually on the right), locate the **Description** box. Enter the following description: **`Salesperson related to Sales`**.

    > *You might recall that descriptions like this are invaluable for report authors, as they appear as helpful tooltips in the **Data** pane when a user hovers their cursor over a table or field, providing immediate context.*

10. For the original **`Salesperson (Performance)`** table, set its description to: **`Salesperson related to region(s)`**.

*Your data model now offers two distinct perspectives for analyzing salespeople. The newly created **Salesperson** table facilitates direct analysis of sales attributed to individual salespeople. Conversely, the **Salesperson (Performance)** table enables analysis of sales made within the sales region(s) that are assigned to a salesperson, providing regional context.*

-----

## Create the Date Table

In this task, you'll create a new **Date** table. This table is crucial for time-based analysis and will serve as a robust date dimension in your model.

1.  Switch to **Table view** (by selecting the table icon on the left-hand navigation). On the **Home** ribbon tab, within the **Calculations** group, select **New Table**.

2.  In the formula bar, enter the following DAX expression:

    ```dax
    Date =
    CALENDARAUTO(6)
    ```

    > *The `CALENDARAUTO()` function is a convenient DAX function that returns a single-column table populated with date values. Its "auto" behavior means it intelligently scans all date columns present within your data model to determine the earliest and latest date values. Based on this range, it then generates one row for each date within that span. Importantly, it extends this range in either direction to ensure that full calendar years of data are always stored, preventing incomplete years at the boundaries of your data.*

    > *This function can accept a single, optional argument: the last month number of a year. If this argument is omitted, the default value is 12, implying that December is considered the last month of the year. In this specific case, we've entered `6`, which means that June is designated as the last month of the fiscal year, aligning with Adventure Works' fiscal calendar.*

3.  Observe the newly created column of date values. Notice that they are formatted according to US regional settings (i.e., mm/dd/yyyy).

4.  In the bottom-left corner of the interface, within the status bar, you'll find the table statistics. These statistics confirm that your new `Date` table contains **1826 rows of data**, which precisely represents five full years of dates.

-----

## Create Calculated Columns

In this task, you'll enhance your **Date** table by adding several calculated columns. These new columns will enable more flexible filtering and grouping of your data by different time periods. You'll also create a special calculated column specifically to control the sort order of other columns, ensuring chronological display.

> **Note**: *For your convenience and to save time, all DAX definitions for calculated columns and measures in this lab can be directly copied from the **`Snippets.txt`** file, which you extracted earlier.*

1.  While in **Table view**, on the **Table Tools** contextual ribbon (which appears when the `Date` table is selected), within the **Calculations** group, select **New Column**.

    > *A calculated column is defined by first specifying the new column's name, followed by an equals sign (`=`), and then a DAX formula that returns a single-value result for each row. The chosen column name must not already exist within the table.*

2.  In the formula bar, type (or copy from the snippets file) the following DAX expression, and then press **Enter**:

    ```dax
    Year =
    "FY" & YEAR('Date'[Date]) + IF(MONTH('Date'[Date]) > 6, 1)
    ```

    > *This formula intelligently derives the fiscal year. It uses the year value from the date but adds one to the year if the month falls after June. This accurately reflects how fiscal years are calculated at Adventure Works.*

3.  Using the definitions provided in the **snippets file**, create the following two additional calculated columns for your **Date** table:

      * **`Quarter`**
      * **`Month`**

4.  Verify that these new columns (`Year`, `Quarter`, and `Month`) have been successfully added to your **Date** table in Table view.

5.  To validate the functionality of your new calculated columns, switch to **Report view** (by selecting the report icon on the left-hand navigation).

6.  To create a clean canvas for your validation, select the **plus icon** next to **Page 1** to add a new report page.

7.  To add a matrix visual to this new report page, go to the **Visualizations** pane (typically on the right side) and select the **matrix visual type**.

    > *Tip: You can hover your cursor over each icon in the Visualizations pane to reveal a tooltip describing the type of visual.*

8.  In the **Data** pane, from within the **Date** table, **drag the `Year` field** into the **Rows** well/area of your matrix visual.

9.  Now, **drag the `Month` field** from the **Date** table into the **Rows** well/area, positioning it directly beneath the `Year` field. This creates a hierarchy in your matrix.

10. At the top-right corner of the matrix visual (or sometimes at the bottom, depending on your visual's placement), select the **forked-double arrow icon** (which represents "Expand all down one level"). This action will expand all the years to show their corresponding months.

11. Observe the matrix. You'll notice that while the years expand to show months, the **months are currently sorted alphabetically** (e.g., April, August, December) rather than chronologically (e.g., January, February, March).

    > *By default, text values in Power BI sort alphabetically, numbers sort from smallest to largest, and dates sort from earliest to latest. To achieve chronological month sorting, we need a specific strategy.*

12. To customize the sort order of the **Month** field, switch back to **Table view**.

13. Add another calculated column to the **Date** table, using the following DAX expression:

    ```dax
    MonthKey =
    (YEAR('Date'[Date]) * 100) + MONTH('Date'[Date])
    ```

    > *This formula cleverly computes a unique numeric value for each year/month combination. For example, July 2017 would become 201707, ensuring a consistent chronological sort order.*

14. In Table view, verify that the new **MonthKey** column indeed contains these numeric values (e.g., 201707 for July 2017, 201708 for August 2017, etc.).

15. Switch back to **Report view**. In the **Data** pane, select the **Month** field within the **Date** table.

16. On the **Column Tools** contextual ribbon (which appears when `Month` is selected), within the **Sort** group, select **Sort by Column**, and then choose **MonthKey** from the dropdown list.

17. In the matrix visual, observe the months once again. You'll now notice that they are correctly **sorted chronologically**, thanks to the `MonthKey` column.

-----

## Complete the Date Table

In this task, you'll finalize the design of your **Date** table. This involves hiding the `MonthKey` column (as it's only for sorting, not display), creating a useful hierarchy for time-based navigation, and then establishing the necessary relationships to your **Sales** and **Targets** tables.

1.  Switch to **Model view**. In the **Date** table, **hide the `MonthKey` column** by setting its **Is Hidden** property to **Yes**. This keeps your Data pane clean for report authors.

2.  On the **Data** pane (right side), select the **Date** table. Right-click on the **Year** column, and then select **Create hierarchy**.

3.  The new hierarchy will initially have a default name. **Right-click on this newly created hierarchy** and select **Rename**. Rename it to **`Fiscal`**.

4.  Add the remaining two time-based fields to your new **Fiscal** hierarchy. Select **`Quarter`** and **`Month`** in the **Data** pane, right-click, select **Add to hierarchy**, and then choose **Fiscal**.

5.  Now, create the following two essential model relationships to connect your `Date` dimension to your fact tables:

      * **`Date | Date`** to **`Sales | OrderDate`**
      * **`Date | Date`** to **`Targets | TargetMonth`**

    > *The labs use a shorthand notation to reference a field. It will look like this: **`Sales | Unit Price`**. In this example, **`Sales`** is the table name, and **`Unit Price`** is the field name.*

6.  To improve model clarity and guide report authors to use your date dimension, **hide the following two columns**:

      * **`Sales | OrderDate`**
      * **`Targets | TargetMonth`**

-----

## Mark the Date Table

In this task, you'll officially **mark the Date table as a date table** within Power BI. This crucial step enables powerful built-in date intelligence features, like automatic time hierarchies and time-based filtering.

1.  Switch to **Report view**. In the **Data** pane, ensure you select the **Date table itself** (not just the `Date` field within it).

2.  On the **Table Tools** contextual ribbon (which appears when the `Date` table is selected), within the **Calendars** group, select **Mark as Date Table**.

3.  In the **Mark as a Date Table** window that appears, slide the **Mark as a Date Table** property to **Yes**. Then, in the **Choose a date column** dropdown list, select **Date**. Finally, select **Save**.

4.  **Save the Power BI Desktop file** to preserve all your changes.

> *By marking this table as a date table, Power BI Desktop now intelligently understands that this table defines date (time) logic for your model. This specific design approach for a date table is particularly well-suited when you don’t have an existing date dimension table directly available in your data source. However, if you are working with a data warehouse that already contains a comprehensive date dimension table, it would generally be more appropriate to load date data from that existing dimension rather than "redefining" date logic within your Power BI data model.*

-----

## Create Simple Measures

In this task, you'll create fundamental measures. These "simple measures" primarily focus on aggregating values from a single column or counting rows within a specific table, forming the basis for quantitative analysis.

1.  In **Report view**, navigate to **Page 2**. In the **Data** pane, **drag the `Sales | Unit Price` field** into the existing matrix visual.

2.  In the visual fields pane (typically located beneath the **Visualizations** pane), within the **Values** field well/area, observe that **Unit Price** is currently listed as **Average of Unit Price**. Select the **down-arrow** next to `Unit Price`, and then review the available menu options for aggregation.

    > *By default, Power BI allows report authors to decide how visible numeric columns will be summarized (or if they will be summarized at all) directly at report design time. While flexible, this can sometimes lead to inappropriate or inconsistent reporting, especially for sensitive metrics like prices (e.g., summing prices often makes no business sense). Many data modelers prefer to eliminate this ambiguity. They choose to **hide these raw numeric columns** and, instead, explicitly **expose aggregation logic defined within measures**. This is precisely the approach you'll now adopt in this lab to ensure robust and appropriate reporting.*

3.  To create your first measure, in the **Data** pane, **right-click the `Sales` table**, and then select **New Measure**.

4.  In the formula bar, add the following DAX measure definition:

    ```dax
    Avg Price =
    AVERAGE(Sales[Unit Price])
    ```

5.  Add the newly created **`Avg Price`** measure to the matrix visual. Notice that it produces the **same result** as the `Unit Price` column did, but likely with slightly different formatting.

6.  In the **Values** well of the matrix visual, open the context menu for the **`Avg Price`** field (by clicking its down-arrow). Observe that it is **not possible to change the aggregation technique** for a measure, as its aggregation is hard-coded within its DAX definition.

    > *This demonstrates a key characteristic of measures: their aggregation behavior is explicitly defined within the DAX formula and cannot be modified by report authors.*

7.  Using the definitions provided in the **snippets file**, create the following five additional measures for the **Sales** table:

      * **`Median Price`**
      * **`Min Price`**
      * **`Max Price`**
      * **`Orders`**
      * **`Order Lines`**

    > *The `DISTINCTCOUNT()` function utilized in the **`Orders`** measure is designed to count only unique instances of order numbers, effectively ignoring any duplicates. Conversely, the `COUNTROWS()` function, employed in the **`Order Lines`** measure, simply counts the total number of rows within a specified table. In this context, the number of distinct orders is derived by counting the unique values in the **`SalesOrderNumber`** column, while the number of order lines corresponds directly to the total number of rows in the `Sales` table, where each row represents a line item of an order.*

8.  Switch to **Model view**. Now, **multi-select the four price measures**: **`Avg Price`**, **`Max Price`**, **`Median Price`**, and **`Min Price`**.

9.  For this multi-selection of measures, configure the following requirements in the Properties pane:

      * **Set the format to two decimal places** for all selected price measures.
      * **Assign them to a display folder named `Pricing`**. This will group them logically in the Data pane for easier navigation.

10. **Hide the `Unit Price` column** in the `Sales` table.

    > *The `Unit Price` column is now no longer directly available to report authors in the Data pane. Instead, they are directed to use the well-defined pricing measures you've added to the model. This thoughtful design approach ensures that report authors will not inappropriately aggregate prices, for instance, by simply summing them, which typically leads to misleading analysis.*

11. **Multi-select the `Order Lines` and `Orders` measures**. Then, configure the following requirements for these measures:

      * **Set the format to use the thousands separator** (e.g., 1,000 instead of 1000).
      * **Assign them to a display folder named `Counts`**.

12. Switch back to **Report view**. In the **Values** well/area of the matrix visual, locate the **`Unit Price`** field (if it's still there from previous steps) and select **X** to remove it.

13. To give your visual more space, **increase the size of the matrix visual** to fill the full page width and height.

14. Add the following five newly created measures to the matrix visual:

      * **`Median Price`**
      * **`Min Price`**
      * **`Max Price`**
      * **`Orders`**
      * **`Order Lines`**

15. Carefully verify that the results displayed in the matrix visual appear sensible and are correctly formatted according to your specifications.

-----

## Create Additional Measures

In this task, you'll create more sophisticated measures that leverage more complex DAX formulas. These measures will provide deeper insights into your sales targets and performance.

1.  In **Report view**, select **Page 1** and carefully review the table visual displayed there, specifically noting the total value for the **Target** column.

2.  Select the table visual. In the **Visualizations** pane, remove the **Target** field from the visual.

3.  To prepare for a more controlled measure, **rename the `Targets | Target` column** to **`Targets | TargetAmount`**.

    > *Tip: There are several convenient ways to rename a column in Report view: In the **Data** pane, you can right-click the column and then select **Rename**. Alternatively, you can double-click the column, or simply press **F2** when the column is selected.*

4.  Create the following new measure on the **Targets** table:

    ```dax
    Target =
    IF(
        HASONEVALUE('Salesperson (Performance)'[Salesperson]),
        SUM(Targets[TargetAmount])
    )
    ```

    > *The `HASONEVALUE()` function is a powerful DAX function that evaluates whether a single, distinct value in the **`Salesperson`** column is currently filtered in the report context. When this condition is `TRUE` (meaning only one salesperson is selected or in context), the expression proceeds to return the sum of target amounts specifically for that salesperson. Conversely, if the condition is `FALSE` (meaning multiple salespeople are in context or no specific salesperson is filtered), the measure returns `BLANK`, preventing misleading aggregated totals.*

5.  **Format the `Target` measure for zero decimal places**.

    > *Tip: You can quickly access formatting options by selecting the measure and then using the **Measure Tools** contextual ribbon that appears.*

6.  **Hide the `TargetAmount` column**.

    > *Tip: You can right-click the column in the **Data** pane and then select **Hide** to remove it from the view of report authors.*

7.  Add the newly created **`Target`** measure to the table visual.

8.  Observe the table visual. You'll notice that the **Total for the `Target` column is now `BLANK`**, which is the intended behavior due to the `HASONEVALUE()` logic, preventing an inappropriate sum across multiple salespeople.

9.  Using the definitions from the **snippets file**, create the following two additional measures for the **Targets** table:

      * **`Variance`**
      * **`Variance Margin`**

10. **Format the `Variance` measure for zero decimal places**.

11. **Format the `Variance Margin` measure as a percentage with two decimal places**.

12. Add both the **`Variance`** and **`Variance Margin`** measures to the table visual.

13. **Resize the table visual** as needed to ensure all columns and rows are clearly visible without truncation.

    > *At this point, it might appear that all salespeople are falling short of their targets. However, remember that the table visual is not yet filtered by a specific time period, so these are overall variances.*

14. At the top-right corner of the **Data** pane, **collapse and then expand** the pane.

    > *Collapsing and reopening the pane effectively refreshes its content and organization.*

15. Notice that the **Targets** table now appears prominently at the top of the list in the Data pane.

    *Tables that contain **only visible measures** (and no visible columns) are automatically prioritized and listed at the top of the Data pane, making them easily discoverable for report authors.*

-----

## Lab Complete

Congratulations\! You have successfully completed this lab, mastering the creation of calculated tables, calculated columns, and various types of measures using DAX in Power BI Desktop. You now have a more robust and insightful data model ready for advanced analysis and reporting.