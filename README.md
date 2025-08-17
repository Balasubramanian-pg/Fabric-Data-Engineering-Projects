# Fabric-Data-Engineering-Projects
This repository contains learning materials and code samples for Microsoft Fabric Data Engineering. Below is the index of all chapters and files with direct links.

<div align="center">
  <img src="https://media.giphy.com/media/L1JjM5mcI9rnwZJde5/giphy.gif" width="150" alt="Data Foundation GIF">
  <h2><strong>Chapter 1: Building a Modern Data Foundation in Microsoft Fabric</strong></h2>
</div>

**Goal:** Turn raw data into a trusted, high-performance analytical asset—step by step.
This chapter is your blueprint for constructing a **scalable, governed data platform** in Fabric. We’ll start with ingestion, enforce quality at every layer, and end with a warehouse ready for analytics. Each guide builds on the last, so tackle them in order.

#### **1. The Big Picture**
<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>What It Covers</strong></th>
    <th align="left"><strong>Why It Matters</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/Read%20Me.md">Read Me.md</a></strong></td>
    <td>Architecture overview, tech stack, and how the pieces fit together.</td>
    <td>Sets expectations: <em>What</em> you’re building and <em>why</em> it’s structured this way.</td>
  </tr>
</table>

#### **2. Core Components: Where Data Lives**
<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Action Items</strong></th>
    <th align="left"><strong>Key Takeaway</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/01%20Lakehouse.md">01 Lakehouse.md</a></strong></td>
    <td>Create a Lakehouse (data lake + warehouse hybrid).</td>
    <td>Your single source of truth—scalable storage <em>and</em> SQL query power.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/03%20Delta%20Lake.md">03 Delta Lake.md</a></strong></td>
    <td>Learn ACID transactions, time travel, and schema enforcement.</td>
    <td>No more "garbage in, garbage out." Delta Lake keeps data reliable and auditable.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/03b%20Medallion%20Lakehouse.md">03b Medallion Lakehouse.md</a></strong></td>
    <td>Organize data into <strong>Bronze (raw) → Silver (clean) → Gold (enriched)</strong> layers.</td>
    <td>Prevents chaos. Each layer has a job: <em>landing → validating → serving</em>.</td>
  </tr>
</table>

#### **3. Moving and Transforming Data**
<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>What You’ll Do</strong></th>
    <th align="left"><strong>Outcome</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/04%20Ingest%20Pipeline.md">04 Ingest Pipeline.md</a></strong></td>
    <td>Build a pipeline to pull data into <strong>Bronze</strong>.</td>
    <td>Automated, repeatable ingestion—no manual uploads.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/05%20Dataflows%20Gen2.md">05 Dataflows Gen2.md</a></strong></td>
    <td>Use Power Query to clean/transform Bronze → <strong>Silver</strong>.</td>
    <td>Low-code way to standardize data <em>before</em> analysis.</td>
  </tr>
</table>

#### **4. Serving Data for Analytics**
<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Focus</strong></th>
    <th align="left"><strong>Business Impact</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/06a%20Data%20Warehouse%20Load.md">06a Data Warehouse Load.md</a></strong></td>
    <td>Load Gold-layer data into the <strong>Warehouse</strong> using T-SQL.</td>
    <td>Optimized for speed: BI tools and reports run <em>fast</em>.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/06b%20Data%20Warehouse%20Query.md">06b Data Warehouse Query.md</a></strong></td>
    <td>Write T-SQL queries to analyze data.</td>
    <td>Answer questions like <em>"What’s our monthly revenue by region?"</em> in seconds.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/06c%20Monitor%20Data%20Warehouse.md">06c Monitor Data Warehouse.md</a></strong></td>
    <td>Track query performance, resource use, and health.</td>
    <td>Proactively fix slowdowns before users complain.</td>
  </tr>
</table>

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

