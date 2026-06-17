# System-Design-For-Data-Engineering
System Design in Data Engineering is the process of planning, designing, and building data systems that can efficiently collect, store, process, and deliver data. It focuses on creating scalable, reliable, secure, and cost-effective architectures capable of handling large volumes of data from multiple sources.

## Step 1: Requirement Gathering in Data Engineering System Design     
### Overview         

Requirement Gathering is the most important phase of System Design. Before selecting technologies such as Kafka, Spark, Airflow, or Databricks, we must first understand the problem we are solving.

Many candidates immediately start drawing architectures, but strong system designers spend the first few minutes clarifying requirements.   

### Main Goal   

Understand:

- What the system should do
- Who will use the system
- How users will access the data
- How much data the system will process
- How fresh the data needs to be

### Types of Requirements
## 1. Functional Requirements

Functional requirements answer:

What should the system do?

Question 1: Who is the End User?

Identify who will consume the data.

Question 2: What Do They Need?        

Understand the type of data required.                   

Examples:              

- Aggregated Metrics
- Raw Events
- Historical Snapshots
### Example: Funnel Analysis
Homepage
   ↓
Search Product
   ↓
View Product
   ↓
Add To Cart
   ↓
Purchase

Business teams often want:

- User counts at each stage
- Drop-off rates
- Conversion rates

Question 3: How Will They Access the Data?

Possible access methods:

- SQL Queries
- Tableau Dashboards
- Power BI Dashboards
- REST APIs

The access pattern influences system design decisions.

Example Functional Requirement

The marketing team requires:

- Daily funnel metrics
- Segmentation by user type
- Segmentation by device type
- Access through Tableau

This becomes the functional requirement for the system.

## 2. Non-Functional Requirements

Non-functional requirements answer:

How should the system behave?

### A. Latency (SLA)

How fresh must the data be?

#### Batch Processing
- Data freshness: 1 hour or more
- Lower cost
- Simpler architecture
#### Streaming Processing
- Data freshness: Seconds or minutes
- Higher cost
- More complex architecture

### B. Data Volume

How much data arrives daily?

Examples:

- 1 GB/day
- 10 TB/day
- Petabytes/day
#### Small Scale
- Pandas
- Single Machine Processing
#### Large Scale
- Spark
- Distributed Computing

#### C. Availability

How much downtime is acceptable?

Examples:

- Can tolerate a few minutes of downtime
- Must be available 24×7

Higher availability requirements increase system complexity.

#### D. Data Retention

How long should data be stored?

Examples:

- 90 Days
- 1 Year
- Forever

Older data is often moved to archival storage to reduce costs.

## Architecture Depends on Requirements

The same interview question can lead to completely different solutions.

### Scenario 1: Marketing Team
Requirements
- Daily reports
- Tableau dashboards
- 1-hour refresh latency
#### Architecture
Source Data    
     ↓          
Batch Pipeline               
     ↓                   
Spark                  
     ↓               
Delta Tables                
     ↓                
Tableau Dashboard         

### Scenario 2: ML Recommendation Team
Requirements
Real-time recommendations
Feature updates within seconds
#### Architecture       
User Events         
   ↓             
Kafka             
     ↓                 
Spark Streaming                
     ↓             
Feature Store                  
     ↓               
ML Model               

## Step 2: Pipeline Design (Batch vs Streaming)
### Overview

After gathering requirements, the next step in System Design is deciding how data will move through the system.

The first architectural decision is:   
     Should the pipeline be Batch or Streaming?

This decision influences the entire system design, including storage, processing engines, orchestration tools, and infrastructure costs.

###  Batch Processing     
#### What is Batch Processing?          
Batch processing collects data over a period of time and processes it together at scheduled intervals.

Instead of processing every event immediately, the system waits until a predefined schedule and then processes all accumulated data.

When to Use Batch Processing?              

Batch processing is suitable when:         

- Real-time updates are not required        
- Cost is more important than latency         
- Users can tolerate delayed data updates       
- Reports are generated hourly, daily, or weekly

#### Common Use Cases
- Daily Sales Reports
- Business Intelligence Dashboards
- Weekly Analytics Reports
  
  Typical Batch Architecture
Source Database          
       ↓      
Extract Data      
       ↓     
Data Lake (Bronze)       
       ↓        
Spark Transformations       
       ↓     
Silver Layer           
       ↓       
Aggregations        
       ↓         
Gold Layer         
       ↓            
Dashboard (Tableau / Power BI)

### Core Components
#### 1. Orchestration Layer

Responsible for scheduling and managing pipeline execution.

Popular Tools:

- Apache Airflow
- Prefect
- Dagster
 
#### 2. Processing Engine

Responsible for cleaning, transforming, and aggregating data.

Popular Tool:

- Apache Spark

#### 3. Storage Layer

Stores raw and processed data.

Examples:

- Amazon S3
- Azure Data Lake Storage (ADLS)

### Example: E-Commerce Reporting Pipeline
Schedule

2:00 AM

Airflow triggers pipeline

2:15 AM

Data extracted from operational database

3:00 AM

Spark performs transformations

5:00 AM

Aggregations generated

6:00 AM

Tableau dashboard refreshed

Business users access updated reports every morning.

### Streaming Processing
#### What is Streaming Processing?

Streaming processing continuously processes data as events arrive.

Instead of waiting for scheduled execution, data flows through the pipeline in near real-time.

## When to Use Streaming?

Streaming is suitable when: 

- Real-time dashboards are required
- Fraud detection systems need immediate decisions
- Recommendation systems require fresh data
- Data freshness must be measured in seconds
Common Use Cases
- Ride-Sharing Applications
- Fraud Detection
- Recommendation Systems

#### Typical Streaming Architecture
Website / Mobile App
          ↓
       Kafka
          ↓
Spark Streaming / Flink
          ↓
Serving Layer
          ↓
Application

#### Core Components
### 1. Event Ingestion

Captures events from applications and systems.

Popular Tool:

- Apache Kafka
### 2. Stream Processing

Processes events in real-time.

Popular Tools:

- Spark Structured Streaming
- Apache Flink

Common Operations:

- Filtering
- Enrichment
- Windowing
- Aggregation
### 3. Historical Storage

Stores processed events for long-term analytics.

Popular Storage:

- Delta Lake
### 4. Serving Layer

Provides low-latency access for applications.

Popular Technologies:

- Redis
- DynamoDB
- Cassandra

           


