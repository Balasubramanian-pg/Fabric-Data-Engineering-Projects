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

# Exercise 2: Deep Dive into SQL Data Exploration

## Objective
This expanded exercise will provide a more comprehensive exploration of the SQL database in Microsoft Fabric, including advanced query techniques and data analysis that will later inform our GraphQL API design.

## Task 2.1: Initial Data Exploration

### 2.1.1 Explore Database Schema
1. In your AdventureWorksLT database, navigate to the **Tables** section
2. Examine the available tables:
   - SalesLT.Product (ProductID, Name, ProductNumber, Color, etc.)
   - SalesLT.ProductCategory (ProductCategoryID, ParentCategoryID, Name)
   - SalesLT.ProductModel (ProductModelID, Name)
   - SalesLT.ProductModelProductDescription (ProductModelID, ProductDescriptionID)
   - SalesLT.ProductDescription (ProductDescriptionID, Description)

### 2.1.2 Basic Data Sampling
Run these queries to understand the data distribution:

```sql
-- Count products per category
SELECT 
    pc.Name AS Category,
    COUNT(p.ProductID) AS ProductCount
FROM 
    SalesLT.Product p
JOIN 
    SalesLT.ProductCategory pc ON p.ProductCategoryID = pc.ProductCategoryID
GROUP BY 
    pc.Name
ORDER BY 
    ProductCount DESC;

-- Price distribution analysis
SELECT 
    MIN(ListPrice) AS MinPrice,
    MAX(ListPrice) AS MaxPrice,
    AVG(ListPrice) AS AvgPrice,
    STDEV(ListPrice) AS PriceStdDev
FROM 
    SalesLT.Product
WHERE 
    ListPrice > 0;
```

## Task 2.2: Advanced Query Techniques

### 2.2.1 Hierarchical Category Query
```sql
-- Recursive CTE for category hierarchy
WITH CategoryHierarchy AS (
    -- Anchor member (top-level categories)
    SELECT 
        ProductCategoryID,
        ParentProductCategoryID,
        Name,
        0 AS Level
    FROM 
        SalesLT.ProductCategory
    WHERE 
        ParentProductCategoryID IS NULL
    
    UNION ALL
    
    -- Recursive member (subcategories)
    SELECT 
        pc.ProductCategoryID,
        pc.ParentProductCategoryID,
        pc.Name,
        ch.Level + 1
    FROM 
        SalesLT.ProductCategory pc
    JOIN 
        CategoryHierarchy ch ON pc.ParentProductCategoryID = ch.ProductCategoryID
)
SELECT 
    REPLICATE('    ', Level) + Name AS CategoryTree,
    Level
FROM 
    CategoryHierarchy
ORDER BY 
    Level, Name;
```

### 2.2.2 Product Attribute Analysis
```sql
-- Pivot table showing available sizes by category
SELECT 
    CategoryName,
    [S], [M], [L], [XL], [38], [40], [42], [44], [48], [52]
FROM (
    SELECT 
        pc.Name AS CategoryName,
        p.Size,
        p.ProductID
    FROM 
        SalesLT.Product p
    JOIN 
        SalesLT.ProductCategory pc ON p.ProductCategoryID = pc.ProductCategoryID
    WHERE 
        p.Size IS NOT NULL
) AS SourceTable
PIVOT (
    COUNT(ProductID)
    FOR Size IN ([S], [M], [L], [XL], [38], [40], [42], [44], [48], [52])
) AS PivotTable;
```

## Task 2.3: Data Quality Assessment

### 2.3.1 Missing Value Analysis
```sql
-- Check for missing critical data
SELECT
    SUM(CASE WHEN Name IS NULL THEN 1 ELSE 0 END) AS MissingNames,
    SUM(CASE WHEN ProductNumber IS NULL THEN 1 ELSE 0 END) AS MissingProductNumbers,
    SUM(CASE WHEN StandardCost IS NULL THEN 1 ELSE 0 END) AS MissingCosts,
    SUM(CASE WHEN ListPrice IS NULL THEN 1 ELSE 0 END) AS MissingPrices,
    COUNT(*) AS TotalProducts
FROM 
    SalesLT.Product;
```

