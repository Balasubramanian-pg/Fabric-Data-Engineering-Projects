# Implement Deployment Pipelines in Microsoft Fabric

Deployment pipelines in Microsoft Fabric are incredibly powerful tools designed to **streamline and automate the process of propagating content changes** across various environments. 

Imagine a structured workflow where your valuable content, whether it's reports, datasets, or lakehouses, can seamlessly move from a **development environment**, where initial creation and iteration occur, to a **testing environment**, where rigorous quality assurance takes place, and finally to a **production environment**, where it becomes accessible to end-users. 

>[!Tip]This systematic approach ensures that all changes are thoroughly developed, meticulously tested, and validated before they ever impact your users. In this comprehensive lab, you'll gain hands-on experience by creating a new deployment pipeline, meticulously assigning specific workspaces to its distinct stages, crafting some initial content within your development workspace, and then strategically deploying this content through the defined pipeline stages—Development, Test, and Production.

> **Note**: To successfully complete every step of this exercise, it is crucial that you possess the necessary permissions. Specifically, you need to be a **member of the Fabric workspace admin role**. For detailed instructions on how to assign these vital roles within your Microsoft Fabric environment, please refer to the official documentation on [Roles in workspaces in Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/get-started/roles-workspaces).

This lab is designed to be completed in approximately **20 minutes**, allowing for focused learning and practical application of deployment pipeline concepts.

-----

## Create Workspaces

To begin our journey into deployment pipelines, the foundational step is to establish the distinct environments that will represent the different stages of our content lifecycle. We'll set up three separate workspaces, each acting as a dedicated environment, and ensure they are all enabled with Fabric trial capabilities.

