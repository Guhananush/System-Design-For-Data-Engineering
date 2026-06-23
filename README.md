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

### Example: Real-Time Driver Earnings System            

Consider a ride-sharing platform.           

Whenever a driver completes a ride:                   

Ride Completed               
        ↓                 
Event Generated              
        ↓                
Kafka                
        ↓                 
Spark Streaming            
        ↓                    
Calculate Earnings                
        ↓                
Redis                    
        ↓                   
Driver App                            

The driver sees updated earnings instantly.This is a classic streaming use case.

### Why Kafka?

Kafka acts as a distributed event buffer between producers and consumers.

Producer          
    ↓            
 Kafka            
    ↓         
Consumer
### Benefits          
- Decouples producers and consumers              
- Stores events temporarily             
- Supports replay of events        

## Architecture Selection Guide
### Choose Batch When
- Daily reporting is sufficient              
- Cost optimization is important               
- Business users can wait for updates               

### Examples:
 
- Marketing Analytics              
- Finance Reports             
- Historical Dashboards             

### Choose Streaming When
- Decisions must be made instantly
- Data freshness is critical
- User experience depends on real-time updates

Examples:

- Fraud Detection
- Product Recommendations
- Live Monitoring Systems

## Step 3: Data Modeling

### Overview

Data Modeling is the process of organizing data into structures that are efficient for storage, querying, analytics, and reporting.

In Data Engineering System Design interviews, simply drawing fact and dimension tables is not enough. Interviewers often ask deeper questions such as:

- Why did you choose this schema?
- Why not denormalize the data?
- What happens when dimensions change?
- How will the model perform at scale?
- How do you optimize query performance?

### Medallion Architecture
What is Medallion Architecture?

Medallion Architecture is a layered approach for organizing data within a Lakehouse architecture.

The architecture consists of three layers:           

Bronze              
   ↓            
Silver           
   ↓           
Gold            

### Each layer progressively improves data quality and usability.

### Bronze Layer (Raw Data)
Purpose

The Bronze Layer stores raw data exactly as received from source systems.

Examples:

- JSON Events
- CDC Logs
- API Responses
- Database Exports
#### Characteristics
- No transformations
- No business logic
- Preserves original data
- Supports reprocessing

#### Why Bronze Exists

If downstream processing fails or business logic changes, engineers can always reprocess data from the raw source.

This makes the Bronze Layer the foundation of the Lakehouse.

### Silver Layer (Cleaned & Structured Data)
#### Purpose

The Silver Layer contains validated, cleaned, and structured data.

This layer is considered the organization's trusted source of truth.

#### Typical Transformations
- Schema Enforcement
- Type Casting
- Data Validation
- Deduplication

### Gold Layer (Business Layer)
#### Purpose

The Gold Layer contains pre-aggregated and business-ready datasets.

It is optimized for:

- Reporting
- Dashboards
- KPIs
- Analytics

#### Characteristics
- Pre-joined data
- Pre-aggregated metrics
- Fast query performance

## Gold Layer

Aggregates business metrics.

### Daily_Artist_Streams
-Date,         
-Artist_ID,          
-Total_Streams              

This allows dashboards to query results instantly.

###Benefits of Medallion Architecture
#### Data Quality

Each layer improves data quality.

#### Reusability

Multiple teams can consume the same Silver data.

#### Scalability

Supports large-scale analytics workloads.

#### Faster Reporting

Gold tables eliminate expensive joins during dashboard execution.

#### Easier Maintenance

Problems can be isolated to specific layers.

## Step 4: Storage & File Formats
### Overview

Storage design plays a critical role in the performance, scalability, and reliability of data systems.

The same query can execute significantly faster or slower depending on how data is stored and which file format is used.

For example, consider a table with:

- 200 Columns
- 1 Billion Rows

If an analyst frequently queries only 3 columns, choosing the wrong storage format can force the system to scan all 200 columns, resulting in unnecessary computation and higher costs.

Understanding storage formats, open table formats, and database types is essential for designing efficient data platforms.

### Key Concepts

The most important storage concepts are:          

1. Row-Based vs Column-Based File Formats
2. Delta Lake, Iceberg, and Hudi
3. Relational vs Non-Relational Databases

## Row-Based vs Column-Based Storage

#### Row-Based Storage

In row-based storage, all columns belonging to a single record are stored together.

Row 1:
[101][John][500][Bengaluru][Electronics]

Row 2:
[102][Alice][300][Chennai][Fashion]

Each row is written as a complete record.

### Advantages
- Fast inserts
- Fast updates
- Efficient for transactional systems
- Ideal for write-heavy workloads
### Common Formats
- Avro
- CSV
- Traditional OLTP Databases

### Column-Based Storage

In column-based storage, values from the same column are stored together.

Order_ID:
101
102

Name:
John
Alice

Revenue:
500
300

City:
Bengaluru
Chennai

Data is stored column-by-column rather than row-by-row.

#### Advantages
- Reads only required columns
- Reduces I/O operations
- Better compression
- Faster analytical queries         
#### Common Formats
- Parquet
- ORC