### 2.3.2 Price Consistency Check
```sql
-- Find products where cost exceeds price (data anomaly)
SELECT 
    ProductID,
    Name,
    StandardCost,
    ListPrice,
    ListPrice - StandardCost AS Margin,
    (ListPrice - StandardCost)/NULLIF(StandardCost, 0) AS MarginPercentage
FROM 
    SalesLT.Product
WHERE 
    StandardCost > ListPrice
    OR StandardCost <= 0
ORDER BY 
    MarginPercentage;
```

## Task 2.4: Preparing for GraphQL Exposure

### 2.4.1 Identify Key Relationships
```sql
-- Many-to-many relationship between products and models
SELECT 
    pm.Name AS ModelName,
    STRING_AGG(p.Name, ', ') AS ProductsInModel
FROM 
    SalesLT.ProductModel pm
JOIN 
    SalesLT.ProductModelProductDescription pmpd ON pm.ProductModelID = pmpd.ProductModelID
JOIN 
    SalesLT.Product p ON p.ProductModelID = pm.ProductModelID
GROUP BY 
    pm.Name;
```

### 2.4.2 Determine Optimal GraphQL Types
Based on our analysis, we can identify these natural GraphQL types:
- Product (with fields: id, name, number, color, size, cost, price)
- ProductCategory (with hierarchy support)
- ProductModel (with product collections)

## Key Takeaways
1. The database contains a complete product catalog with hierarchical categories
2. Price data is generally clean but has a few anomalies to address
3. Product models group multiple product variants
4. Size information is available for apparel products
5. The data structure naturally maps to GraphQL types and relationships
---

# **Exercise 3: Configuring a Robust GraphQL API in Microsoft Fabric**  

## **Objective**  
This expanded exercise will guide you through configuring a **production-ready GraphQL API** in Microsoft Fabric, covering:  
- **Data source configuration**  
- **Schema customization**  
- **Security hardening**  
- **Performance optimization**  
- **Query testing & validation**  

---

## **Task 3.1: Configure Data Sources for GraphQL**  

### **3.1.1 Connect Multiple Data Sources**  
Microsoft Fabric allows connecting **multiple tables** (even across different databases) to a single GraphQL API.  

1. **Add additional tables** from `AdventureWorksLT`:  
   - `SalesLT.ProductCategory`  
   - `SalesLT.ProductModel`  
   - `SalesLT.ProductDescription`  

2. **Steps to add tables**:  
   - In your **GraphQL API**, select **"Select data source"**  
   - Choose **"Add more data"**  
   - Select the additional tables and **"Load"**  

3. **Verify relationships**:  
   - The system **auto-detects foreign keys** (e.g., `Product.ProductCategoryID → ProductCategory.ProductCategoryID`)  
   - If relationships aren't detected automatically, manually define them in the **Schema Editor**  

---

## **Task 3.2: Customize the GraphQL Schema**  

### **3.2.1 Rename Fields for Better Usability**  
By default, field names match database columns. Modify them for a cleaner API:  

1. Open the **Schema Explorer**  
2. For the `Product` type:  
   - Rename `ProductID` → `id`  
   - Rename `ProductNumber` → `sku`  
   - Rename `ListPrice` → `price`  
3. For the `ProductCategory` type:  
   - Rename `Name` → `categoryName`  

### **3.2.2 Add Computed Fields (Virtual Fields)**  
GraphQL allows adding **derived fields** that don’t exist in the database.  

Example: Add a `isOnSale` field to `Product` that checks if `ListPrice < StandardCost * 1.2`  

1. In the **Schema Editor**, modify the `Product` type:  
   ```graphql
   type Product {
     id: Int!
     name: String!
     sku: String!
     price: Float!
     isOnSale: Boolean! @resolver(query: "SELECT CASE WHEN ListPrice < StandardCost * 1.2 THEN 1 ELSE 0 END FROM SalesLT.Product WHERE ProductID = $id")
   }
   ```
   *(Note: The exact resolver syntax may vary based on Fabric’s implementation.)*  

---

## **Task 3.3: Secure the GraphQL API**  

### **3.3.1 Disable All Mutations (Read-Only API)**  
Since we only need **read operations**, disable all mutations:  

1. In the **Schema Explorer**, expand **Mutations**  
2. For each mutation (`createProduct`, `updateProduct`, `deleteProduct`), select **"Disable"**  