1.  Open your web browser and navigate to the [Microsoft Fabric home page](https://app.fabric.microsoft.com/home?experience=fabric). Once there, proceed to **sign in using your Microsoft Fabric credentials**.
2.  On the left-hand side of the interface, within the menu bar, locate and select the **Workspaces** icon (which typically resembles 🗇). This will open your workspace management area.
3.  Initiate the creation of a new workspace. Name this first workspace **Development**. During the creation process, you will be prompted to select a licensing mode. It is essential to choose a mode that includes **Fabric capacity**, such as **Trial**, **Premium**, or **Fabric**, to ensure full functionality.
4.  Repeat the preceding steps (1 and 2) to create two additional workspaces. Name these subsequent workspaces **Test** and **Production**, respectively.
5.  After creating all three workspaces, select the **Workspaces** icon on the left menu bar once more. This will allow you to visually confirm that your three new workspaces—**Development**, **Test**, and **Production**—are now listed and accessible.

>[!Abstract] **Note**: In some instances, you might be prompted to enter a **unique name** for your workspaces, especially if generic names are already in use within your tenant. If this occurs, a simple solution is to append one or more random numbers to the end of the default names. For example, you could name them "Development123," "Test456," or "Production789" to ensure uniqueness.

-----

## Create a Deployment Pipeline

With our foundational workspaces established, the next crucial step is to define the deployment pipeline itself. This pipeline will serve as the automated pathway for our content.

1.  From the persistent left-hand menu bar, click on **Workspaces** to return to your workspace overview.

2.  Within the workspace management interface, locate and select **Deployment Pipelines**. Subsequently, click on **New pipeline** to initiate the pipeline creation wizard.

3.  In the **Add a new deployment pipeline** window that appears, you will be prompted to give your new pipeline a descriptive and **unique name**. Choose a name that clearly identifies its purpose.

4.  The next step involves customizing your stages. For the purpose of this lab, you can **accept the default settings** presented in the **Customize your stages** window. This will set up a standard three-stage pipeline (Development, Test, Production).

5.  Finally, to finalize the creation of your deployment pipeline, select **Create**. Your pipeline is now defined and ready for the next step.

-----

## Assign Workspaces to Pipeline Stages

Now that our deployment pipeline is structured, the critical next action is to link our previously created workspaces to the corresponding stages within the pipeline. This establishes the physical environments for each stage.

1.  On the left-hand menu bar, navigate back and select the **deployment pipeline you just created**. This will open the detailed view of your pipeline.

2.  Within the main window that appears, you will see the different deployment stages (Development, Test, Production). For each of these stages, locate the word **Select** displayed below it. Click on **Select** and, from the dropdown list that appears, carefully choose the name of the workspace that precisely matches the name of that stage (e.g., select the "Development" workspace for the Development stage, the "Test" workspace for the Test stage, and the "Production" workspace for the Production stage).

3.  After selecting the appropriate workspace for each stage, make sure to select **Assign a workspace** for each deployment stage to confirm the linkage.

-----

## Create Content

With our pipeline configured and workspaces assigned, our next task is to generate some actual Fabric items within our initial development workspace. This content will be the subject of our deployment process.

1.  In the left-hand menu bar, select **Workspaces** to return to your list of available workspaces.

2.  From the list, select the **Development** workspace. This is where we will create our initial content.

3.  Within the Development workspace, locate and select **New Item**. This action will open a window presenting various item types you can create.

4.  In the window that appears, choose **Lakehouse**. A new window, **New lakehouse**, will then prompt you to name your lakehouse. Enter the name **LabLakehouse**.

5.  Once named, select **Create** to materialize your new lakehouse within the Development workspace.

6.  Upon creation, the Lakehouse Explorer window will open. To quickly populate your new lakehouse with data for testing, select **Start with sample data**. This will automatically furnish the lakehouse with relevant information.

7.  Now, return to the left-hand menu bar and select the **pipeline you created** earlier. This will bring you back to the pipeline's overview.

8.  Within the **Development** stage of the pipeline view, repeatedly select the **\>** (right arrow) until you specifically see **Lakehouses** listed under the content types. You should now clearly observe that **LabLakehouse** is listed as new content within the Development stage.

9.  Crucially, notice the visual indicator positioned between the **Development** and **Test** stages: an **orange X within a circle**. This prominent orange **X** serves as an important signal, indicating that the content in the Development and Test stages are currently **not synchronized**. There are differences that need to be addressed.

10. To understand these differences in detail, select the **downward arrow** located directly below the orange **X**, and then proceed to choose **Compare**. This action will present a side-by-side comparison. You will immediately observe that **LabLakehouse** presently exists exclusively in the Development stage and is absent from the Test stage.

-----

## Deploy Content Between Stages

Now that our content is created and our pipeline stages are out of sync, the primary objective is to deploy our **LabLakehouse** from its current home in the **Development** stage to both the **Test** and subsequently the **Production** stages.

1.  Within the pipeline view, locate the **Deploy** button positioned within the **Development** stage. Select this button to initiate the process of copying the **LabLakehouse** in its current state to the Test stage.
2.  A **Deploy to next stage** window will appear, prompting for confirmation. Select **Deploy** to proceed with the transfer.
3.  Once the deployment to the Test stage is complete, you will again notice an **orange X** between the **Test** and **Production** stages, indicating a new synchronization gap. Select the **downward-facing arrow** below this orange **X**. The comparison will now show that the lakehouse exists in both the Development and Test stages but has not yet reached the Production stage.
4.  Proceed to the **Test** stage and select its **Deploy** button. This will prepare to push the content to the Production environment.
5.  In the subsequent **Deploy to next stage** confirmation window, select **Deploy** once more. Upon successful deployment, you will observe a satisfying **green check mark** appearing between all stages. This green check mark is the ultimate indicator that all stages in your pipeline are now perfectly in sync and contain identical content.
6.  The beauty of deployment pipelines is that they automatically update the content within the workspaces corresponding to each deployment stage. Let's verify this.
7.  In the left-hand menu bar, select **Workspaces**. Then, select the **Test** workspace. You should now clearly see that **LabLakehouse** has been successfully copied into this workspace.
8.  Similarly, open the **Production** workspace from the **Workspaces** icon on the left menu. You will confirm that **LabLakehouse** has also been accurately copied to the Production workspace, completing the full deployment cycle.

-----

## Clean Up

Congratulations\! In this comprehensive exercise, you've successfully navigated the process of creating a deployment pipeline, meticulously assigned workspaces to its various stages, developed essential content within a development workspace, and then expertly deployed that content across all pipeline stages using the robust capabilities of Microsoft Fabric's deployment pipelines.

To ensure a clean environment and remove the resources created during this hands-on lab:

  * In the left-hand navigation bar, select the **Workspaces** icon. Then, one by one, select the icon for **each workspace** you created (**Development**, **Test**, and **Production**) to view all the items it contains.
  * Within each workspace's view, locate and select **Workspace settings** from the menu on the top toolbar.
  * In the **General** section of the Workspace settings, you will find the option to **Remove this workspace**. Select this option for each of the three workspaces to delete them.

-----

Would you be interested in delving deeper into other facets of Microsoft Fabric, perhaps exploring advanced data integration techniques or optimizing data workloads?
