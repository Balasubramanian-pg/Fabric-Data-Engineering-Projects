# Using Data Activator in Microsoft Fabric  

> **IMPORTANT**: This exercise is deprecated and will be removed or updated soon. The instructions may no longer be accurate, and the exercise is unsupported.

## Overview  
Data Activator in Microsoft Fabric enables automated actions based on real-time data changes. This lab will guide you through:  
- Creating a workspace  
- Setting up a reflex to monitor data  
- Configuring triggers and actions  
- Cleaning up resources  

**Estimated Time**: 30 minutes  

> **Prerequisite**: A [Microsoft Fabric trial](https://learn.microsoft.com/fabric/get-started/fabric-trial) is required.  

---

## 1. Create a Workspace  
1. Navigate to the [Microsoft Fabric home page](https://app.fabric.microsoft.com).  
2. Select **Data Activator** from the services menu.  
3. In the left menu bar, select **Workspaces** (icon: &#128455;).  
4. Create a new workspace:  
   - Name it as desired  
   - Select a licensing mode with Fabric capacity (*Trial*, *Premium*, or *Fabric*)  
5. Verify the new workspace opens empty.  

![Empty workspace in Fabric](./Images/new-workspace.png)  

---

## 2. Scenario Setup  
**Business Context**:  
You're a data analyst for a shipping company monitoring medical prescription deliveries to Redmond. These refrigerated packages must maintain temperatures between 33°F and 41°F.  

**Objective**:  
Create a reflex that:  
- Monitors package temperatures  
- Triggers email alerts when:  
  - Temperature exits the safe range  
  - Package is destined for Redmond  
  - Contains refrigerated medicine  

---

## 3. Create a Reflex  
1. From the Data Activator home screen, select **Reflex (Preview)**.  
2. Use the provided sample data by selecting **Use Sample Data**.  
3. Rename the default reflex:  
   - Click the dropdown next to the auto-generated name  
   - Enter "Contoso Shipping Reflex"  

![Reflex home screen](./Images/data-activator-reflex-home-screen.png)  

---

## 4. Understand Reflex Interfaces  
### Design Mode  
- **Triggers**: Define conditions for actions  
- **Properties**: Data fields for monitoring  
- **Events**: Data streams to analyze  

![Design mode interface](./Images/data-activator-design-tab.png)  

### Data Mode  
- View sample data streams:  
  - Package Delivery Status  
  - Package In Transit  
  - Package Delivered  

![Data mode interface](./Images/data-activator-data-tab.png)  

---

## 5. Create a Custom Object  
1. In **Data Mode**, select the *Package In Transit* event.  
2. Note key columns: *PackageId*, *Temperature*, *ColdChainType*, *City*, *SpecialCare*.  
3. Select **Assign your data** → **Assign to new object**:  
   - **Object Name**: Redmond Packages  
   - **Key Column**: PackageId  
   - **Properties**: City, ColdChainType, SpecialCare, Temperature  
4. Save and return to Design Mode.  

![New object creation](./Images/data-activator-design-tab-new-object.png)  

---

## 6. Configure the Trigger  
1. In Design Mode, select *Package In Transit* under *Redmond Packages*.  
2. Create **New Trigger** → Name it "Medicine temp out of range".  

### Trigger Conditions  
1. Select existing *Temperature* property.  
2. Set condition type:  
   - Choose **Numeric** → **Exits range**  
   - Set range: 33 to 41  

### Filters  
Add these filters (all must be true):  
1. *City* **Equals** Redmond  
2. *SpecialCare* **Equals** Medicine  
3. *ColdChainType* **Equals** Refrigerated  

![Trigger condition setup](./Images/data-activator-trigger-select-condition-add-filter-additional.png)  

### Action Configuration  
1. Select **Email** action.  
2. Configure:  
   - **To**: Your account (default)  
   - **Subject**: "Redmond Medical Package outside acceptable temperature range"  
   - **Headline**: "Temperature too high or too low"  
   - **Additional Info**: Temperature  

![Email action setup](./Images/data-activator-trigger-define-action.png)  

3. **Save** and **Start** the trigger.  

---

## 7. Enhance the Trigger  
1. Add *PackageId* property:  
   - In Design Mode, select *Packages in Transit* → **New Property**  
   - Map to *PackageId* column  
2. Edit the trigger:  
   - Add *PackageId* to "Additional information"  
   - **Update** (not Save) to apply changes to running trigger  

![Updated trigger](./Images/data-activator-trigger-updated.png)  

3. **Stop** the trigger when complete.  

---

## 8. Clean Up Resources  
1. Navigate to workspace contents.  
2. Select **...** → **Workspace settings**.  
3. Choose **Remove this workspace**.  

---

## Conclusion  
You've successfully:  
1. Created a Data Activator reflex  
2. Configured conditional triggers  
3. Implemented automated email alerts  
4. Managed reflex properties  

This demonstrates how Fabric's Data Activator can automate responses to critical data changes in real-time scenarios.
