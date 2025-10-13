Let's walk through how to endorse items in Microsoft Fabric end-to-end. Item endorsement is a key feature for promoting trust and discoverability of high-quality, reliable content within your Fabric workspaces.

**Understanding Item Endorsement in Microsoft Fabric**

- **Purpose of Endorsement:** Endorsement is a way to highlight and promote Fabric items that are considered high-quality, reliable, and ready for broad use within your organization. It helps users discover and trust the right content.
- **Types of Endorsement:** Microsoft Fabric offers two levels of endorsement:
    - **Promoted:** Indicates that an item is of good quality and is ready for general use. Promoted items are recommended for wider consumption.
    - **Certified:** Indicates that an item meets the highest standards of quality, reliability, and is officially endorsed by your organization. Certified items are considered authoritative and are highly trusted. Certification typically requires a more rigorous review process.
- **Benefits of Endorsement:**
    - **Increased Discoverability:** Endorsed items are often surfaced more prominently in Fabric experiences (e.g., in search results, recommendations, item lists).
    - **Enhanced Trust:** Endorsement signals to users that an item has been reviewed and approved, increasing their confidence in using it.
    - **Improved Data Governance:** Endorsement helps establish a process for quality control and ensures that users are leveraging reliable and authoritative data and analytics assets.
    - **Clear Distinction:** Provides a clear visual distinction between endorsed and non-endorsed content, helping users differentiate between official and potentially less reliable items.
- **Visual Cues:** Endorsed items are visually marked in the Fabric UI with badges or icons to indicate their endorsement status (Promoted or Certified).

**End-to-End Guide to Endorsing Items in Microsoft Fabric**

**Phase 1: Prerequisites**

Before you can endorse items in Fabric, ensure you have the following:

1. **Microsoft Fabric Workspace:** You need a Fabric workspace containing the items you want to endorse.
2. **Fabric Capacity:** The workspace must be assigned to a Microsoft Fabric Capacity. Endorsement is a feature associated with Fabric Capacities.
3. **Workspace Admin or Member Permissions:** You need to be a Workspace Admin or Member to endorse items within a workspace. Contributor and Viewer roles typically cannot endorse items.
4. **Enable Endorsement (Tenant Setting - Often Pre-configured):** In some organizations, tenant administrators might need to explicitly enable the endorsement feature at the tenant level in the Power BI admin portal (as Fabric endorsement often leverages the Power BI governance framework). However, in many Fabric tenants, endorsement is enabled by default. If you don't see the endorsement options, check with your Fabric/Power BI administrator to ensure it's enabled in your tenant settings.
5. **Established Endorsement Process (Recommended):** Ideally, your organization should have a defined process for item endorsement. This process might include criteria for promotion and certification, review workflows, and responsible roles for endorsement.

**Phase 2: Endorsing an Item**

The steps to endorse an item are generally similar across different Fabric item types. Let's use a Power BI Report (Fabric Report) as an example, but the process is comparable for other endorsable items like Semantic Models, Dataflows Gen2, etc.

1. **Navigate to the Fabric Item:** Open your Fabric workspace and locate the item you want to endorse (e.g., a Power BI Report).
2. **Open Item Settings:**
    - For a Power BI Report, typically you can open the report and then go to **File -> Settings** in the report menu.
    - For other item types, the "Settings" option might be accessed via a context menu (right-click on the item in the workspace list) or through a "Settings" button within the item's interface.
3. **Find the "Endorsement" Section:** In the item's "Settings" pane, look for a section labeled "Endorsement" or "Certification."
4. **Choose Endorsement Level:** In the "Endorsement" section, you'll usually see a dropdown or options to select the endorsement level.
    - **"None" (Default):** Items are typically "None" endorsed by default.
    - **"Promoted":** Select "Promoted" to endorse the item as being of good quality and ready for general use.
    - **"Certified":** Select "Certified" to endorse the item as meeting the highest standards and being authoritative.
5. **Apply/Save Endorsement:** After selecting the endorsement level, click "Apply" or "Save" in the settings pane to save your changes.
6. **Verification (Optional but Recommended):** After endorsing the item, it's good practice to:
    - **Check Visual Badge/Icon:** Verify that the endorsed item now displays the appropriate badge or icon in the Fabric UI to indicate its endorsement status (e.g., a "Promoted" badge or a "Certified" badge).
    - **Inform Users (If Applicable):** If you've endorsed an item for broader use, consider informing relevant users or teams about the endorsed content and its purpose.

**Phase 3: Managing Endorsement and Viewing Endorsed Items**

