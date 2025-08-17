# Fabric-Data-Engineering-Projects
This repository contains learning materials and code samples for Microsoft Fabric Data Engineering. Below is the index of all chapters and files with direct links.

## **Chapter 1: Building a Modern Data Foundation in Microsoft Fabric**
**Goal:** Turn raw data into a trusted, high-performance analytical asset—step by step.
This chapter is your blueprint for constructing a **scalable, governed data platform** in Fabric. We’ll start with ingestion, enforce quality at every layer, and end with a warehouse ready for analytics. Each guide builds on the last, so tackle them in order.

#### **1. The Big Picture**
| **Document**       | **What It Covers**                                                                 | **Why It Matters**                                                                 |
|--------------------|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| **[Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/Read%20Me.md)**   | Architecture overview, tech stack, and how the pieces fit together.               | Sets expectations: *What* you’re building and *why* it’s structured this way.      |

#### **2. Core Components: Where Data Lives**
| **Document**               | **Action Items**                                                                   | **Key Takeaway**                                                                   |
|----------------------------|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| **[01 Lakehouse.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/01%20Lakehouse.md)**      | Create a Lakehouse (data lake + warehouse hybrid).                                 | Your single source of truth—scalable storage *and* SQL query power.                |
| **[03 Delta Lake.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/03%20Delta%20Lake.md)**     | Learn ACID transactions, time travel, and schema enforcement.                      | No more "garbage in, garbage out." Delta Lake keeps data reliable and auditable.   |
| **[03b Medallion Lakehouse.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/03b%20Medallion%20Lakehouse.md)** | Organize data into **Bronze (raw) → Silver (clean) → Gold (enriched)** layers.      | Prevents chaos. Each layer has a job: *landing → validating → serving*.           |

#### **3. Moving and Transforming Data**
| **Document**               | **What You’ll Do**                                                                 | **Outcome**                                                                        |
|----------------------------|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| **[04 Ingest Pipeline.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/04%20Ingest%20Pipeline.md)** | Build a pipeline to pull data into **Bronze**.                                     | Automated, repeatable ingestion—no manual uploads.                                  |
| **[05 Dataflows Gen2.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/05%20Dataflows%20Gen2.md)** | Use Power Query to clean/transform Bronze → **Silver**.                            | Low-code way to standardize data *before* analysis.                                |

#### **4. Serving Data for Analytics**
| **Document**               | **Focus**                                                                          | **Business Impact**                                                                |
|----------------------------|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| **[06a Data Warehouse Load.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/06a%20Data%20Warehouse%20Load.md)** | Load Gold-layer data into the **Warehouse** using T-SQL.                     | Optimized for speed: BI tools and reports run *fast*.                              |
| **[06b Data Warehouse Query.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/06b%20Data%20Warehouse%20Query.md)** | Write T-SQL queries to analyze data.                                         | Answer questions like *"What’s our monthly revenue by region?"* in seconds.        |
| **[06c Monitor Data Warehouse.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/06c%20Monitor%20Data%20Warehouse.md)** | Track query performance, resource use, and health.                          | Proactively fix slowdowns before users complain.                                   |

### **Key Patterns to Notice**
1. **Progressive Refinement**: Data gets *better* as it moves from Bronze → Gold. No shortcuts.
2. **Separation of Concerns**:
   - *Lakehouse* = Storage + flexible schemas.
   - *Warehouse* = Structured, high-speed queries.
3. **Automation First**: Pipelines and Dataflows replace manual Excel hell.

### **Where People Stumble**
- **Skipping Medallion layers?** You’ll drown in technical debt. Silver/Gold exist to *save time later*.
- **Ignoring Delta Lake features?** No time travel = no easy rollbacks when data breaks.
- **Not monitoring the Warehouse?** A slow report is a unused report.

### **Your Next Step**
Start with **[Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/Read%20Me.md)** to grasp the "why," then dive into **[01 Lakehouse.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/01%20Lakehouse.md)** to build your foundation.

## **Chapter 2: From Raw Data to Actionable Insights**
**Problem:** Data is messy. Models fail. Teams waste time cleaning instead of analyzing.
**Solution:** A **repeatable, scalable** process to explore, clean, and train data—*without reinventing the wheel every time*.

This chapter focuses on **preparation and transformation** using Spark, Data Science tools, and automation. No theory—just what works.

### **1. Start Here: The 5-Minute Overview**
| **Document** | **What It Covers** | **Why It Matters** |
|--------------|-------------------|-------------------|
| **[Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/Read%20Me.md)** | Goals, tools, and workflow for this chapter. | Skip this, and you’ll waste time guessing how pieces fit together. |

### **2. Explore and Understand Your Data**
**Mistake:** Jumping into modeling without knowing your data.
**Fix:** Profile, visualize, and validate *first*.

| **Document** | **Your Task** | **Outcome** |
|--------------|---------------|-------------|
| **[2. Analyze Spark.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/2.%20Analyze%20Spark.md)** | Use Spark to analyze large datasets efficiently. | Find patterns, outliers, and issues *before* they derail your analysis. |
| **[8.1 Data Science Explore Data.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.1%20Data%20Science%20Explore%20Data.md)** | Dive deep into data distributions, correlations, and quality. | Answer: *"Is this data even usable for modeling?"* |

### **3. Clean and Preprocess (The Boring but Critical Step)**
**Old Way:** Manual Excel cleaning, inconsistent scripts.
**New Way:** **Automated wrangling** with audit trails.

