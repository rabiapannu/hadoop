# Hadoop

A guide to download and install Hadoop on Ubuntu 18.04.6

## Step 1: Update and Upgrade

Update your system with the following commands:<br/>
sudo apt-get update<br/>
sudo apt-get upgrade -y<br/>

## Step 2: Install Java

Install java with the following command:<br/>
sudo apt install default-jdk<br/>

Check if java has been installed successfully:<br/>
java -version<br/>

## Step 3: Install Apache Hadoop

Download the latest version of Apache Hadoop from their [official page](https://hadoop.apache.org/releases.html).<br/>
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.3.5/hadoop-3.3.5.tar.gz<br/>

Extract the downloaded file:<br/>
tar -xvzf hadoop-3.3.5.tar.gz<br/>

## Step 4: Configure Hadoop

### 4a: Configure Hadoop environment variables (bashrc)

Check out the location of “JAVA_HOME” variable:<br/>
dirname $(dirname $(readlink -f $(which java)))<br/>

sudo nano ~/.bashrc<br/>

Add the following lines and close the file:<br/>
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64<br/>
export HADOOP_HOME=~/hadoop-3.3.5<br/>
export HADOOP_INSTALL=$HADOOP_HOME<br/>
export HADOOP_MAPRED_HOME=$HADOOP_HOME<br/>
export HADOOP_COMMON_HOME=$HADOOP_HOME<br/>
export HADOOP_HDFS_HOME=$HADOOP_HOME<br/>
export YARN_HOME=$HADOOP_HOME<br/>
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native<br/>
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin<br/>
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"<br/><br/>

Activate the environment variables:<br/>
source ~/.bashrc<br/>

### 4b: Edit hadoop-env.sh File

sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh<br/>

Uncomment the **$JAVA_HOME** variable:<br/>
JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64<br/>

Make sure this matches the location of the Java installation on your system.<br/>
which javac<br/>
readlink -f /usr/bin/javac<br/>

### 4c: Edit core-site.xml File

sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml<br/>

Write the following in the file and close it:<br/>
```xml
<configuration>
 <property>
   <name>fs.defaultFS</name>
   <value>hdfs://localhost:9000</value>
 </property>
</configuration>
```
### 4d: Edit hdfs-site.xml File

sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml<br/>

Write the following in the file and close it:<br/>
```xml
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
```

### 4e: Edit mapred-site.xml File

sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml<br/>

Write the following in the file and close it:<br/>
```xml
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
```

### 4f: Edit yarn-site.xml File

sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml<br/>

Write the following in the file and close it:<br/>
```xml
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
</configuration>
```

### 4g: Format HDFS NameNode

hdfs namenode -format<br/>

## Step 5: Start Hadoop Cluster

Start the NameNode and DataNode.<br/>
start-dfs.sh<br/>

Start the YARN resource and node managers.<br/>
start-yarn.sh<br/>

Verify all the running components:<br/>
jps<br/>

## Step 6: Access Hadoop UI from Browser

The default port number 9870 gives you access to the Hadoop NameNode UI:<br/>
http://localhost:9870/<br/>

The default port 9864 is used to access individual DataNodes directly from your browser:<br/>
http://localhost:9864/<br/>

The YARN Resource Manager is accessible on port 8088:<br/>
http://localhost:8088/<br/>

## Step 6: Run the sample wordcount example:

### 6a: Place the input file in Hadoop file system

Create the input directory in hdfs<br/>
hadoop fs -mkdir /test<br/>

Put the data.txt (your sample input file) in the hadoop file system:<br/>
hadoop fs -put /home/pannu/Desktop/data.txt /test<br/>

Verify the contents of hadoop file system:<br/>
hadoop fs -ls /test<br/>
hadoop dfs -cat /test/data.txt<br/>

### 6b: Run the sample wordcount example:

Run the example program:<br/>
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.5.jar wordcount /test/data.txt /test/out<br/>

Wait for the program to run, then view the output:<br/>
hadoop fs -cat /test/out/*<br/>

You can also view the output by browsing the file system through your browser.<br/>
