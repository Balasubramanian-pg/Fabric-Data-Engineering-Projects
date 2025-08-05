# Lab: Work with API for GraphQL in Microsoft Fabric

## Introduction  
This hands-on lab will guide you through using Microsoft Fabric's API for GraphQL to efficiently query data from multiple sources. GraphQL provides a flexible query language that enables clients to request exactly the data they need in a single API call.

**Estimated Duration**: 30 minutes  
**Prerequisites**: Microsoft Fabric trial account

## Exercise 1: Set Up Your Environment

### Task 1: Create a Workspace
1. Sign in to [Microsoft Fabric](https://app.fabric.microsoft.com)
2. Select **New workspace** from the left navigation
3. Name your workspace (e.g., "GraphQL-Lab")
4. Choose a licensing mode with Fabric capacity (Trial, Premium, or Fabric)
5. Verify your new empty workspace appears

### Task 2: Create and Populate a SQL Database
1. In your workspace, select **Create** → **SQL database**
2. Name the database "AdventureWorksLT" and create it
3. Load sample data using the **Sample data** option
4. Confirm the database appears with populated tables

## Exercise 2: Explore SQL Data

### Task 1: Execute SQL Queries
1. Open your AdventureWorksLT database
2. Select **New query**
3. Run the following query:
```sql
SELECT 
    p.Name AS ProductName,
    pc.Name AS CategoryName,
    p.ListPrice
FROM 
    SalesLT.Product p
INNER JOIN 
    SalesLT.ProductCategory pc ON p.ProductCategoryID = pc.ProductCategoryID
ORDER BY 
    p.ListPrice DESC;
```
4. Review the results showing products with their categories and prices
5. Close all query tabs

## Exercise 3: Configure GraphQL API

### Task 1: Create GraphQL Endpoint
1. In your workspace, select **New item** → **API for GraphQL**
2. Name your API (e.g., "ProductsAPI") and create it
3. Select **Select data source**
4. Choose SSO authentication option
5. Connect to your AdventureWorksLT database
6. Select the `SalesLT.Product` table and load it
7. Note the API endpoint URL (optional)

### Task 2: Secure the API
1. In Schema Explorer, expand **Mutations**
2. For each mutation operation:
   - Select the **...** menu
   - Choose **Disable**
3. Confirm all mutations are now disabled

## Exercise 4: Query Data with GraphQL

### Task 1: Execute GraphQL Query
1. In the GraphQL query editor, enter:
```graphql
query {
  products(filter: { Name: { startsWith: "HL Road Frame" } }) {
    items {
      ProductModelID
      Name
      ListPrice
      Color
      Size
      ModifiedDate
    }
  }
}
```
2. Execute the query
3. Review the returned product data matching your filter

## Cleanup
1. Navigate to workspace settings
2. Select **Remove this workspace** to delete all resources

## Conclusion
You've successfully:
- Created a Fabric workspace with sample data
- Established a GraphQL API endpoint
- Configured secure read-only access
- Executed targeted GraphQL queries

For advanced features, explore the [Microsoft Fabric GraphQL documentation](https://learn.microsoft.com/fabric/data-engineering/api-graphql-overview).
