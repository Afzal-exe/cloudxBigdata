# LAB: Run a WordCount Job using Amazon EMR

---

# Lab 1 — Objective

## Goal

* Launch an EMR cluster
* Upload data to S3
* Run a Hadoop (MapReduce) job
* View results

---

## What this lab validates

* EMR cluster provisioning
* Integration with S3
* Distributed data processing
* Understanding managed Hadoop

---

# Lab 2 — Architecture Overview

## Components used

* Amazon EMR — Managed Hadoop/Spark platform
* Amazon S3 — Input/output storage
* Amazon EC2 — Cluster nodes

---

## Flow

1. Upload data → S3
2. EMR reads data from S3
3. Run MapReduce job
4. Output stored back in S3

---

# Lab 3 — Prerequisites

## Required

* AWS account
* IAM permissions for:

  * EMR
  * EC2
  * S3

---

## Create S3 bucket

```text
Bucket name: emr-lab-yourname
```

**Explanation:**
S3 acts as persistent storage. EMR clusters are temporary.

---

# Lab 4 — Prepare Input Data

## Create sample file locally

```text
Hadoop is a big data framework
EMR simplifies big data processing
Hadoop and Spark run on EMR
```

Save as:

```text
input.txt
```

---

## Upload to S3

Go to S3 → Upload:

```
s3://emr-lab-yourname/input/input.txt
```

---

**Explanation:**
EMR does not rely on local disk for input; it uses S3.

---

# Lab 5 — Create EMR Cluster

## Navigate to EMR Console

Create cluster with:

### Basic configuration

* Cluster name: `emr-lab`
* Release: EMR 6.x
* Applications:

  * Hadoop
  * Spark (optional but recommended)

---

### Hardware

* Instance type: t3.medium (cost-effective)
* Instance count: 1 (single-node lab)

---

### Security

* Use default EMR roles:

  * EMR_DefaultRole
  * EMR_EC2_DefaultRole

---

### Storage

* Keep default

---

### Create cluster

---

**Explanation:**
EMR provisions EC2 instances and installs Hadoop automatically.

---

# Lab 6 — Add Step (Run WordCount)

## Add a Step

Choose:

```
Hadoop MapReduce
```

---

## Configure step

### Jar location:

```text
command-runner.jar
```

---

### Arguments:

```text
hadoop-mapreduce-examples.jar wordcount s3://emr-lab-yourname/input s3://emr-lab-yourname/output
```

---

**Explanation:**

* Uses built-in Hadoop example
* Reads input from S3
* Writes output to S3

---

# Lab 7 — Monitor Job

## EMR Console

Check:

* Step status → Running → Completed

---

## If failure occurs

Common reasons:

* Output path already exists
* Incorrect S3 path
* IAM permission issues

---

# Lab 8 — View Output

Go to S3:

```
s3://emr-lab-yourname/output/
```

Open:

```
part-r-00000
```

---

## Example Output

```
Hadoop 2
EMR 2
data 2
```

---

**Explanation:**
This is the result of MapReduce processing.

---

# Lab 9 — Clean Up (Critical)

## Terminate cluster

Go to EMR → Terminate cluster

---

## Delete S3 data (optional)

---

**Explanation:**
EMR clusters incur cost while running.

---

# What You Learned

## 1. EMR vs Local Hadoop

* EMR = managed cluster
* No manual setup required

---

## 2. S3 Integration

* Input/output stored externally
* Enables scalability

---

## 3. Job Execution

* Steps run distributed jobs
* Managed by YARN internally

---
