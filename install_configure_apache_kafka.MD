## Install JAVA
1. Update your Ubuntu packages.
```bash
sudo apt-get update
```

2. Install OpenJDK
```bash
sudo apt install openjdk-21-jdk
```

3. Check the Java version
```bash
java -version
```

Java should be return 
```
openjdk 21.0.3 2024-04-16
OpenJDK Runtime Environment (build 21.0.3+9-Ubuntu-1ubuntu1)
OpenJDK 64-Bit Server VM (build 21.0.3+9-Ubuntu-1ubuntu1, mixed mode, sharing)
```

## Install Apache Kafka
1. Download the Apache Kafka binary files from the official website.
```bash
wget https://downloads.apache.org/kafka/3.8.0/kafka_2.12-3.8.0.tgz
```

2. Extract the downloaded file.
```bash
tar -xvzf kafka_2.12-3.8.0.tgz
```

## Run Apache Kafka
1. Configure the Kafka server.
```kafka_2.12-3.8.0/config/server.properties```
```bash
delete.topic.enable = true
```

2. Start the Zookeeper service.
```bash
cd kafka_2.12-3.8.0
bin/zookeeper-server-start.sh config/zookeeper.properties
```

3. Open a new terminal and start the Kafka server.
```bash
cd kafka_2.12-3.8.0
bin/kafka-server-start.sh config/server.properties
```

## Create a Topic
1. Open a new terminal and create a topic.
2. Create a new topic named ```test_events```:
```bash
cd kafka_2.12-3.8.0
bin/kafka-topics.sh --create --topic test-events --bootstrap-server localhost:9092
```
Kafka confirms the topic has been created:
```bash
Created topic test-events.
```

3. Generate a list of all the topics on the cluster with the ```--list``` option:
```bash
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

return result:
```bash
test-events
```

4. Describe the topic with the ```--describe``` option:
```bash
bin/kafka-topics.sh --describe --topic test-events --bootstrap-server localhost:9092
```

## Create systemd service for Apache Kafka
1. Create a system file for Zookeeper called ```/etc/systemd/system/zookeeper.service```.
```bash
sudo nano /etc/systemd/system/zookeeper.service
```

2. Edit the file and add the following content:
```/etc/systemd/system/zookeeper.service```
```bash
[Unit]
Description=Apache Zookeeper Server
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
ExecStart=/home/kafka/kafka_2.13-3.7.0/bin/zookeeper-server-start.sh /home/kafka/kafka_2.13-3.7.0/config/zookeeper.properties
ExecStop=/home/kafka/kafka_2.13-3.7.0/bin/zookeeper-server-stop.sh

Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```

3. Create a system file for Kafka called ```/etc/systemd/system/kafka.service```.
```bash
sudo nano /etc/systemd/system/kafka.service
```

4. Edit the file and add the following content:
```/etc/systemd/system/kafka.service```
```bash
[Unit]
Description=Apache Kafka Server
Requires=zookeeper.service
After=zookeeper.service

[Service]
Type=simple
Environment="JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64"
ExecStart=/home/kafka/kafka_2.13-3.7.0/bin/kafka-server-start.sh /home/kafka/kafka_2.13-3.7.0/config/server.properties
ExecStop=/home/kafka/kafka_2.13-3.7.0/bin/kafka-server-stop.sh

Restart=on-abnormal

[Install]
WantedBy=multi-user.target
```

5. Reload the systemd manager configuration.
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now zookeeper
sudo systemctl enable --now kafka
```

6. Check the status of the Kafka service.
```bash
sudo systemctl status kafka zookeeper
```

7. (Optional) To stop the Kafka service, run:
```bash
sudo systemctl stop kafka zookeeper
```

Remove logs
```bash
sudo rm -rf /tmp/kafka-logs /tmp/zookeeper
```

