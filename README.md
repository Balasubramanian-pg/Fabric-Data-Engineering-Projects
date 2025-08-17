# Fabric-Data-Engineering-Projects
This repository contains learning materials and code samples for Microsoft Fabric Data Engineering. Below is the index of all chapters and files with direct links.

Here’s a sharper, more engaging rewrite—structured to highlight **purpose**, **action**, and **why it matters** at each step. I’ve trimmed redundancy, grouped related concepts, and framed it as a *journey* with clear milestones.

---

### **Chapter 1: Building a Modern Data Foundation in Microsoft Fabric**
**Goal:** Turn raw data into a trusted, high-performance analytical asset—step by step.

This chapter is your blueprint for constructing a **scalable, governed data platform** in Fabric. We’ll start with ingestion, enforce quality at every layer, and end with a warehouse ready for analytics. Each guide builds on the last, so tackle them in order.

---

#### **1. The Big Picture**
| **Document**       | **What It Covers**                                                                 | **Why It Matters**                                                                 |
|--------------------|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| **[Read Me.md]**   | Architecture overview, tech stack, and how the pieces fit together.               | Sets expectations: *What* you’re building and *why* it’s structured this way.      |

---
#### **2. Core Components: Where Data Lives**
| **Document**               | **Action Items**                                                                   | **Key Takeaway**                                                                   |
|----------------------------|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| **[01 Lakehouse.md]**      | Create a Lakehouse (data lake + warehouse hybrid).                                 | Your single source of truth—scalable storage *and* SQL query power.                |
| **[03 Delta Lake.md]**     | Learn ACID transactions, time travel, and schema enforcement.                      | No more "garbage in, garbage out." Delta Lake keeps data reliable and auditable.   |
| **[03b Medallion Lakehouse.md]** | Organize data into **Bronze (raw) → Silver (clean) → Gold (enriched)** layers.      | Prevents chaos. Each layer has a job: *landing → validating → serving*.           |

---
#### **3. Moving and Transforming Data**
| **Document**               | **What You’ll Do**                                                                 | **Outcome**                                                                        |
|----------------------------|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| **[04 Ingest Pipeline.md]** | Build a pipeline to pull data into **Bronze**.                                     | Automated, repeatable ingestion—no manual uploads.                                  |
| **[05 Dataflows Gen2.md]** | Use Power Query to clean/transform Bronze → **Silver**.                            | Low-code way to standardize data *before* analysis.                                |

---
#### **4. Serving Data for Analytics**
| **Document**               | **Focus**                                                                          | **Business Impact**                                                                |
|----------------------------|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| **[06a Data Warehouse Load.md]** | Load Gold-layer data into the **Warehouse** using T-SQL.                     | Optimized for speed: BI tools and reports run *fast*.                              |
| **[06b Data Warehouse Query.md]** | Write T-SQL queries to analyze data.                                         | Answer questions like *"What’s our monthly revenue by region?"* in seconds.        |
| **[06c Monitor Data Warehouse.md]** | Track query performance, resource use, and health.                          | Proactively fix slowdowns before users complain.                                   |

---
### **Key Patterns to Notice**
1. **Progressive Refinement**: Data gets *better* as it moves from Bronze → Gold. No shortcuts.
2. **Separation of Concerns**:
   - *Lakehouse* = Storage + flexible schemas.
   - *Warehouse* = Structured, high-speed queries.
3. **Automation First**: Pipelines and Dataflows replace manual Excel hell.

---
### **Where People Stumble**
- **Skipping Medallion layers?** You’ll drown in technical debt. Silver/Gold exist to *save time later*.
- **Ignoring Delta Lake features?** No time travel = no easy rollbacks when data breaks.
- **Not monitoring the Warehouse?** A slow report is a unused report.

---
### **Your Next Step**
Start with **[Read Me.md]** to grasp the "why," then dive into **[01 Lakehouse.md]** to build your foundation.

## Chapter 2 - Data Preparation & Transformation
- [2. Analyze Spark.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/2.%20Analyze%20Spark.md)
- [8.0 Data Science Get Started.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.0%20Data%20Science%20Get%20Started.md)
- [8.1 Data Science Explore Data.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.1%20Data%20Science%20Explore%20Data.md)
- [8.2 Data Science Preprocess Data Wrangler.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.2%20Data%20Science%20Preprocess%20Data%20Wrangler.md)
- [8.3 Data Science Train.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.3%20Data%20Science%20Train.md)
- [8.4 Data Science Batch.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.4%20Data%20Science%20Batch.md)
- [Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/Read%20Me.md)

## Chapter 3 - Advanced Analytics & AI
- [10. Ingest Notebooks.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/10.%20Ingest%20Notebooks.md)
- [11 Data Activator.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/11%20Data%20Activator.md)
- [12. Query Data in KQL Database.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/12.%20Query%20Data%20in%20KQL%20Database.md)
- [7. Real Time Intelligence.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/7.%20Real%20Time%20Intelligence.md)
- [8. Data Science Get Started.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.%20Data%20Science%20Get%20Started.md)
- [8.1 Data Science Explore Data.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.1%20Data%20Science%20Explore%20Data.md)
- [8.2 Data Science Preprocess Data Wrangler.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.2%20Data%20Science%20Preprocess%20Data%20Wrangler.md)
- [8.3 Data Science Train.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.3%20Data%20Science%20Train.md)
- [8.4 Data Science Batch.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.4%20Data%20Science%20Batch.md)
- [9 Real Time Analytics Eventstream.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/9%20Real%20Time%20Analytics%20Eventstream.md)
- [Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/Read%20Me.md)

## Chapter 4 - Reporting & Business Intelligence
- [13 Real Time Dashboards.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/13%20Real%20Time%20Dashboards.md)
- [14 Create A Star Schema Model.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/14%20Create%20A%20Star%20Schema%20Model.md)
- [14 Create Dax Calculations.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/14%20Create%20Dax%20Calculations.md)
- [15 Design Scalable Semantic Models.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/15%20Design%20Scalable%20Semantic%20Models.md)
- [15.1 Work With Model Relationships.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/15.1%20Work%20With%20Model%20Relationships.md)
- [16 Create Reusable Power BI Assets.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/16%20Create%20Reusable%20Power%20BI%20Assets.md)
- [16.1 Optimize Power BI Performance Using External Tools.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/16.1%20Optimize%20Power%20BI%20Performance%20Using%20External%20Tools.md)
- [17 Enforce Model Security.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/17%20Enforce%20Model%20Security.md)
- [18 Monitor Hub.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/18%20Monitor%20Hub.md)
- [19 Secure Data Access.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/19%20Secure%20Data%20Access.md)
- [20 Work With Database.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/20%20Work%20With%20Database.md)
- [20a Work With GraphQL.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/20a%20Work%20With%20GraphQL.md)
- [Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/Read%20Me.md)

## Chapter 5 - Deployment & Operations
- [21. Deployment Pipelines in Microsoft Fabric.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%205%20-%20Deployment%20%26%20Operations/21.%20Deployment%20Pipelines%20in%20Microsoft%20Fabric.md)
- [Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%205%20-%20Deployment%20%26%20Operations/Read%20Me.md)
