# Fabric-Data-Engineering-Projects
This repository contains learning materials and code samples for Microsoft Fabric Data Engineering. Below is the index of all chapters and files with direct links.

### Chapter 1 - Foundational Data Layer

This chapter outlines the end-to-end process of establishing a modern data platform using Microsoft Fabric, from initial data ingestion to creating a queryable, high-performance analytical layer. Think of this as the first step to Fabric data engineering

To start things up please read this first
**[Read Me.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/Read%20Me.md)**

The Read Me file serves as the introduction for the chapter, providing a high-level overview of the project's goals. It outlines the architecture being constructed, the technologies involved, and the purpose of each subsequent guide, setting the context for the entire foundational data layer.

**[01 Lakehouse.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/01%20Lakehouse.md)**

This document introduces the Lakehouse, a central component in Microsoft Fabric that combines the scalability of a data lake with the features of a data warehouse. It explains the core concepts and guides the user through creating and configuring a new Lakehouse, setting the foundation for all subsequent data storage and processing activities.

**[03 Delta Lake.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/03%20Delta%20Lake.md)**

This section delves into Delta Lake, the open-source storage format that underpins the Fabric Lakehouse. It explains key features such as ACID transactions, time travel, and schema enforcement, which bring reliability and performance to the data lake. Understanding Delta Lake is crucial for managing data quality and versioning.

**[03b Medallion Lakehouse.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/03b%20Medallion%20Lakehouse.md)**

This guide details the implementation of the Medallion architecture, a best practice for organizing data into Bronze (raw), Silver (validated), and Gold (enriched) layers. It outlines how to structure the Lakehouse to progressively clean, transform, and aggregate data, promoting governance and reusability.

**[04 Ingest Pipeline.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/04%20Ingest%20Pipeline.md)**

This document provides a step-by-step guide on building a data pipeline within Fabric. It focuses on creating a repeatable process to pull data from a source system and land it in the Bronze layer of the Lakehouse, marking the first step in the data engineering lifecycle.

**[05 Dataflows Gen2.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/05%20Dataflows%20Gen2.md)**

This section explores Dataflows Gen2, a low-code data transformation service in Fabric. It demonstrates how to use its Power Query-based interface to clean and transform the raw data from the Bronze layer and load the refined output into the Silver layer of the Lakehouse.

**[06a Data Warehouse Load.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/06a%20Data%20Warehouse%20Load.md)**

This guide covers loading curated data from the Gold layer of the Lakehouse into the Fabric Data Warehouse. It focuses on using T-SQL commands to create a relational, high-performance analytical model ready for business intelligence and reporting.

**[06b Data Warehouse Query.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/06b%20Data%20Warehouse%20Query.md)**

Following the data load, this document demonstrates how to query and analyze data within the Fabric Data Warehouse. It provides examples of standard T-SQL queries to interact with the tables, highlighting the performance and analytical capabilities of the Warehouse.

**[06c Monitor Data Warehouse.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%201%20-%20Foundational%20Data%20Layer/06c%20Monitor%20Data%20Warehouse.md)**

This section addresses operational management, explaining how to monitor the performance and usage of the Data Warehouse. The guide covers using built-in Fabric tools and Dynamic Management Views (DMVs) to track query execution, resource consumption, and overall system health.

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