- **Viewing Endorsement Status in Item Lists:** In your Fabric workspace item lists (e.g., list of reports, list of semantic models), you'll often see visual indicators (badges or icons) next to endorsed items, making it easy to identify their endorsement status.
- **Filtering or Sorting by Endorsement (Potentially Available in Future Updates):** In the future, Fabric might introduce features to filter or sort item lists by endorsement status, making it even easier to find endorsed content. Keep an eye on Fabric updates for such enhancements.
- **Changing Endorsement Level:** To change the endorsement level of an item (e.g., from "Promoted" to "Certified," or to remove endorsement), simply repeat the steps in Phase 2 and select a different endorsement level or "None."
- **Removing Endorsement:** To remove endorsement completely, set the endorsement level back to "None" in the item's settings.
- **Monitoring Usage of Endorsed Items (Indirect):** Fabric usage metrics and monitoring tools can help you understand how often endorsed items are being used. This can provide insights into the effectiveness of your endorsement program.

**Phase 4: Best Practices for Item Endorsement in Fabric**

- **Establish Clear Endorsement Criteria:** Define clear and documented criteria for both "Promoted" and "Certified" endorsement levels. What makes an item "good quality" for promotion? What are the rigorous requirements for certification?
- **Define Endorsement Process:** Create a formal process for item endorsement. This might involve:
    - **Request Process:** How users can request endorsement for their items.
    - **Review Workflow:** Who is responsible for reviewing items for endorsement? What review steps are involved (e.g., data quality checks, performance testing, documentation review)?
    - **Approval Process:** Who has the authority to approve or deny endorsement requests?
    - **Maintenance and Re-certification:** How often should endorsed items be reviewed and re-certified to ensure they remain current and high-quality?
- **Communicate Endorsement Guidelines:** Clearly communicate the endorsement criteria and process to all Fabric users in your organization.
- **Promote Endorsed Items:** Actively promote endorsed items to users who need them. Highlight endorsed content in training materials, documentation, and communication channels.
- **Start with High-Value Items:** Begin your endorsement program by focusing on endorsing your most critical and widely used Fabric items first.
- **Iterative Approach:** Start with a basic endorsement process and refine it over time based on feedback and experience.
- **Consider Automation (Future):** Explore opportunities to automate parts of the endorsement process, such as automated quality checks or workflow notifications, as Fabric capabilities evolve.
- **Integrate with Data Governance Strategy:** Align your item endorsement program with your broader data governance strategy and data quality initiatives.

**Phase 5: Limitations and Considerations**

- **Metadata, Not Enforcement (Primarily for Visibility):** Remember that endorsement in Fabric is primarily a _metadata_ classification. It's a signal of quality and trust, but it doesn't automatically enforce permissions or security policies based on endorsement status _within Fabric itself_. Enforcement actions based on quality signals are typically handled through organizational processes and user awareness.
- **Manual Endorsement Process:** The endorsement process in Fabric is currently primarily manual. You need to manually review and endorse items through the Fabric UI. Automated endorsement workflows are not built-in (though you could potentially create custom workflows outside of Fabric to manage endorsement requests and trigger manual endorsement actions within Fabric).
- **Tenant Setting Dependency (Potentially):** In some tenants, endorsement might need to be explicitly enabled at the tenant level. Check with your Fabric/Power BI administrator if you don't see endorsement options.
- **Item Type Support (Evolving):** The range of Fabric item types that fully support endorsement might expand over time. Check the latest Microsoft Fabric documentation for the most current list of supported item types and features.
- **Organizational Process is Key:** The success of an item endorsement program heavily relies on having a well-defined organizational process for endorsement, clear criteria, and user adoption.

**Phase 6: Troubleshooting Common Issues**

- **"Endorsement" Option Not Visible:**
    - **Check Workspace Role:** Ensure you are a Workspace Admin or Member. Contributor and Viewer roles cannot endorse items.
    - **Fabric Capacity Assignment:** Verify that the workspace is assigned to a Fabric Capacity. Endorsement is a capacity-based feature.
    - **Tenant Setting (If Applicable):** Check with your Fabric/Power BI administrator if endorsement is enabled at the tenant level in your organization.
    - **Item Type Support:** Confirm that the item type you are trying to endorse actually supports endorsement.
    - **Refresh/Wait:** Sometimes, Fabric UI updates might take a moment to reflect. Try refreshing your browser or waiting briefly.
- **Endorsement Badge Not Appearing:**
    - **Save Changes:** Ensure you have clicked "Apply" or "Save" after selecting the endorsement level in the item's settings.
    - **Browser Cache:** Clear your browser cache and refresh the Fabric workspace to ensure you are seeing the latest UI updates.
    - **Verify Endorsement in Settings:** Re-check the item's settings to confirm that the endorsement level is indeed set to "Promoted" or "Certified."
- **Users Not Finding Endorsed Items:**
    - **Communicate and Promote:** Actively communicate the existence of endorsed items to users who should be using them. Promote endorsed content in training materials, documentation, and relevant communication channels.
    - **Search and Discovery Features (Future):** As Fabric evolves, look for potential future enhancements to Fabric search and discovery features that might more prominently surface endorsed items.

By following this guide and implementing best practices, you can effectively use item endorsement in Microsoft Fabric to promote high-quality content, build user trust, and enhance data governance within your organization. Remember that a successful endorsement program requires not only technical configuration but also a well-defined organizational process and user adoption.
