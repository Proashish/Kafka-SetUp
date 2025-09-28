Got it bro ✅
I’ll prepare a clean **README.md** style setup guide for Kafka on AWS EC2 with your exact steps. You can drop it inside your project so that anyone can reproduce the setup.

# 🚀 Kafka on AWS EC2 Setup Guide

This guide explains how to install and configure **Apache Kafka 3.9.1** on an AWS EC2 instance and connect it with a Spring Boot application.

---

## ✅ 1. Install Java (OpenJDK 17)

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
java -version
```

---

## ✅ 2. Download and Extract Kafka

```bash
cd /opt
sudo wget https://dlcdn.apache.org/kafka/3.9.1/kafka_2.12-3.9.1.tgz
sudo tar -xvzf kafka_2.12-3.9.1.tgz
sudo mv kafka_2.12-3.9.1 kafka
```

---

## ✅ 3. Fix Permissions

> Replace `ubuntu` with your actual EC2 username if different.

```bash
sudo chown -R ubuntu:ubuntu /opt/kafka
```

---

## ✅ 4. Start Zookeeper

Open **Terminal 1**:

```bash
cd /opt/kafka
bin/zookeeper-server-start.sh config/zookeeper.properties
```

---

## ✅ 5. Start Kafka Server

Open **Terminal 2**:

```bash
cd /opt/kafka
bin/kafka-server-start.sh config/server.properties
```

---

## ✅ 6. Test Kafka

Open **Terminal 3**:

```bash
# Create a Topic
cd /opt/kafka
bin/kafka-topics.sh --create --topic send-email --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```

---

## ✅ 7. Configure Kafka for Remote Access

Edit the `server.properties` file:

```bash
sudo vim /opt/kafka/config/server.properties
```

Update/add these lines (replace with your EC2 **Public DNS**):

```properties
listeners=PLAINTEXT://0.0.0.0:9092
advertised.listeners=PLAINTEXT://ec2-13-232-203-118.ap-south-1.compute.amazonaws.com:9092
```

Save and exit (`ESC + :wq`), then restart Kafka.

---

## ✅ 8. AWS Security Group Rules

In AWS EC2 **Security Group**:

* Allow inbound traffic on **port 9092 (TCP)** from your client IP (or `0.0.0.0/0` for testing).
* Allow inbound traffic on **port 2181 (TCP)** if Zookeeper needs to be accessed externally (optional).

---

## ✅ 9. Spring Boot Configuration

Update your `application.properties`:

```properties
spring.kafka.bootstrap-servers=ec2-13-232-203-118.ap-south-1.compute.amazonaws.com:9092
spring.kafka.consumer.group-id=notification-group
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.springframework.kafka.support.serializer.JsonSerializer
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.springframework.kafka.support.serializer.JsonDeserializer
spring.kafka.consumer.properties.spring.json.trusted.packages=*
```

---

## ✅ 10. Verify Setup

* Produce a message:

```bash
bin/kafka-console-producer.sh --broker-list ec2-13-232-203-118.ap-south-1.compute.amazonaws.com:9092 --topic send-email
```

* Consume messages:

```bash
bin/kafka-console-consumer.sh --bootstrap-server ec2-13-232-203-118.ap-south-1.compute.amazonaws.com:9092 --topic send-email --from-beginning
```

---

🎉 **Kafka is now running on AWS EC2 and ready to integrate with your Spring Boot app.**

Do you also want me to add **systemd service setup** (so Kafka and Zookeeper auto-start when EC2 reboots)?

