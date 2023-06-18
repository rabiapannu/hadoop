# Hadoop

A guide to download and install Hadoop on Ubuntu 18.04.6

## Step 1: Update and Upgrade

Update your system with the following commands:
sudo apt-get update
sudo apt-get upgrade -y

## Step 2: Install Java

Install java with the following command:
sudo apt install default-jdk

Check if java has been installed successfully:
java -version

## Step 3: Install Apache Hadoop

Download the latest version of Apache Hadoop from their [official page](https://hadoop.apache.org/releases.html).
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.3.5/hadoop-3.3.5.tar.gz

Extract the downloaded file:
tar -xvzf hadoop-3.3.5.tar.gz

## Step 4: Configure Hadoop

### 4a: Configure Hadoop environment variables (bashrc)

Check out the location of “JAVA_HOME” variable:
dirname $(dirname $(readlink -f $(which java)))

sudo nano ~/.bashrc

Add the following lines and close the file:
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export HADOOP_HOME=~/hadoop-3.3.5
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"

Activate the environment variables:
ource ~/.bashrc

### 4b: Edit hadoop-env.sh File

sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh

Uncomment the **$JAVA_HOME** variable:
JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64

Make sure this matches the location of the Java installation on your system.
which javac
readlink -f /usr/bin/javac

### 4c: Edit core-site.xml File

sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml

Write the following in the file and close it:
<configuration>
 <property>
   <name>fs.defaultFS</name>
   <value>hdfs://localhost:9000</value>
 </property>
</configuration>

### 4d: Edit hdfs-site.xml File

sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml

Write the following in the file and close it:
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.name.dir</name>
    <value>file:///home/pannu/hadoopdata/hdfs/namenode</value>
  </property>
  <property>
    <name>dfs.data.dir</name>
    <value>file:///home/pannu/hadoopdata/hdfs/datanode</value>
  </property>
</configuration>

### 4e: Edit mapred-site.xml File

sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml

Write the following in the file and close it:
<configuration> 
  <property> 
    <name>mapreduce.framework.name</name> 
    <value>yarn</value> 
  </property>
  <property>
    <name>yarn.app.mapreduce.am.env</name>
    <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
  </property>
  <property>
    <name>mapreduce.map.env</name>
    <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
  </property>
  <property>
    <name>mapreduce.reduce.env</name>
    <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
  </property>
</configuration>

### 4f: Edit yarn-site.xml File

sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml

Write the following in the file and close it:
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
</configuration>

### 4g: Format HDFS NameNode

hdfs namenode -format

## Step 5: Start Hadoop Cluster

Start the NameNode and DataNode.
start-dfs.sh

Start the YARN resource and node managers.
start-yarn.sh

Verify all the running components:
jps

## Step 6: Access Hadoop UI from Browser

The default port number 9870 gives you access to the Hadoop NameNode UI:
http://localhost:9870/

The default port 9864 is used to access individual DataNodes directly from your browser:
http://localhost:9864/

The YARN Resource Manager is accessible on port 8088:
http://localhost:8088/

## Step 6: Run the sample wordcount example:

### 6a: Place the input file in Hadoop file system

Create the input directory in hdfs
hadoop fs -mkdir /test

Put the data.txt (your sample input file) in the hadoop file system:
hadoop fs -put /home/pannu/Desktop/data.txt /test

Verify the contents of hadoop file system:
hadoop fs -ls /test
hadoop dfs -cat /test/data.txt

### 6b: Run the sample wordcount example:

Run the example program:
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.5.jar wordcount /test/data.txt /test/out

Wait for the program to run, then view the output:
hadoop fs -cat /test/out/*

You can also view the output by browsing the file system through your browser.
