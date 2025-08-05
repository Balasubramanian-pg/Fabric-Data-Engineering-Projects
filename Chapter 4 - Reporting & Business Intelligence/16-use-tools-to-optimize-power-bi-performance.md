# Optimize Power BI Performance Using External Tools

**Module:** Optimize Enterprise-Scale Tabular Models

**Duration:** Approximately 30 minutes

**Prerequisite:** A [Microsoft Fabric trial](https://learn.microsoft.com/fabric/get-started/fabric-trial) is required to complete this exercise.

## Introduction

In this lab, you will learn how to use two external tools to develop, manage, and optimize data models and DAX queries:

- Best Practice Analyzer (BPA) in Tabular Editor
- DAX Studio

## Exercise 1: Use Best Practice Analyzer

### Overview

Best Practice Analyzer (BPA) is a free third-party tool that identifies potential modeling issues and suggests improvements for model design and performance. It provides recommendations for naming conventions, user experience, and common optimizations.

### Step 1: Download and Install Tabular Editor 2

Tabular Editor is an alternative tool for authoring tabular models for Analysis Services and Power BI. Tabular Editor 2 is an open-source project that can edit a BIM file without accessing any data in the model.

1. Ensure Power BI Desktop is closed.
2. In Microsoft Edge, navigate to the [Tabular Editor Release page](https://github.com/TabularEditor/TabularEditor/releases).
3. Scroll down to the **Assets** section and select the **TabularEditor.Installer.msi** file to initiate the installation.
4. Once the download is complete, select **Open file** to run the installer.
5. Follow the installation steps, selecting **Next** and agreeing to the license terms as prompted.
6. When the installation is complete, select **Close**.

### Step 2: Set Up Power BI Desktop

1. Download the [Sales Analysis starter file](https://aka.ms/fabric-optimize-starter) and save it to a location you will remember.
2. Open the downloaded file in Power BI Desktop.
3. Select the **External Tools** ribbon tab and note that you can launch Tabular Editor from this tab.

### Step 3: Review the Data Model

1. In Power BI Desktop, switch to **Model** view.
2. Use the model diagram to review the model design.

### Step 4: Load BPA Rules

1. On the **External Tools** ribbon, select **Tabular Editor**.
2. To load the BPA rules, select the **C# Script** tab.
3. Paste the following script into the script tab:

```csharp
System.Net.WebClient w = new System.Net.WebClient();
string path = System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData);
string url = "https://raw.githubusercontent.com/microsoft/Analysis-Services/master/BestPracticeRules/BPARules.json";
string downloadLoc = path + @"\TabularEditor\BPARules.json";
w.DownloadFile(url, downloadLoc);
```

4. Run the script by selecting the **Run script** command on the toolbar.
5. Close and reopen Tabular Editor to use the BPA rules.

### Step 5: Review and Address BPA Issues

1. In Tabular Editor, select **Tools** > **Manage BPA Rules**.
2. Review the list of BPA rules and disable any rules as needed.
3. Select **Tools** > **Best Practice Analyzer** to review the BPA results.
4. Address the identified issues by ignoring items, navigating to objects, or generating fix scripts as needed.
5. Save the model changes and close Tabular Editor.
6. Save the Power BI Desktop file.

## Exercise 2: Use DAX Studio

### Overview

DAX Studio is an advanced tool designed for Data Analysis Expressions (DAX) authoring, diagnosis, performance tuning, and analysis. It is an essential tool for anyone working with Power BI, Analysis Services, or Power Pivot in Excel. DAX Studio provides a wide range of features to enhance productivity and optimize performance:

- **Object Browsing:** Easily navigate and explore the metadata of your data model, including tables, columns, measures, and relationships.
- **Integrated Tracing:** Capture and analyze detailed trace information to understand query execution and identify performance bottlenecks.
- **Query Execution Breakdowns:** Get detailed statistics on query execution, including duration, CPU usage, and storage engine queries.
- **DAX Syntax Highlighting and Formatting:** Write and format DAX code with syntax highlighting, making it easier to read and debug.
- **Advanced Diagnostics:** Use advanced diagnostic features to analyze and optimize DAX queries, including server timings and query plans.
- **Metadata Exploration:** Explore the metadata of your data model to understand its structure and components.
- **Export and Import:** Easily export and import DAX queries and measures for sharing and collaboration.

### Step 1: Download and Install DAX Studio

To get started with DAX Studio, follow these steps to download and install the latest version:

1. **Navigate to the DAX Studio Downloads Page:**
   - Open your web browser and go to the [DAX Studio downloads page](https://daxstudio.org/downloads/).

2. **Download the Installer:**
   - On the downloads page, locate the latest version of DAX Studio.
   - Click on the download link for the installer file (e.g., `DaxStudio_3_X_XX_setup.exe`).

3. **Run the Installer:**
   - Once the download is complete, navigate to the location where the installer file was saved.
   - Double-click on the installer file to run it. If prompted by User Account Control, select **Yes** to allow the app to make changes to your device.

4. **Follow the Installation Steps:**
   - In the DAX Studio installer window, you will be presented with several installation options. Select the default options as prompted:
     - **Install for all users (recommended):** Choose this option to make DAX Studio available to all users on the computer.
     - **License Agreement:** Read and accept the license agreement to proceed with the installation.
     - **Destination Location:** Use the default destination location for the installation.
     - **Components:** Select the default components to install.
     - **Start Menu Folder:** Use the default start menu folder for shortcuts.
     - **Create a Desktop Shortcut:** Select this option to create a desktop shortcut for easy access to DAX Studio.

5. **Complete the Installation:**
   - Click **Install** to begin the installation process.
   - Once the installation is complete, ensure that the **Launch DAX Studio** option is selected, and click **Finish** to launch DAX Studio.

### Step 2: Connect to Power BI Desktop

After installing DAX Studio, the next step is to connect it to your Power BI Desktop model:

1. **Launch DAX Studio:**
   - If DAX Studio is not already open, launch it from the desktop shortcut or the start menu.

2. **Connect to Power BI Desktop:**
   - In the **Connect** window that appears, you will see several options for connecting to different types of data models.
   - Select the **Power BI / SSDT Model** option. This option allows you to connect to a Power BI Desktop model or an Analysis Services Tabular Model in SQL Server Data Tools (SSDT).

3. **Select the Model:**
   - In the dropdown list, you should see the **Sales Analysis - Use tools to optimize Power BI performance** model. Ensure that this model is selected.
   - If you do not see the model in the dropdown list, make sure that the corresponding Power BI Desktop file is open.

4. **Establish the Connection:**
   - Click the **Connect** button to establish the connection to the selected model.
   - Once connected, you will be able to explore the metadata, run DAX queries, and use the various features of DAX Studio to optimize and analyze your data model.

By following these steps, you will have successfully downloaded, installed, and connected DAX Studio to your Power BI Desktop model, enabling you to leverage its powerful features for DAX authoring, diagnosis, and performance tuning.

### Step 3: Review the Data Model

In this task, you will thoroughly review the data model to understand its structure and components. This understanding is crucial for effectively using the Best Practice Analyzer (BPA) to detect and fix issues.

1. **Switch to Model View:**
   - In Power BI Desktop, locate the left-hand navigation pane.
   - Click on the **Model** icon, which looks like a small diagram. This will switch your view to the Model view, where you can see the visual representation of your data model.

2. **Understand the Model Diagram:**
   - The Model view displays a diagram of your data model, showing all the tables and the relationships between them.
   - Tables are represented as boxes, and relationships are represented as lines connecting these boxes.
   - Take a moment to familiarize yourself with the layout. Notice how the tables are arranged and connected.

3. **Identify Tables and Their Roles:**
   - **Fact Tables:** These tables contain the primary data you want to analyze. In this model, the **Sales** table is the fact table, storing sales order details.
   - **Dimension Tables:** These tables contain attributes or dimensions that describe the data in the fact table. In this model, there are eight dimension tables, including **Category**, **Subcategory**, **Product**, and others.
   - **Snowflake Schema:** Notice that the model uses a snowflake schema for the product dimension, where the **Category**, **Subcategory**, and **Product** tables are connected in a hierarchical manner.

4. **Examine Relationships:**
   - Click on the lines connecting the tables to see the details of the relationships.
   - Note the type of relationships (e.g., one-to-many, many-to-one) and the columns used to create these relationships.
   - Ensure that the relationships are correctly defined and that there are no missing or incorrect connections.

5. **Review Table Contents:**
   - Double-click on any table to see its contents and structure.
   - Examine the columns within each table, their data types, and any measures or calculated columns that have been created.
   - Pay attention to the naming conventions used for tables and columns, as consistent and clear naming is essential for a well-organized model.

6. **Check for Data Issues:**
   - Look for any obvious data issues, such as missing values, duplicate entries, or inconsistent data types.
   - Note any tables or columns that might need further investigation or cleaning.

7. **Document Your Observations:**
   - As you review the model, take notes on any observations or potential issues you identify.
   - This documentation will be helpful when you use the Best Practice Analyzer to detect and address specific issues in the model.

By thoroughly reviewing the data model, you will gain a comprehensive understanding of its structure and components. This knowledge will enable you to effectively use the Best Practice Analyzer to optimize and improve the model.

### Conclusion

Close all applications to conclude this exercise. There is no need to save the files.

---


