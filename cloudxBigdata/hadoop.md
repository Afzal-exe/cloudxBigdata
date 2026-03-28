# LAB: Install and Configure Hadoop (Single Node) on CentOS (Modern Systems)

---

# Lab 1 — Objective

## Goal

Set up a working Hadoop single-node cluster:

* HDFS (storage)
* YARN (resource management)

---

## Critical Constraints

* Hadoop 3.3.x **does NOT support Java 17+**
* CentOS Stream **does NOT provide Java 8/11 via dnf reliably**
* Therefore:

> You MUST use manually installed Java 11

---

# Lab 2 — System Preparation

## Step 1: Update system

```bash
sudo dnf update -y
```

**Explanation:**
Ensures system packages and metadata are up to date.

---

## Step 2: Install required tools

```bash
sudo dnf install -y wget curl vim net-tools openssh-server openssh-clients
```

**Explanation:**
Provides tools for downloading, editing, networking, and SSH.

---

## Step 3: Enable SSH

```bash
sudo systemctl enable sshd
sudo systemctl start sshd
```

**Explanation:**
Hadoop internally uses SSH to start services.

---

# Lab 3 — Install Java 11 (Stable Method)

## Step 1: Download (version-pinned, reliable)

```bash
cd /opt
sudo wget https://github.com/adoptium/temurin11-binaries/releases/download/jdk-11.0.24%2B8/OpenJDK11U-jdk_x64_linux_hotspot_11.0.24_8.tar.gz
```

---

## Step 2: Extract

```bash
sudo tar -xzf OpenJDK11U-jdk_x64_linux_hotspot_11.0.24_8.tar.gz
```

Verify:

```bash
ls /opt
```

Expected:

```text
jdk-11.0.24+8
```

---

## Step 3: Configure Java globally

```bash
sudo vim /etc/profile.d/java.sh
```

Add:

```bash
export JAVA_HOME=/opt/jdk-11.0.24+8
export PATH=$JAVA_HOME/bin:$PATH
```

Apply:

```bash
source /etc/profile.d/java.sh
```

---

## Step 4: Verify Java

```bash
java -version
```

Expected:

```text
openjdk version "11.0.24"
```

---

# Lab 4 — Create Hadoop User

```bash
sudo useradd hadoop
sudo passwd hadoop
sudo usermod -aG wheel hadoop
su - hadoop
```

**Explanation:**
Runs Hadoop in isolation to avoid permission issues.

---

# Lab 5 — Configure SSH (Fixed Version)

## Step 1: Create SSH directory

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

---

## Step 2: Generate key (do not interrupt)

```bash
ssh-keygen -t rsa -P ""
```

Press Enter for default location.

---

## Step 3: Enable passwordless SSH

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

---

## Step 4: Fix ownership

```bash
chown -R hadoop:hadoop ~/.ssh
```

---

## Step 5: Test

```bash
ssh localhost
```

Expected:

* No password prompt

---

# Lab 6 — Install Hadoop

## Step 1: Download Hadoop

```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
```

---

## Step 2: Extract

```bash
tar -xzf hadoop-3.3.6.tar.gz
mv hadoop-3.3.6 hadoop
```

---

## Step 3: Set environment variables

```bash
vim ~/.bashrc
```

Add:

```bash
export HADOOP_HOME=/home/hadoop/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

Apply:

```bash
source ~/.bashrc
```

---

## Step 4: Verify

```bash
hadoop version
```

---

# Lab 7 — Configure Hadoop

```bash
cd $HADOOP_HOME/etc/hadoop
```

---

## Step 1: Set Java in Hadoop

```bash
vim hadoop-env.sh
```

Set:

```bash
export JAVA_HOME=/opt/jdk-11.0.24+8
```

---

## Step 2: core-site.xml

```bash
cat <<EOF > core-site.xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
EOF
```

---

## Step 3: hdfs-site.xml

```bash
cat <<EOF > hdfs-site.xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.name.dir</name>
    <value>file:///home/hadoop/hdfs/namenode</value>
  </property>
  <property>
    <name>dfs.data.dir</name>
    <value>file:///home/hadoop/hdfs/datanode</value>
  </property>
</configuration>
EOF
```

Create directories:

```bash
mkdir -p ~/hdfs/namenode
mkdir -p ~/hdfs/datanode
```

---

## Step 4: mapred-site.xml

```bash
cp mapred-site.xml.template mapred-site.xml

cat <<EOF > mapred-site.xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
EOF
```

---

## Step 5: yarn-site.xml

```bash
cat <<EOF > yarn-site.xml
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
</configuration>
EOF
```

---

# Lab 8 — Initialize Hadoop

## Step 1: Clean old state

```bash
rm -rf ~/hdfs/namenode/*
rm -rf ~/hdfs/datanode/*
```

---

## Step 2: Format NameNode

```bash
hdfs namenode -format
```

---

# Lab 9 — Start Hadoop

```bash
start-dfs.sh
start-yarn.sh
```

---

# Lab 10 — Validation

## Step 1: Check processes

```bash
jps
```

Expected:

* NameNode
* DataNode
* ResourceManager
* NodeManager

---

## Step 2: Test HDFS

```bash
hdfs dfs -mkdir /test
hdfs dfs -ls /
```

---

## Step 3: Run sample job

```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar pi 2 5
```

---

## Step 4: Web UI

* [http://localhost:9870](http://localhost:9870)
* [http://localhost:8088](http://localhost:8088)

---

# Lab 11 — Stop Hadoop

```bash
stop-yarn.sh
stop-dfs.sh
```
---