<div align="center">
<img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExajJtMDRmaXl5ZGQyZHcxMXA3M3l0bjZweG05MTZtdnZrM3c1cGl6NSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3o7btPCcdNni12S_1m/giphy.gif" width="150">
<h2><strong>Chapter 2: From Raw Data to Actionable Insights</strong></h2>
</div>

**Problem:** Data is messy. Models fail. Teams waste time cleaning instead of analyzing.
**Solution:** A **repeatable, scalable** process to explore, clean, and train data—*without reinventing the wheel every time*.

This chapter focuses on **preparation and transformation** using Spark, Data Science tools, and automation. No theory—just what works.

### **1. Start Here: The 5-Minute Overview**
<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>What It Covers</strong></th>
    <th align="left"><strong>Why It Matters</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/Read%20Me.md">Read Me.md</a></strong></td>
    <td>Goals, tools, and workflow for this chapter.</td>
    <td>Skip this, and you’ll waste time guessing how pieces fit together.</td>
  </tr>
</table>

### **2. Explore and Understand Your Data**
**Mistake:** Jumping into modeling without knowing your data.
**Fix:** Profile, visualize, and validate *first*.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Your Task</strong></th>
    <th align="left"><strong>Outcome</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/2.%20Analyze%20Spark.md">2. Analyze Spark.md</a></strong></td>
    <td>Use Spark to analyze large datasets efficiently.</td>
    <td>Find patterns, outliers, and issues <em>before</em> they derail your analysis.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.1%20Data%20Science%20Explore%20Data.md">8.1 Data Science Explore Data.md</a></strong></td>
    <td>Dive deep into data distributions, correlations, and quality.</td>
    <td>Answer: <em>"Is this data even usable for modeling?"</em></td>
  </tr>
</table>

### **3. Clean and Preprocess (The Boring but Critical Step)**
**Old Way:** Manual Excel cleaning, inconsistent scripts.
**New Way:** **Automated wrangling** with audit trails.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Action</strong></th>
    <th align="left"><strong>Result</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.2%20Data%20Science%20Preprocess%20Data%20Wrangler.md">8.2 Data Science Preprocess Data Wrangler.md</a></strong></td>
    <td>Clean, transform, and standardize data with Wrangler.</td>
    <td>No more "I forgot which column I fixed." Reproducible prep.</td>
  </tr>
</table>

### **4. Train and Deploy Models (Where the Magic Happens)**
**Goal:** Build models that *actually* work in production.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Focus</strong></th>
    <th align="left"><strong>Business Impact</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.3%20Data%20Science%20Train.md">8.3 Data Science Train.md</a></strong></td>
    <td>Train ML models with Fabric’s built-in tools.</td>
    <td>From prototype to production—<em>without hand-offs</em>.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.4%20Data%20Science%20Batch.md">8.4 Data Science Batch.md</a></strong></td>
    <td>Schedule batch scoring for new data.</td>
    <td>Models stay fresh. No manual reruns.</td>
  </tr>
</table>

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

<div align="center">
<img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExMmV0Mmx3bm50Nmc4ZzRvdG9oMDI3OGE2NG5rY3BwZzM1am45YWc3eSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/sIIhZliB2McAo/giphy.gif" width="150">
<h2><strong>Chapter 3: From Batch to Real-Time AI</strong></h2>
</div>

**Problem:** Your analytics are slow, reactive, and stuck in spreadsheets.
**Solution:** **Real-time insights, automated actions, and scalable AI**—without the PhD.

This chapter covers **advanced analytics, event-driven systems, and AI that actually ships**.

### **1. Start Here: The 5-Minute Primer**
<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>What It Covers</strong></th>
    <th align="left"><strong>Why It Matters</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/Read%20Me.md">Read Me.md</a></strong></td>
    <td>The <em>what</em>, <em>why</em>, and <em>how</em> of advanced analytics in Fabric.</td>
    <td>Skip this, and you’ll build cool demos that never go live.</td>
  </tr>
</table>

