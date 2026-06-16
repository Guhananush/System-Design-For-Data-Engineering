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