### Parquet (Column-Based Format)
What is Parquet?

Parquet is a columnar storage format optimized for analytical workloads.

It stores data column-by-column, allowing query engines to read only the columns needed for a query.

### Column Pruning

This optimization is called:         

#### Column Pruning            

The query engine ignores unnecessary columns, reducing scan time and improving performance.

#### Best Use Cases

Parquet is ideal for:

- Data Warehouses
- Data Lakes
- Business Intelligence

#### Why Parquet is Fast

- Reads only required columns
- Better compression
- Lower storage costs
- Faster query execution

#### Avro (Row-Based Format)
##### What is Avro?

Avro is a row-based serialization format optimized for streaming and write-heavy workloads.

Each event is written as a complete record.

#### Why Avro is Good for Streaming

Streaming systems continuously receive events:

- Order Created
- Button Clicked
- Ride Completed
- Payment Processed

Each event must be written immediately.Avro allows fast serialization and efficient writes.

#### Why Delta Lake, Iceberg, and Hudi Matter
##### Problem with Raw Parquet Files

Early data lakes stored only Parquet files inside cloud storage.

Example:

S3 Bucket         
   ├── file1.parquet       
   ├── file2.parquet          
   ├── file3.parquet           

The issue is that Parquet files alone provide no table management.       

#### Delta Lake / Iceberg / Hudi

Open table formats solve these challenges.

Examples:

- Delta Lake
- Apache Iceberg
- Apache Hudi

#### Relational vs Non-Relational Databases

System Design interviews often ask:

Which database would you choose and why?

#### Relational Databases

Use relational databases when:

- Data is structured
- Relationships are important
- ACID guarantees are required
- Complex SQL queries are needed
#### Examples
- PostgreSQL
- MySQL
- SQL Server and Oracle

#### Non-Relational Databases

Use NoSQL databases when:

- Data structure changes frequently
- Data is semi-structured
- Data is unstructured
- High scalability is required

Examples
- MongoDB
- Cassandra
- DynamoDB and Redis

## Step 5: Data Quality & Observability
#### Overview

A data pipeline is only as good as the data flowing through it.

Even if the architecture, storage layer, and processing logic are designed perfectly, poor-quality data can lead to incorrect business decisions, failed machine learning models, and unreliable analytics.

In production systems, data quality issues occur frequently:

- Source systems change unexpectedly
- Fields become null
- Data volumes drop suddenly
- Duplicate records appear
- Schemas evolve without notice

Without proper monitoring and validation, pipelines may continue running successfully while producing incorrect results.

### Data Quality Dimensions

#### Data quality is not simply "good" or "bad".

Instead, data should be validated across multiple dimensions.

### 1. Completeness
Question

Is all expected data present?

Completeness checks ensure that no records or critical fields are missing.

#### Examples

##### Expected:

Daily Orders = 10 Million

##### Received:

Daily Orders = 7.5 Million

Possible Issue:

- Source system failure
- Missing ingestion jobs

### 2. Accuracy
Question

Is the data correct?

Accuracy checks validate business logic and detect impossible values.

#### Examples

Invalid Order Amount:

Order Amount = -500

Invalid Delivery Event:

Delivery Time < Order Time

These values are technically possible in source systems but should never pass validation.

### 3. Consistency
Question

Does the same data agree across systems?

Consistency ensures that related systems report matching values.

#### Example

##### Order System:

March Revenue = $2 Million

Payment System:

March Revenue = $1.8 Million

These numbers should match.

A mismatch indicates data inconsistency.

### 4. Freshness
Question

Is the data up to date?

Freshness validates whether data arrives within the expected SLA.

#### Example

Expected:

Dashboard Refresh = Daily

Actual:

Last Refresh = 3 Days Ago

Business decisions are now being made using stale data.

### 5. Uniqueness
Question

Are duplicate records present?

Duplicate records can significantly distort analytics.

#### Example

##### A pipeline retries after failure:

Batch 101 Written
Batch 101 Written Again

##### Result:

Duplicate Records
Inflated Metrics

### Data Contracts
#### What is a Data Contract?

A Data Contract is a formal agreement between:

Data Producer
        ↔
Data Consumer

#### The contract defines:

- Schema
- Data Types
- Required Fields
- Allowed Values

### Why Data Contracts Matter

Traditional data quality checks identify problems only after data enters the pipeline.

Data Contracts prevent bad data from entering the system in the first place.

This approach is known as:

##### Shift-Left Data Quality

### Example Architecture
Microservice       
      ↓       
Data Contract           
      ↓                      
Data Pipeline              
      ↓           
Bronze Layer          

The contract validates incoming data before it reaches storage. 

#### Schema Evolution

Suppose a producer wants to change:

Schema V1
    ↓
Schema V2

A migration process should occur before deployment.

Typical requirements include:

- Versioning
- Backward Compatibility
- Advance Notice Period

This prevents downstream failures.


### Pipeline Observability
#### What is Observability?

Data Quality focuses on the data.

Observability focuses on the pipeline itself.

#### Key Question

Is the pipeline healthy?

Even if data is correct, the pipeline may still experience operational issues.