### **2. Ingest and Process Data at Scale**
**Mistake:** Waiting for batch jobs to finish.
**Fix:** **Real-time pipelines + notebooks** for speed and flexibility.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Your Task</strong></th>
    <th align="left"><strong>Outcome</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/10.%20Ingest%20Notebooks.md">10. Ingest Notebooks.md</a></strong></td>
    <td>Use notebooks for flexible data ingestion.</td>
    <td>No more rigid ETL—adapt on the fly.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/9%20Real%20Time%20Analytics%20Eventstream.md">9 Real Time Analytics Eventstream.md</a></strong></td>
    <td>Set up event streams for live data.</td>
    <td>React to events <em>as they happen</em>—not tomorrow.</td>
  </tr>
</table>

### **3. Real-Time Intelligence (The Game-Changer)**
**Old Way:** Dashboards that show *yesterday’s* data.
**New Way:** **Live monitoring + automated triggers**.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Action</strong></th>
    <th align="left"><strong>Result</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/7.%20Real%20Time%20Intelligence.md">7. Real Time Intelligence.md</a></strong></td>
    <td>Build real-time dashboards and alerts.</td>
    <td>Spot trends <em>before</em> they become crises.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/11%20Data%20Activator.md">11 Data Activator.md</a></strong></td>
    <td>Automate actions based on data changes.</td>
    <td>No more manual "check the dashboard" routines.</td>
  </tr>
</table>

### **4. Query and Analyze High-Velocity Data**
**Goal:** Ask complex questions *fast*—even on streaming data.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Focus</strong></th>
    <th align="left"><strong>Business Impact</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/12.%20Query%20Data%20in%20KQL%20Database.md">12. Query Data in KQL Database.md</a></strong></td>
    <td>Use KQL for log analytics and time-series data.</td>
    <td>Find anomalies in seconds, not hours.</td>
  </tr>
</table>

### **5. Data Science That Ships (Not Just Experiments)**
**Problem:** Models die in Jupyter notebooks.
**Solution:** **End-to-end ML in Fabric**.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Task</strong></th>
    <th align="left"><strong>Outcome</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.%20Data%20Science%20Get%20Started.md">8. Data Science Get Started.md</a></strong></td>
    <td>Set up your data science environment.</td>
    <td>No more "it works on my machine."</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.1%20Data%20Science%20Explore%20Data.md">8.1 Data Science Explore Data.md</a></strong></td>
    <td>Explore data with purpose.</td>
    <td>Avoid training models on garbage.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.2%20Data%20Science%20Preprocess%20Data%20Wrangler.md">8.2 Data Science Preprocess Data Wrangler.md</a></strong></td>
    <td>Clean and prep data <em>at scale</em>.</td>
    <td>Reproducible pipelines, not one-off scripts.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.3%20Data%20Science%20Train.md">8.3 Data Science Train.md</a></strong></td>
    <td>Train and validate models.</td>
    <td>Models that <em>actually</em> deploy.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/8.4%20Data%20Science%20Batch.md">8.4 Data Science Batch.md</a></strong></td>
    <td>Schedule batch scoring.</td>
    <td>Keep models fresh <em>without manual work</em>.</td>
  </tr>
</table>

### **Where Teams Fail**
1. **Stuck in batch?** Your competitors are reacting in real time.
2. **Ignoring event streams?** You’re flying blind between reports.
3. **Data science in silos?** Models that never leave the lab.

### **Your First Step**
1. Read **[Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/Read%20Me.md)** (5 min).
2. Pick **one** real-time use case (e.g., fraud detection, live monitoring).
3. Start with **[9 Real Time Analytics Eventstream.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%203%20-%20Advanced%20Analytics%20%26%20AI/9%20Real%20Time%20Analytics%20Eventstream.md)**.

**No more "we’ll get to real-time later."** Start now.

<div align="center">
<img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExZnRna3R2czZua3gyMmFocW90NmYxemwzMGRkbmJnY2FkbmZqMHF2eSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/u2dI2h52gAzNS/giphy.gif" width="150">
<h3><strong>Chapter 4: Reports That Actually Get Used</strong></h3>
</div>