### **3.3.2 Enable Query Rate Limiting**  
Prevent API abuse by setting request limits:  

1. Go to **API Settings**  
2. Set:  
   - **Max requests per minute**: 100  
   - **Max query depth**: 10 (to prevent overly complex queries)  

### **3.3.3 (Optional) Enable Authentication**  
If exposing externally, require API keys or OAuth:  

1. In **API Settings**, enable **"Require API Key"**  
2. Generate & distribute keys to clients  

---

## **Task 3.4: Optimize Performance**  

### **3.4.1 Enable Caching for Frequent Queries**  
Cache responses to reduce database load:  

1. In **API Settings**, enable **"Response Caching"**  
2. Set **TTL (Time-to-Live)**: 60 seconds  

### **3.4.2 Optimize Resolver Queries**  
For complex fields (like `isOnSale`), ensure the resolver SQL is efficient:  

```sql
-- Instead of a subquery per record, batch-process in a single SQL call
SELECT 
  ProductID,
  CASE WHEN ListPrice < StandardCost * 1.2 THEN 1 ELSE 0 END AS IsOnSale
FROM SalesLT.Product
```

---

## **Task 3.5: Test & Validate the API**  

### **3.5.1 Run Sample Queries**  
Test different query patterns in the **GraphQL Playground**:  

#### **Basic Product Query**  
```graphql
query {
  products(filter: { price: { gt: 1000 } }) {
    items {
      id
      name
      price
      isOnSale
      category {
        categoryName
      }
    }
  }
}
```

#### **Nested Query (Products + Categories + Models)**  
```graphql
query {
  productCategories {
    items {
      categoryName
      products {
        name
        price
        model {
          name
        }
      }
    }
  }
}
```

### **3.5.2 Check Performance Metrics**  
1. In **API Analytics**, monitor:  
   - **Query execution time**  
   - **Most frequent queries**  
   - **Error rates**  

2. Optimize slow queries by:  
   - Adding database **indexes**  
   - Simplifying **nested queries**  

---

## **Key Takeaways**  
✅ **Multi-source GraphQL APIs** can combine data from different tables seamlessly.  
✅ **Schema customization** improves usability (renaming fields, adding computed fields).  
✅ **Security hardening** (disabling mutations, rate limiting) prevents abuse.  
✅ **Caching & query optimization** ensure high performance.  
✅ **Testing with real queries** validates functionality before production use.  

This **production-grade GraphQL API** is now ready for client applications!   

---
  
**Next Steps**:  
➡️ **Exercise 4** will explore **querying this API from a client app** (React, Python, etc.).  
➡️ Learn about **GraphQL subscriptions** for real-time updates.  
➡️ Explore **Fabric’s monitoring tools** for API analytics.

# **Exercise 4: Advanced GraphQL Querying & Real-World Applications**  

## **Objective**  
This expanded exercise dives deep into **querying your GraphQL API** with:  
✔ **Complex query structures** (filtering, sorting, pagination)  
✔ **Performance optimization techniques**  
✔ **Error handling & debugging**  
✔ **Real-world client integration examples** (React, Python)  

---

## **Task 4.1: Mastering GraphQL Query Techniques**  

### **4.1.1 Filtering & Sorting**  
Run queries with **dynamic filtering** and **multi-field sorting**:  

#### **Filter Products by Price & Category**  
```graphql
query {
  products(
    filter: { 
      price: { gt: 100, lt: 1000 }
      category: { categoryName: { eq: "Bikes" } }
    }
    sort: { price: DESC, name: ASC }
  ) {
    items {
      id
      name
      price
      category {
        categoryName
      }
    }
  }
}
```
✅ **Key Filters Supported**:  
- `eq`, `neq`, `gt`, `lt`, `contains`, `startsWith`  
- **Nested filters** (e.g., `category: { name: { eq: "Bikes" } }`)  

---

### **4.1.2 Pagination (Cursor-Based & Offset-Based)**  
#### **Cursor-Based (Recommended for large datasets)**  
```graphql
query {
  products(first: 5, after: "cursor-from-previous-page") {
    edges {
      node {
        id
        name
      }
      cursor
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```
#### **Offset-Based (Simple but less performant)**  
```graphql
query {
  products(limit: 5, offset: 10) {
    items {
      id
      name
    }
    totalCount
  }
}
```