| **Document** | **Action** | **Result** |
|--------------|------------|------------|
| **[8.2 Data Science Preprocess Data Wrangler.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.2%20Data%20Science%20Preprocess%20Data%20Wrangler.md)** | Clean, transform, and standardize data with Wrangler. | No more "I forgot which column I fixed." Reproducible prep. |

### **4. Train and Deploy Models (Where the Magic Happens)**
**Goal:** Build models that *actually* work in production.

| **Document** | **Focus** | **Business Impact** |
|--------------|-----------|---------------------|
| **[8.3 Data Science Train.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.3%20Data%20Science%20Train.md)** | Train ML models with Fabric’s built-in tools. | From prototype to production—*without hand-offs*. |
| **[8.4 Data Science Batch.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.4%20Data%20Science%20Batch.md)** | Schedule batch scoring for new data. | Models stay fresh. No manual reruns. |

### **Where Teams Waste Time**
1. **Skipping exploration?** Garbage in → garbage predictions.
2. **Manual preprocessing?** One-off scripts break when data changes.
3. **No batch updates?** Models decay silently.

### **Your First Step**
1. Read **[Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/Read%20Me.md)** (5 min).
2. Profile your data with **[2. Analyze Spark.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/2.%20Analyze%20Spark.md)**.
3. **Ask yourself:** *What’s the riskiest assumption in our current data pipeline?*
   This chapter helps you validate it.

**No shortcuts.** Start with the [Read Me](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/Read%20Me.md).

## **Chapter 3: From Batch to Real-Time AI**
**Problem:** Your analytics are slow, reactive, and stuck in spreadsheets.
**Solution:** **Real-time insights, automated actions, and scalable AI**—without the PhD.

This chapter covers **advanced analytics, event-driven systems, and AI that actually ships**.

### **1. Start Here: The 5-Minute Primer**
| **Document** | **What It Covers** | **Why It Matters** |
|--------------|-------------------|-------------------|
| **[Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/Read%20Me.md)** | The *what*, *why*, and *how* of advanced analytics in Fabric. | Skip this, and you’ll build cool demos that never go live. |

### **2. Ingest and Process Data at Scale**
**Mistake:** Waiting for batch jobs to finish.
**Fix:** **Real-time pipelines + notebooks** for speed and flexibility.

| **Document** | **Your Task** | **Outcome** |
|--------------|---------------|-------------|
| **[10. Ingest Notebooks.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/10.%20Ingest%20Notebooks.md)** | Use notebooks for flexible data ingestion. | No more rigid ETL—adapt on the fly. |
| **[9 Real Time Analytics Eventstream.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/9%20Real%20Time%20Analytics%20Eventstream.md)** | Set up event streams for live data. | React to events *as they happen*—not tomorrow. |

### **3. Real-Time Intelligence (The Game-Changer)**
**Old Way:** Dashboards that show *yesterday’s* data.
**New Way:** **Live monitoring + automated triggers**.

| **Document** | **Action** | **Result** |
|--------------|------------|------------|
| **[7. Real Time Intelligence.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/7.%20Real%20Time%20Intelligence.md)** | Build real-time dashboards and alerts. | Spot trends *before* they become crises. |
| **[11 Data Activator.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/11%20Data%20Activator.md)** | Automate actions based on data changes. | No more manual "check the dashboard" routines. |

### **4. Query and Analyze High-Velocity Data**
**Goal:** Ask complex questions *fast*—even on streaming data.

| **Document** | **Focus** | **Business Impact** |
|--------------|-----------|---------------------|
| **[12. Query Data in KQL Database.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/12.%20Query%20Data%20in%20KQL%20Database.md)** | Use KQL for log analytics and time-series data. | Find anomalies in seconds, not hours. |

### **5. Data Science That Ships (Not Just Experiments)**
**Problem:** Models die in Jupyter notebooks.
**Solution:** **End-to-end ML in Fabric**.

| **Document** | **Task** | **Outcome** |
|--------------|----------|-------------|
| **[8. Data Science Get Started.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.%20Data%20Science%20Get%20Started.md)** | Set up your data science environment. | No more "it works on my machine." |
| **[8.1 Data Science Explore Data.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.1%20Data%20Science%20Explore%20Data.md)** | Explore data with purpose. | Avoid training models on garbage. |
| **[8.2 Data Science Preprocess Data Wrangler.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.2%20Data%20Science%20Preprocess%20Data%20Wrangler.md)** | Clean and prep data *at scale*. | Reproducible pipelines, not one-off scripts. |
| **[8.3 Data Science Train.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.3%20Data%20Science%20Train.md)** | Train and validate models. | Models that *actually* deploy. |
| **[8.4 Data Science Batch.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.4%20Data%20Science%20Batch.md)** | Schedule batch scoring. | Keep models fresh *without manual work*. |

### **Where Teams Fail**
1. **Stuck in batch?** Your competitors are reacting in real time.
2. **Ignoring event streams?** You’re flying blind between reports.
3. **Data science in silos?** Models that never leave the lab.

### **Your First Step**
1. Read **[Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/Read%20Me.md)** (5 min).
2. Pick **one** real-time use case (e.g., fraud detection, live monitoring).
3. Start with **[9 Real Time Analytics Eventstream.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/9%20Real%20Time%20Analytics%20Eventstream.md)**.

**No more "we’ll get to real-time later."** Start now.

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
