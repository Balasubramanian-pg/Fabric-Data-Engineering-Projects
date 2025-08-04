# Implement Deployment Pipelines in Microsoft Fabric

Deployment pipelines in Microsoft Fabric are powerful tools designed to streamline and automate the process of propagating content changes across various environments. Imagine a structured workflow where your valuable content, such as reports, datasets, or lakehouses, can seamlessly move from a development environment to a testing environment and finally to a production environment. This systematic approach ensures that all changes are thoroughly developed, tested, and validated before impacting end-users.

> **Tip:** This lab provides hands-on experience in creating a deployment pipeline, assigning workspaces to its stages, crafting initial content in a development workspace, and deploying this content through the pipeline stages—Development, Test, and Production.

> **Note:** To complete this exercise, you need to be a member of the Fabric workspace admin role. For detailed instructions on assigning roles, refer to the official documentation on [Roles in workspaces in Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/get-started/roles-workspaces).

This lab is designed to be completed in approximately **20 minutes**.

---

## Create Workspaces

To begin, we'll set up three separate workspaces, each representing a different stage of our content lifecycle.

1. Open your web browser and navigate to the [Microsoft Fabric home page](https://app.fabric.microsoft.com/home?experience=fabric). Sign in using your Microsoft Fabric credentials.
2. On the left-hand menu bar, select the **Workspaces** icon (🗇).
3. Create a new workspace named **Development**. During the creation process, select a licensing mode that includes Fabric capacity (*Trial*, *Premium*, or *Fabric*).
4. Repeat the steps to create two additional workspaces named **Test** and **Production**.
5. Confirm that your three new workspaces—**Development**, **Test**, and **Production**—are listed and accessible.

> **Note:** If prompted to enter a unique name for your workspaces, append random numbers to the default names (e.g., "Development123," "Test456," or "Production789").

---

## Create a Deployment Pipeline

Next, we'll define the deployment pipeline itself.

1. From the left-hand menu bar, select **Workspaces**.
2. Within the workspace management interface, select **Deployment Pipelines**, then click on **New pipeline**.
3. In the **Add a new deployment pipeline** window, give your pipeline a descriptive and unique name.
4. Accept the default settings in the **Customize your stages** window to set up a standard three-stage pipeline (Development, Test, Production).
5. Select **Create** to finalize the creation of your deployment pipeline.

---

## Assign Workspaces to Pipeline Stages

Now, we'll link our workspaces to the corresponding stages within the pipeline.

1. On the left-hand menu bar, select the deployment pipeline you just created.
2. Within the main window, select **Select** under each deployment stage and choose the workspace that matches the stage name.
3. Select **Assign a workspace** for each deployment stage to confirm the linkage.

---

## Create Content

We'll now generate some Fabric items within our development workspace.

1. In the left-hand menu bar, select **Workspaces**, then choose the **Development** workspace.
2. Within the Development workspace, select **New Item** and choose **Lakehouse**.
3. Name your lakehouse **LabLakehouse** and select **Create**.
4. In the Lakehouse Explorer window, select **Start with sample data** to populate the lakehouse.
5. Return to the pipeline view and navigate to the **Development** stage. Select the **>** until you see **Lakehouses** listed.
6. Notice the orange **X** between the **Development** and **Test** stages, indicating that the stages are not synchronized.
7. Select the downward arrow below the orange **X** and choose **Compare** to see the differences.

---

## Deploy Content Between Stages

Now, we'll deploy our **LabLakehouse** from the **Development** stage to the **Test** and **Production** stages.

1. In the pipeline view, select the **Deploy** button in the **Development** stage to copy the lakehouse to the Test stage.
2. In the **Deploy to next stage** window, select **Deploy**.
3. Notice the orange **X** between the **Test** and **Production** stages. Select the downward-facing arrow below the orange **X** to compare the stages.
4. In the **Test** stage, select **Deploy** to push the content to the Production stage.
5. In the **Deploy to next stage** window, select **Deploy**. A green check mark between all stages indicates that they are now in sync.
6. Verify that the lakehouse has been copied to the **Test** and **Production** workspaces.

---

## Clean Up

To ensure a clean environment, remove the resources created during this lab:

1. In the left-hand navigation bar, select the **Workspaces** icon.
2. Select each workspace (**Development**, **Test**, and **Production**) and choose **Workspace settings** from the top toolbar.
3. In the **General** section, select **Remove this workspace** to delete each workspace.