---

### **4.1.3 Nested Queries (Joins in GraphQL)**  
Fetch **products + categories + models** in one request:  
```graphql
query {
  products(filter: { color: { eq: "Red" } }) {
    items {
      name
      price
      category {
        categoryName
      }
      model {
        name
        description {
          text
        }
      }
    }
  }
}
```
⚠ **Performance Tip**: Avoid **over-fetching**—only request needed fields!  

---

## **Task 4.2: Optimizing Query Performance**  

### **4.2.1 Query Cost Analysis**  
Microsoft Fabric’s **GraphQL Analytics** shows:  
- **Query execution time**  
- **Database load**  
- **Most expensive fields**  

**Optimization Strategies**:  
1. **Batch Resolvers**: Combine multiple SQL queries into one.  
2. **Add Indexes** on filtered/sorted fields (`price`, `categoryName`).  
3. **Use `@defer`** for slow non-critical fields.  

---

### **4.2.2 Caching Strategies**  
| **Cache Type**       | **Use Case**                          | **Implementation**                     |
|----------------------|---------------------------------------|----------------------------------------|
| **Response Cache**   | Static data (e.g., categories)        | Enable in API Settings (TTL: 1 hour)   |
| **Persisted Queries**| Repeated complex queries              | Store hashed queries server-side       |
| **Client-Side Cache**| Frontend apps (React/Apollo)          | Use `@client` directives               |

---

## **Task 4.3: Error Handling & Debugging**  

### **4.3.1 Structured Error Responses**  
GraphQL returns **partial data + errors** (unlike REST):  
```json
{
  "errors": [
    {
      "message": "Price filter must be a number",
      "path": ["products", "filter", "price"],
      "extensions": { "code": "VALIDATION_ERROR" }
    }
  ],
  "data": {
    "products": null
  }
}
```

### **4.3.2 Logging & Monitoring**  
1. **Enable Query Logging** in **Fabric Monitoring**.  
2. **Set Alerts** for:  
   - `queryDepth > 10`  
   - `responseSize > 1MB`  

---

## **Task 4.4: Real-World Client Integrations**  

### **4.4.1 React (Apollo Client)**  
```javascript
import { ApolloClient, InMemoryCache, gql } from '@apollo/client';

const client = new ApolloClient({
  uri: 'YOUR_GRAPHQL_ENDPOINT',
  cache: new InMemoryCache()
});

const GET_PRODUCTS = gql`
  query GetProducts($minPrice: Float!) {
    products(filter: { price: { gt: $minPrice } }) {
      items {
        id
        name
        price
      }
    }
  }
`;

function ProductList() {
  const { loading, error, data } = useQuery(GET_PRODUCTS, {
    variables: { minPrice: 100 }
  });

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return data.products.items.map(({ id, name, price }) => (
    <div key={id}>{name} - ${price}</div>
  ));
}
```

### **4.4.2 Python (Requests Library)**  
```python
import requests

query = """
query GetProducts($color: String!) {
  products(filter: { color: { eq: $color } }) {
    items {
      id
      name
    }
  }
}
"""

variables = { "color": "Blue" }

response = requests.post(
  "YOUR_GRAPHQL_ENDPOINT",
  json={"query": query, "variables": variables}
)

print(response.json()["data"]["products"]["items"])
```

---

## **Key Takeaways**  
✅ **Complex queries** (filtering, sorting, pagination) are easy in GraphQL.  
✅ **Optimize performance** with caching, batching, and indexing.  
✅ **Handle errors gracefully** with structured responses.  
✅ **Integrate seamlessly** with frontend/backend clients.  

---
  
**Next Steps**:  
➡️ **Explore GraphQL Subscriptions** for real-time updates.  
➡️ **Set up CI/CD** for your GraphQL API in Fabric.  
➡️ **Monitor production traffic** with Fabric Analytics.  

Your API is now **production-ready**!

## Conclusion
You've successfully:
- Created a Fabric workspace with sample data
- Established a GraphQL API endpoint
- Configured secure read-only access
- Executed targeted GraphQL queries

For advanced features, explore the [Microsoft Fabric GraphQL documentation](https://learn.microsoft.com/fabric/data-engineering/api-graphql-overview).
