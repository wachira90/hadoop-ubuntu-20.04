# hadoop-ubuntu-20.04
hadoop-ubuntu-20.04

# Install and Configure Hadoop on Ubuntu 20.04

## Step 1 – Installing Java

````
sudo apt update 
sudo apt install openjdk-11-jdk
````

java -version 
````
openjdk version "11.0.11" 2021-04-20
OpenJDK Runtime Environment (build 11.0.11+9-Ubuntu-0ubuntu2.20.04)
OpenJDK 64-Bit Server VM (build 11.0.11+9-Ubuntu-0ubuntu2.20.04, mixed mode, sharing)
````

## Step 2 – Create a Hadoop User

````
sudo adduser hadoop 
````

## Step 3 – Configure SSH Key-based Authentication
````
su - hadoop 

ssh-keygen -t rsa 
<ENTER>
<ENTER>

cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

chmod 640 ~/.ssh/authorized_keys

ssh localhost

````

## Step 4 – Installing Hadoop

````
su - hadoop 

wget https://downloads.apache.org/hadoop/common/hadoop-3.3.4/hadoop-3.3.4.tar.gz
tar -xvzf hadoop-3.3.4.tar.gz 
mv hadoop-3.3.4 hadoop
````

````
nano ~/.bashrc 


export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export HADOOP_HOME=/home/hadoop/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"


source ~/.bashrc 
````


````
nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh


export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
````

## Step 5 - Configuring Hadoop

````
mkdir -p ~/hadoopdata/hdfs/namenode
mkdir -p ~/hadoopdata/hdfs/datanode
````

nano $HADOOP_HOME/etc/hadoop/core-site.xml

````
<configuration>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://172.16.1.15:9000</value>
        </property>
</configuration>
````


nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml

````
<configuration>
        <property>
                <name>dfs.replication</name>
                <value>1</value>
        </property>
 
        <property>
                <name>dfs.name.dir</name>
                <value>file:///home/hadoop/hadoopdata/hdfs/namenode</value>
        </property>
 
        <property>
                <name>dfs.data.dir</name>
                <value>file:///home/hadoop/hadoopdata/hdfs/datanode</value>
        </property>
</configuration>
````

nano $HADOOP_HOME/etc/hadoop/mapred-site.xml 

````
<configuration>
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
</configuration>
````

nano $HADOOP_HOME/etc/hadoop/yarn-site.xml 

````
<configuration>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
</configuration>
````


## Step 6 - Start Hadoop Cluster

````
hdfs namenode -format
````

## result
````
2020-11-23 10:31:51,318 INFO namenode.NNStorageRetentionManager: Going to retain 1 images with txid >= 0
2020-11-23 10:31:51,323 INFO namenode.FSImage: FSImageSaver clean checkpoint: txid=0 when meet shutdown.
2020-11-23 10:31:51,323 INFO namenode.NameNode: SHUTDOWN_MSG:
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at 172.16.1.15/127.0.1.1
************************************************************/
````


````
start-dfs.sh
hadoop/sbin/start-dfs.sh

start-yarn.sh
hadoop/sbin/start-yarn.sh
````

````
Starting resourcemanager
Starting nodemanagers
````

## check process running
````
jps
````

## output

````
18194 NameNode
18822 NodeManager
17911 SecondaryNameNode
17720 DataNode
18669 ResourceManager
19151 Jps
````



## Step 7 - Adjust Firewall


````
firewall-cmd --permanent --add-port=9870/tcp && firewall-cmd --reload
firewall-cmd --permanent --add-port=8088/tcp && firewall-cmd --reload
````

### or

````

ufw status
ufw enable

ufw allow 9870
ufw allow 8088

````


## Step 8 - Access Hadoop Namenode and Resource Manager

## Namenode

````
http://172.16.1.15:9870
````

## Resource Manage

````
http://172.16.1.15:8088
````

### datanode

````
http://172.16.1.15:9864/
````


## Step 9 - Verify the Hadoop Cluster

````
hdfs dfs -mkdir /test1
hdfs dfs -mkdir /logs


hdfs dfs -ls /


Found 3 items
drwxr-xr-x   - hadoop supergroup          0 2020-11-23 10:56 /logs
drwxr-xr-x   - hadoop supergroup          0 2020-11-23 10:51 /test1


hdfs dfs -put /var/log/* /logs/
````


## Step 10 - Stop Hadoop Cluster

````
hadoop/sbin/stop-dfs.sh

hadoop/sbin/stop-yarn.sh


root@ubt1:~# usermod -a -G adm hadoop

root@ubt1:~# usermod -a -G sudo hadoop
````
