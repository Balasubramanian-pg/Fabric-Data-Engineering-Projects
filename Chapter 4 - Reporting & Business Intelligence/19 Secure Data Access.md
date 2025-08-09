# **Lab: Secure Data Access in Microsoft Fabric**  
**Module: Secure Data Access in Microsoft Fabric**  

---

## **Lab Overview**  
**Objective:**  
Implement and validate multi-layered security controls in Microsoft Fabric, including:  
1. **Workspace-level access** (roles and permissions)  
2. **Item-level security** (granular permissions for warehouses/lakehouses)  
3. **OneLake data access roles** (folder-level restrictions)  

**Learning Outcomes:**  
1. Configure workspace roles to control user visibility and actions.  
2. Apply item permissions to restrict access to specific artifacts.  
3. Use OneLake data access roles (Preview) for column/folder-level security.  

**Estimated Time:** **45 minutes**  

> **Prerequisite:**  
> - A [Microsoft Fabric trial](https://learn.microsoft.com/fabric/get-started/fabric-trial) account.  
> - Two user accounts (e.g., *Workspace Admin* and *Test User*).  

---

## **Lab Structure**  
1. **Set Up a Workspace** → Create a workspace with sample data.  
2. **Workspace Access Controls** → Assign roles and test visibility.  
3. **Item-Level Security** → Restrict access to specific artifacts.  
4. **OneLake Data Access Roles** → Implement folder-level permissions.  
5. **Clean Up** → Delete the workspace post-lab.  

---

## **Step 1: Set Up a Workspace**  
**Purpose:** Create a workspace with sample data for testing security controls.  

### **Task A: Create a Workspace**  
1. Sign in to [Microsoft Fabric](https://app.fabric.microsoft.com).  
2. Navigate to **Workspaces** → **New Workspace**.  
3. Name it (e.g., `SecurityLab`), select a **Fabric-enabled** license (Trial/Premium), and create.  

![Empty workspace](./Images/new-empty-workspace.png)  

### **Task B: Add Sample Data**  
1. **Create a Data Warehouse:**  
   - Select **Create** → **Data Warehouse** → **Sample warehouse**.  
   - Name it (e.g., `Sales_Warehouse`).  

2. **Create a Lakehouse:**  
   - Select **New Item** → **Lakehouse** → Name it (e.g., `HR_Lakehouse`).  
   - Choose **Start with sample data** to populate tables.  

![Sample lakehouse](./Images/new-sample-lakehouse.png)  

---

## **Step 2: Workspace Access Controls**  
**Purpose:** Test how workspace roles (Admin, Viewer) impact user access.  

### **Task A: Assign Workspace Viewer Role**  
1. As the **Workspace Admin**, go to **Workspaces** → Select your workspace → **Manage Access**.  
2. Add the *Test User* with the **Viewer** role.  

### **Task B: Validate Viewer Permissions**  
1. Open an **InPrivate browser** and sign in as the *Test User*.  
2. Navigate to **Workspaces** → Select the lab workspace.  
   - ✅ **Expected:** The user sees all items (warehouse, lakehouse).  
   - ✅ **Test:** Open the warehouse’s `Date` table → Data is readable.  
   - ✅ **Test:** Open the lakehouse’s `publicholidays` table → Data is readable.  

![Workspace Viewer view](./Images/workspace-viewer-view.png)  

---

## **Step 3: Item-Level Security**  
**Purpose:** Restrict access to specific items (e.g., warehouse only).  

### **Task A: Remove Workspace Permissions**  
1. As the **Admin**, go to **Manage Access** → Remove the *Test User*’s **Viewer** role.  

### **Task B: Grant Warehouse-Only Access**  
1. Hover over the warehouse → **…** → **Manage Permissions**.  
2. Add the *Test User* with only **ReadData** permission.  

![Warehouse permissions](./Images/grant-warehouse-access.png)  

### **Task C: Validate Item Permissions**  
1. As the *Test User* (refresh browser):  
   - ✅ **Expected:** The workspace is no longer visible.  
   - ✅ **Test:** Navigate via **OneLake** → Open the warehouse → `Date` table is readable.  
   - ❌ **Test:** Attempt to open the lakehouse → Access denied.  

---

## **Step 4: OneLake Data Access Roles**  
**Purpose:** Restrict lakehouse access to specific folders (Preview feature).  

### **Task A: Enable OneLake Roles**  
1. As the **Admin**, open the lakehouse → **Manage OneLake Data Access** → **Continue**.  

![Enable OneLake roles](./Images/manage-onelake-roles.png)  

### **Task B: Create a Custom Role**  
1. Select **New Role** → Name it `publicholidays`.  
2. Grant access **only** to the `publicholidays` folder.  

![Custom role setup](./Images/new-data-access-role.png)  

### **Task C: Assign Role to Test User**  
1. Under **Assign Role**, add the *Test User* → **Save**.  

### **Task D: Validate Folder Access**  
1. As the *Test User* (refresh browser):  
   - ✅ **Expected:** Only the `publicholidays` table is accessible.  
   - ❌ **Test:** Other tables/files are hidden.  

![Restricted lakehouse view](./Images/custom-role-view.png)  

---

## **Step 5: Clean Up**  
**Purpose:** Remove lab resources to avoid unnecessary costs.  

1. As the **Admin**, go to **Workspace Settings** → **Remove this Workspace**.  

---

## **Key Takeaways**  
1. **Workspace roles** control broad access (e.g., Viewer = read-only).  
2. **Item permissions** override workspace roles (e.g., warehouse-only access).  
3. **OneLake roles** enable column/folder-level security (Preview).  

**Next Steps:** Explore [row-level security (RLS)](https://learn.microsoft.com/fabric/security/row-level-security) for dynamic data filtering.  

--- 

**Feedback?** Rate this lab or suggest improvements [here](#).