**Problem:** Dashboards are slow, confusing, or ignored. Business teams still export to Excel.
**Solution:** **Fast, secure, and actionable** BI—built for scale, not just pretty visuals.

This chapter turns raw data into **trusted, self-service insights**—without the chaos.

### **1. Start Here: The 5-Minute Blueprint**
<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>What It Covers</strong></th>
    <th align="left"><strong>Why It Matters</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/Read%20Me.md">Read Me.md</a></strong></td>
    <td>The <em>what</em>, <em>why</em>, and <em>how</em> of BI in Fabric.</td>
    <td>Skip this, and you’ll build reports nobody trusts.</td>
  </tr>
</table>

### **2. Build the Right Data Model (Do This First)**
**Mistake:** Dumping tables into Power BI and calling it a day.
**Fix:** **Star schemas + DAX** for performance and clarity.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Your Task</strong></th>
    <th align="left"><strong>Outcome</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/14%20Create%20A%20Star%20Schema%20Model.md">14 Create A Star Schema Model.md</a></strong></td>
    <td>Design a star schema for your data warehouse.</td>
    <td>Queries run <em>fast</em>. Users find what they need.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/14%20Create%20Dax%20Calculations.md">14 Create Dax Calculations.md</a></strong></td>
    <td>Write DAX measures for KPIs.</td>
    <td>No more "Why doesn’t this number match Excel?"</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/15%20Design%20Scalable%20Semantic%20Models.md">15 Design Scalable Semantic Models.md</a></strong></td>
    <td>Optimize models for enterprise use.</td>
    <td>Handles 10x data without choking.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/15.1%20Work%20With%20Model%20Relationships.md">15.1 Work With Model Relationships.md</a></strong></td>
    <td>Fix relationship issues.</td>
    <td>No more "blank visuals" in reports.</td>
  </tr>
</table>

### **3. Dashboards That Don’t Suck**
**Old Way:** Static reports that answer *yesterday’s* questions.
**New Way:** **Real-time, interactive, and reusable**.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Action</strong></th>
    <th align="left"><strong>Result</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/13%20Real%20Time%20Dashboards.md">13 Real Time Dashboards.md</a></strong></td>
    <td>Build live dashboards with Fabric.</td>
    <td>Decisions made on <em>current</em> data, not last month’s.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/16%20Create%20Reusable%20Power%20BI%20Assets.md">16 Create Reusable Power BI Assets.md</a></strong></td>
    <td>Create templates, themes, and components.</td>
    <td>Stop reinventing the wheel for every report.</td>
  </tr>
</table>

### **4. Make It Fast (Because Nobody Waits)**
**Goal:** Reports that load in **under 3 seconds**.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Focus</strong></th>
    <th align="left"><strong>Business Impact</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/16.1%20Optimize%20Power%20BI%20Performance%20Using%20External%20Tools.md">16.1 Optimize Power BI Performance Using External Tools.md</a></strong></td>
    <td>Use Tabular Editor, DAX Studio, etc.</td>
    <td>Reports that <em>don’t</em> spin forever.</td>
  </tr>
</table>

### **5. Lock It Down (Before It’s Too Late)**
**Problem:** Sensitive data leaks. Users see *everything*.
**Solution:** **Row-level security + access controls**.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Task</strong></th>
    <th align="left"><strong>Outcome</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/17%20Enforce%20Model%20Security.md">17 Enforce Model Security.md</a></strong></td>
    <td>Set up RLS and object-level security.</td>
    <td>Sales sees <em>only</em> their region’s data.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/19%20Secure%20Data%20Access.md">19 Secure Data Access.md</a></strong></td>
    <td>Manage permissions in Fabric.</td>
    <td>No more "Oops, wrong person saw this."</td>
  </tr>
</table>

### **6. Monitor and Maintain (Or It Will Break)**
**Reality:** Reports slow down. Data drifts. Users complain.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Action</strong></th>
    <th align="left"><strong>Result</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/18%20Monitor%20Hub.md">18 Monitor Hub.md</a></strong></td>
    <td>Track usage, performance, and errors.</td>
    <td>Fix issues <em>before</em> the CEO notices.</td>
  </tr>
