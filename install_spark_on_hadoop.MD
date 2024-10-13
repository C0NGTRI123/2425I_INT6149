# Install Spark on Hadoop

## Before you start
- Make sure you have Hadoop installed and running. If not, follow the instructions in [this guide](install_hadoop.MD) to install Hadoop.
- Run ```jps``` on each of nodes and confirm that HDFS and YARN are running. If they are not, start the services using the following commands:
    ```bash
    start-dfs.sh
    start-yarn.sh
    ```

# Download and extract Spark
- Download the latest version of Spark from the [official website](https://spark.apache.org/downloads.html).
```bash
wget https://dlcdn.apache.org/spark/spark-3.5.3/spark-3.5.3-bin-hadoop3.tgz
```

- Extract the downloaded file.
```bash
tar -xvzf spark-3.5.3-bin-hadoop3.tgz
```

- Move the extracted folder to the desired location.
```bash
mv spark-2.2.0-bin-hadoop2.7 spark
```

- Add the Spark binaries to your ```PATH``` environment variable. \
```File: /home/hduser/.profile```
```
PATH=/home/hduser/spark/bin:$PATH
```

# Integrate Spark with YARN
To communicate with the Hadoop cluster, Spark needs to be configured to use YARN as the resource manager. This can be done by editing the ```spark-defaults.conf``` file. And step by step guide is given below.
1. Edit the hadoop user profile
```File: /home/hduser/.profile```
```
export HADOOP_CONF_DIR=/home/hduser/hadoop/etc/hadoop
export SPARK_HOME=/home/hduser/spark
export LD_LIBRARY_PATH=/home/hduser/hadoop/lib/native:$LD_LIBRARY_PATH
```
2. Restart your session by logging out and logging in again.
3. Rename the spark default template config file:
```bash
mv $SPARK_HOME/conf/spark-defaults.conf.template $SPARK_HOME/conf/spark-defaults.conf
```
4. Edit the spark-defaults.conf file:
```File: $SPARK_HOME/conf/spark-defaults.conf```
```bash
spark.master    yarn
spark.driver.memory    512m
spark.yarn.am.memory    512m
spark.executor.memory          512m
spark.eventLog.enabled  true
spark.eventLog.dir hdfs://nodemaster:9000/spark-logs
spark.history.provider            org.apache.spark.deploy.history.FsHistoryProvider
spark.history.fs.logDirectory     hdfs://nodemaster:9000/spark-logs
spark.history.fs.update.interval  10s
spark.history.ui.port             18080
```

# Run the Spark shell
Work with examples and test your Spark installation by running the Spark shell.
1. Put some data into HDFS:
```bash
cd /home/hadoop
wget -O alice.txt https://www.gutenberg.org/files/11/11-0.txt
hdfs dfs -mkdir inputs
hdfs dfs -put alice.txt inputs
```

2. Run the Spark shell:
```bash
spark-shell

var input = spark.read.textFile("inputs/alice.txt")
// Count the number of non blank lines
input.filter(line => line.length()>0).count()
```
