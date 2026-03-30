# LAB: Word Count Analysis using Hadoop

---

# Lab 1 — Objective

## Goal

Process a text file using Hadoop to:

* Store data in HDFS
* Run a MapReduce job
* Analyze word frequency

---

## What this lab proves

By completing this lab, you validate:

* HDFS is working
* YARN is working
* MapReduce execution is successful

---

# Lab 2 — Start Hadoop Services

```bash
start-dfs.sh
start-yarn.sh
```

---

## Verification

```bash
jps
```

**Expected processes:**

* NameNode
* DataNode
* ResourceManager
* NodeManager

---

**Explanation:**
Ensures all Hadoop components are running before executing jobs.

---

# Lab 3 — Prepare Input Data

## Step 1: Create a sample file

```bash
mkdir ~/lab-data
vim ~/lab-data/input.txt
```

Add:

```text
Hadoop is a big data framework
Hadoop processes large datasets
Big data requires distributed systems
Hadoop is scalable and reliable
```

---

## Step 2: Verify file

```bash
cat ~/lab-data/input.txt
```

---

**Explanation:**
This file will act as input for the MapReduce job.

---

# Lab 4 — Upload Data to HDFS

## Step 1: Create HDFS directory

```bash
hdfs dfs -mkdir /input
```

---

## Step 2: Upload file

```bash
hdfs dfs -put ~/lab-data/input.txt /input
```

---

## Step 3: Verify upload

```bash
hdfs dfs -ls /input
```

---

**Explanation:**
Moves data from local filesystem to distributed HDFS.

---

# Lab 5 — Run MapReduce Job

## Step 1: Execute WordCount example

```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount /input /output
```

---

## Important Note

If `/output` already exists, Hadoop will fail.

Fix:

```bash
hdfs dfs -rm -r /output
```

---

**Explanation:**

* Uses built-in WordCount program
* Reads `/input`
* Writes results to `/output`

---

# Lab 6 — Monitor Job Execution

## Option 1: CLI output

You will see:

```text
map 100% reduce 100%
```

---

## Option 2: Web UI

Open:

* [http://localhost:8088](http://localhost:8088)

Check:

* Running/Completed jobs
* Resource usage

---

**Explanation:**
Confirms that YARN is scheduling and executing tasks.

---

# Lab 7 — View Output

## Step 1: List output files

```bash
hdfs dfs -ls /output
```

---

## Step 2: View results

```bash
hdfs dfs -cat /output/part-r-00000
```

---

## Example Output

```text
Hadoop 3
data 2
big 1
framework 1
```

---

**Explanation:**
Displays word frequency calculated by MapReduce.

---

# Lab 8 — Validate HDFS Storage

## Check file blocks

```bash
hdfs fsck /input -files -blocks
```

---

**Explanation:**
Shows how Hadoop splits files into blocks and distributes them.

---

# Lab 9 — Clean Up

```bash
hdfs dfs -rm -r /input
hdfs dfs -rm -r /output
rm -rf ~/lab-data
```

---

**Explanation:**
Removes lab data and resets environment.

---

# What You Actually Learned

## 1. HDFS

* Creating directories
* Uploading files
* Reading distributed data

---

## 2. MapReduce

* Input → Map → Shuffle → Reduce → Output

---

## 3. YARN

* Job scheduling
* Resource allocation

---