#### Pipeline Monitoring

Observability tracks:

#### Job Status
Running
Succeeded
Failed

#### Execution Time

##### Expected:

40 Minutes

Actual:

3 Hours

##### Possible Causes:

- Increased Data Volume
- Cluster Resource Issues

#### Pipeline Latency

Tracks how quickly data moves through the system.

#### Resource Utilization

##### Monitors:

- CPU
- Memory
- Storage
- Cluster Health
   
### Failure Detection

#### Detects:

- Failed Tasks
- Missing Runs
- Dependency Issues

#### Example Monitoring Flow
Source          
   ↓           
Pipeline          
   ↓            
Storage          
          
Monitor:     

- Did the pipeline start?
- Did it complete successfully?
- How long did it take?
- How much data was processed?

## Step 6: Scalability, Backfills & DataOps

### Overview

Designing a pipeline is only the beginning.

In real-world production systems, pipelines must handle failures, schema changes, business logic updates, increasing data volumes, and operational challenges without disrupting downstream consumers.

#### A production-ready Data Engineering system must be:

- Scalable
- Reliable
- Idempotent
- Recoverable
- Maintainable

This section focuses on the operational aspects of Data Engineering that ensure long-term reliability.

### Key Concepts

The most important concepts are:

- Idempotency
- Backfills
- Schema Evolution
- DataOps Best Practices

### Idempotency: The Golden Rule
#### What is Idempotency?    

A pipeline is idempotent if running it multiple times with the same input always produces the same output.       

Run Pipeline Once      
        ↓          
Output A       

Run Pipeline Five Times             
        ↓         
Output A             

The result remains identical regardless of how many times the pipeline executes.              

### Why is Idempotency Important?

Production pipelines frequently fail due to:

- Network Issues
- Cluster Failures
- Timeouts
- Infrastructure Outages

When failures occur, engineers often rerun pipelines.       

Without idempotency:           

Run 1           
  ↓             
100 Records         

Run Again         
  ↓                     
200 Records (Duplicates)           

This leads to corrupted analytics and incorrect business metrics.

### Achieving Idempotency
#### Bad Approach

Using INSERT statements.

INSERT INTO orders

Every rerun adds duplicate records.

### Recommended Approach

#### Using MERGE operations.

MERGE INTO orders

Behavior:

- Update if record exists
- Insert if record does not exist

Benefits:

- No duplicate records
- Safe pipeline reruns
- Reliable incremental processing

### Backfills
#### What is a Backfill?

A backfill is the process of reprocessing historical data after business logic or pipeline code changes.

#### Why Backfills Are Needed

Business requirements evolve over time.

Historical data often needs to be recalculated using updated logic.

### Backfill Strategy

Modern table formats such as Delta Lake and Iceberg make backfills safer.

#### Example:

Affected Dates:

2026-01-01
      ↓
2026-04-01

Only those partitions are reprocessed.

### Why This Works

Open table formats support:

#### ACID Transactions

Guarantees:

Success          
OR              
Rollback

Partial updates never become visible.

### Partition Overwrites

Instead of rebuilding the entire table:

Overwrite:
2026-01
2026-02
2026-03

Only affected partitions are replaced.

#### Benefits:

- Faster recovery
- Lower compute cost
- Minimal disruption

## Schema Evolution
### The Reality

Schemas change.

#### Examples:

- New columns are added
- Existing columns are renamed
- Data types change
- APIs evolve

If not handled correctly:

- Pipelines fail
- Dashboards break
- Data becomes inconsistent


### Common Schema Changes
#### New Column Added
discount_code

added to order events.

#### Column Renamed
user_location       
      ↓         
device_location

#### Data Type Change
user_id : INTEGER          
      ↓      
user_id : STRING   
           
## Recommended Strategy
### Flexible Bronze Layer

The Bronze Layer should accept all incoming data.

Source
   ↓
Bronze

Characteristics:

- Schema Drift Allowed
- New Columns Accepted
- Raw Data Preserved

### Purpose:

Never lose incoming data.

### Strict Silver & Gold Layers           
Bronze              
   ↓           
Silver           
   ↓           
Gold          

#### Silver and Gold should enforce:

- Schema Validation
- Data Types
- Business Rules
- Quality Checks

New fields must be:

- Reviewed
- Validated
- Explicitly Mapped

before reaching downstream consumers.

### Delta Lake Support

Delta Lake provides:

### Merge Schema

Automatically detects new fields.

mergeSchema = true

Benefits:

- Handles schema evolution gracefully
- Prevents pipeline failures
- Maintains backward compatibility

### DataOps Principles

DataOps applies software engineering practices to data platforms.

The goal is to improve reliability, deployment speed, and operational efficiency.

Core Principles
Automation

Automate:

Deployments
Testing
Monitoring
Data Quality Validation
Version Control

Store:

Pipeline Code
SQL Transformations
Infrastructure Definitions

inside Git repositories.

Continuous Testing

Validate:

Data Quality
Schemas
Business Logic

before deployment.

Monitoring

Track:

Pipeline Health
Data Freshness
SLA Compliance
Resource Utilization

