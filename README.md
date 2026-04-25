# Lab-2.3-Storing-and-Analyzing-Data-by-Using-Amazon-Redshift

In this laboratory, we implemented a proof of concept (POC) using Amazon Redshift as a cloud-based data warehouse. The objective was to store, process, and analyze a large dataset of music ticket sales stored in Amazon S3.

The lab covered the full pipeline:
- IAM role configuration
- Redshift cluster deployment
- Table creation
- Data ingestion from S3
- Data querying using SQL
- Query execution using AWS CLI
- IAM policy validation

---

## 1. IAM Role Configuration

We started by reviewing the IAM role **MyRedshiftRole**.

### Purpose
This role allows Amazon Redshift to:
- Read data from Amazon S3
- Access EC2 and VPC resources

### Key Components
- **AmazonS3ReadOnlyAccess**: Enables Redshift to read data from S3 buckets
- **RedshiftIAMLabPolicy**: Allows Redshift to interact with network infrastructure (VPC, subnets, interfaces)

### Key Output
- Extracted the **ARN (Amazon Resource Name)** of the role, required for the `COPY` command.

---

## 2. Redshift Cluster Deployment

We created a Redshift cluster to serve as the data warehouse.

### Configuration

| Parameter | Value |
|----------|------|
| Cluster Identifier | redshift-cluster-1 |
| Node Type | ra3.large |
| Number of Nodes | 2 |
| Database | dev |
| Admin User | awsuser |

### Explanation

Amazon Redshift clusters consist of:
- **Leader node**: Handles query planning and coordination
- **Compute nodes**: Execute queries in parallel

Using a multi-node configuration improves performance and scalability.

---

## 3. Network Configuration (Security Group)

We configured a **Security Group** to allow access to the Redshift cluster.

### Configuration

| Setting | Value |
|--------|------|
| Port | 5439 |
| Protocol | TCP |
| Source | 0.0.0.0/0 |

### Explanation

- Port **5439** is the default port for Redshift.
- This configuration allows inbound traffic for database connections.
- The cluster is still inside a VPC (not public), ensuring controlled access.

---

## 4. Database and Table Creation

We connected to the Redshift Query Editor and created three tables:

### Tables Created

#### Users Table
Stores user preferences and demographic data.

#### Date Table
Stores calendar information used for time-based analysis.

#### Sales Table
Stores transactional ticket sales data.

### Example (Sales Table)

```sql
create table sales(
    salesid integer not null,
    listid integer not null distkey,
    sellerid integer not null,
    buyerid integer not null,
    eventid integer not null,
    dateid smallint not null sortkey,
    qtysold smallint not null,
    pricepaid decimal(8,2),
    commission decimal(8,2),
    saletime timestamp);
