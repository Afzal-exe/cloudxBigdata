# Lab 1 — Lab Overview & Environment Setup

## Objective

Prepare the system and ensure it is ready for Hadoop installation.

---

## What you will do

* Update system packages
* Install required tools
* Verify system readiness

---

## Steps

### 1. Update system

```bash
sudo dnf update -y
```

**Explanation:**
Ensures all system packages are current and avoids dependency conflicts later.

---

### 2. Install required utilities

```bash
sudo dnf install -y wget curl vim net-tools
```

**Explanation:**
These tools are required for downloading files, editing configs, and troubleshooting.

---

## Validation

Run:

```bash
wget --version
```

**Expected:** Command should return version details.

---

# Lab 2 — Install and Configure Java

## Objective

Install Java and configure environment variables required by Hadoop.

---

## Steps

### 1. Install Java

```bash
sudo dnf install -y java-11-openjdk-devel
```

**Explanation:**
Hadoop depends on Java. Without it, Hadoop will not start.

---

### 2. Verify Java

```bash
java -version
```

**Expected:** OpenJDK 1.8 output.

---

### 3. Configure JAVA_HOME

Find path:

```bash
readlink -f $(which java)
```

Set variable:

```bash
sudo vim /etc/profile.d/java.sh
```

Add:

```bash
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
export PATH=$PATH:$JAVA_HOME/bin
```

Apply:

```bash
source /etc/profile.d/java.sh
```

**Explanation:**
JAVA_HOME is required by Hadoop scripts to locate Java.

---

## Validation

```bash
echo $JAVA_HOME
```

---

# Lab 3 — Create Hadoop User

## Objective

Create a dedicated user to run Hadoop services.

---

## Steps

### 1. Create user

```bash
sudo useradd hadoop
sudo passwd hadoop
```

---

### 2. Grant sudo access (optional)

```bash
sudo usermod -aG wheel hadoop
```

---

### 3. Switch user

```bash
su - hadoop
```

**Explanation:**
Running Hadoop as a separate user prevents permission issues and improves isolation.

---

## Validation

```bash
whoami
```

**Expected:** hadoop

---

# Lab 4 — Configure SSH

## Objective

Enable passwordless SSH required by Hadoop.

---

## Steps

### 1. Install SSH

```bash
sudo dnf install -y openssh-server openssh-clients
```

---

### 2. Start SSH service

```bash
sudo systemctl enable sshd
sudo systemctl start sshd
```

---

### 3. Configure passwordless SSH

```bash
ssh-keygen -t rsa -P ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

---

### 4. Test SSH

```bash
ssh localhost
```

**Explanation:**
Hadoop internally uses SSH to start and manage services.

---

## Validation

Login should occur without password prompt.

---

# Lab 5 — Download and Set Hadoop

## Objective

Download Hadoop and configure environment variables.

---

## Steps

### 1. Download Hadoop

```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
```

---

### 2. Extract Hadoop

```bash
tar -xzf hadoop-3.3.6.tar.gz
mv hadoop-3.3.6 hadoop
```

---

### 3. Configure environment variables

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

**Explanation:**
This allows Hadoop commands to be executed globally.

---

## Validation

```bash
hadoop version
```

---

# Lab 6 — Configure Hadoop Core Files

## Objective

Configure Hadoop services (HDFS and YARN).

---

## Steps

Navigate:

```bash
cd $HADOOP_HOME/etc/hadoop
```

---

### 1. Configure hadoop-env.sh

```bash
vim hadoop-env.sh
```

Set:

```bash
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
```

---

### 2. Configure core-site.xml

```bash
vim core-site.xml
```

Add:

```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
  </property>
</configuration>
```

---

### 3. Configure hdfs-site.xml

```bash
vim hdfs-site.xml
```

Add:

```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
</configuration>
```

---

**Explanation:**
These files define how Hadoop storage and services operate.

---

## Validation

No syntax errors in XML files.

---

# Lab 7 — Initialize and Start Hadoop

## Objective

Format HDFS and start Hadoop services.

---

## Steps

### 1. Format NameNode

```bash
hdfs namenode -format
```

**Explanation:**
Initializes HDFS metadata. Must be done only once.

---

### 2. Start services

```bash
start-dfs.sh
start-yarn.sh
```

---

## Validation

```bash
jps
```

**Expected processes:**

* NameNode
* DataNode
* ResourceManager
* NodeManager

---

# Lab 8 — Test and Verify Hadoop

## Objective

Validate full Hadoop functionality.

---

## Steps

### 1. Test HDFS

```bash
hdfs dfs -mkdir /test
hdfs dfs -ls /
```

---

### 2. Run sample job

```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar pi 2 5
```

---

### 3. Access Web UI

* HDFS: [http://localhost:9870](http://localhost:9870)
* YARN: [http://localhost:8088](http://localhost:8088)

---

**Explanation:**
These checks confirm storage, processing, and resource management are working.

---

# Lab 9 — Stop Services

## Objective

Safely stop Hadoop services.

---

## Steps

```bash
stop-yarn.sh
stop-dfs.sh
```

---

## Explanation

Stops all running Hadoop daemons and frees system resources.