</table>

### **7. Advanced: Query Like a Pro**
**For when SQL isn’t enough.**

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Focus</strong></th>
    <th align="left"><strong>Use Case</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/20%20Work%20With%20Database.md">20 Work With Database.md</a></strong></td>
    <td>Direct database queries.</td>
    <td>Complex analytics without Power BI limits.</td>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/20a%20Work%20With%20GraphQL.md">20a Work With GraphQL.md</a></strong></td>
    <td>Use GraphQL for flexible data fetching.</td>
    <td>Build custom apps on top of your BI.</td>
  </tr>
</table>

### **Where Teams Fail**
1. **No star schema?** Queries timeout. Users give up.
2. **Ignoring DAX?** Numbers don’t add up. Trust erodes.
3. **No security?** Compliance nightmares.
4. **No monitoring?** "Why is this report so slow?" becomes a daily question.

### **Your First Step**
1. Read **[Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/Read%20Me.md)** (5 min).
2. Build your **star schema** ([Guide](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%204%20-%20Reporting%20%26%20Business%20Intelligence/14%20Create%20A%20Star%20Schema%20Model.md)).
3. **Ask yourself:** *Which report causes the most arguments in meetings?*
   Fix it first.

**No more "the data is wrong."** Build BI that works.

<div align="center">
<img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExM3Zob2Z0ZGFjcWVjaDkzcTgwM2ZtZ3pqb3A1MTY4bDZqZXIzcHBqYSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/26tn33aiTi1jkl6H6/giphy.gif" width="150">
<h2><strong>Chapter 5: Deploy Without the Drama</strong></h2>
</div>

**Problem:** "It works on my machine" doesn’t cut it. Deployments break. Teams blame each other.
**Solution:** **Automated, repeatable, and zero-downtime** releases—so you can sleep at night.

This chapter is about **getting your work into production**—*without the fire drills*.

### **1. Start Here: The 5-Minute Survival Guide**
<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>What It Covers</strong></th>
    <th align="left"><strong>Why It Matters</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%205%20-%20Deployment%20%26%20Operations/Read%20Me.md">Read Me.md</a></strong></td>
    <td>The <em>what</em>, <em>why</em>, and <em>how</em> of Fabric deployments.</td>
    <td>Skip this, and you’ll be fixing production at 2 AM.</td>
  </tr>
</table>

### **2. Deployment Pipelines (Your New Best Friend)**
**Mistake:** Manual copies from Dev → Test → Prod.
**Fix:** **Automated pipelines** with rollback plans.

<table style="width:100%;">
  <tr>
    <th align="left"><strong>Document</strong></th>
    <th align="left"><strong>Your Task</strong></th>
    <th align="left"><strong>Outcome</strong></th>
  </tr>
  <tr>
    <td><strong><a href="https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%205%20-%20Deployment%20%26%20Operations/21.%20Deployment%20Pipelines%20in%20Microsoft%20Fabric.md">21. Deployment Pipelines in Microsoft Fabric.md</a></strong></td>
    <td>Set up CI/CD for Fabric.</td>
    <td>One-click deployments. No more "forgot to update the parameter" disasters.</td>
  </tr>
</table>

### **Where Teams Fail**
1. **Manual deployments?** Human error *will* bite you.
2. **No rollback plan?** Outages last *hours*.
3. **Skipping testing?** Production becomes the test environment.

### **Your First Step**
1. Read **[Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%205%20-%20Deployment%20%26%20Operations/Read%20Me.md)** (5 min).
2. **Build your first pipeline** ([Guide](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%205%20-%20Deployment%20%26%20Operations/21.%20Deployment%20Pipelines%20in%20Microsoft%20Fabric.md)).
3. **Ask yourself:** *What’s the riskiest part of our current deployment process?*
   Automate it *today*.

**No more "hope it works."** Deploy with confidence.
