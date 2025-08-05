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

DAX Studio is a feature-rich tool for DAX authoring, diagnosis, performance tuning, and analysis. It provides object browsing, integrated tracing, query execution breakdowns with detailed statistics, and DAX syntax highlighting and formatting.

### Step 1: Download and Install DAX Studio

1. Navigate to the [DAX Studio downloads page](https://daxstudio.org/downloads/).
2. Download and run the installer for the latest version of DAX Studio.
3. Follow the installation steps, selecting the default options as prompted.
4. Upon completion, launch DAX Studio.

### Step 2: Connect to Power BI Desktop

1. In the **Connect** window, select the **Power BI / SSDT Model** option.
2. Ensure the **Sales Analysis - Use tools to optimize Power BI performance** model is selected in the dropdown list.
3. Select **Connect**.

### Step 3: Optimize a Query Using DAX Studio

1. Download the [Monthly Profit Growth.dax](https://aka.ms/fabric-optimize-dax) file and save it to your local computer.
2. In DAX Studio, open the **Monthly Profit Growth.dax** file.
3. Review the comments and the query in the file.
4. Run a server trace to record detailed timing information for performance profiling by selecting **Server Timings** on the **Home** ribbon tab.
5. Run the script and review the query results and server timing statistics.
6. Modify the query to use the second measure by replacing the word **Bad** with **Better** at line 72.
7. Run the updated query and review the server timing statistics again to compare performance.

### Conclusion

Close all applications to conclude this exercise. There is no need to save the files.

---
