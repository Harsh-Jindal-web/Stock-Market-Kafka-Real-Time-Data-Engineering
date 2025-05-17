# Stock Market Kafka Real Time Data Engineering Project

## 📘 Introduction

In this project, you'll build an end-to-end real-time data engineering pipeline using **Apache Kafka**, **Amazon EC2**, **S3**, **AWS Glue**, and **Athena**.

You'll stream real-time stock market data using a Kafka Producer, consume it on an EC2-hosted Kafka Consumer, store it as CSV in an S3 bucket, and analyze the data using **AWS Glue Data Catalog** and **Athena SQL queries**.

This project demonstrates how to integrate real-time data streaming with scalable cloud storage and serverless data analytics using AWS.

---

## 🚀 Steps Overview

1. **Launch EC2 Instance**
2. **SSH into EC2 and Install Kafka & Java**
3. **Start ZooKeeper and Kafka**
4. **Configure Kafka to Use EC2 Public IP**
5. **Create Kafka Topic, Producer, and Consumer**
6. **Set Up S3 Bucket**
7. **Create IAM User and Configure AWS CLI**
8. **Send CSV Data via Producer and Store in S3 via Consumer**
9. **Run AWS Glue Crawler**
10. **Query Data in Athena**

---

## Architecture 
<img src="Architecture.jpg">

## Technology Used
- Programming Language - Python
- Amazon Web Service (AWS)
1. S3 (Simple Storage Service)
2. Athena
3. Glue Crawler
4. Glue Catalog
5. EC2
- Apache Kafka

---

## 🔧 EC2 Setup & Kafka Installation

### 📥 Download and Extract Kafka

```bash
wget https://downloads.apache.org/kafka/3.3.1/kafka_2.12-3.3.1.tgz
tar -xvf kafka_2.12-3.3.1.tgz
cd kafka_2.12-3.3.1
```

### ☕ Install Java

```bash
sudo yum install java-1.8.0-openjdk
java -version
```

---

## ⚙️ Start ZooKeeper and Kafka

### Start ZooKeeper

```bash
bin/zookeeper-server-start.sh config/zookeeper.properties
```

### Start Kafka (in a new SSH session)

```bash
export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"
bin/kafka-server-start.sh config/server.properties
```

If Kafka binds to the private IP, stop the server and edit:

```bash
sudo nano config/server.properties
```

Change:

```properties
advertised.listeners=PLAINTEXT://<Your EC2 Public IP>:9092
```

Then restart ZooKeeper and Kafka.

---

## 🔐 Security Group Configuration

1. Go to AWS Console
2. Edit your EC2 instance's security group
3. Add an **inbound rule** to allow traffic on port **9092** from your IP (or a broader range for testing)

---

## 🧪 Kafka Topic, Producer, Consumer

### Create Topic

```bash
bin/kafka-topics.sh --create --topic demo_testing2 --bootstrap-server <EC2_Public_IP>:9092 --replication-factor 1 --partitions 1
```

### Start Producer

```bash
bin/kafka-console-producer.sh --topic demo_testing2 --bootstrap-server <EC2_Public_IP>:9092
```

### Start Consumer

```bash
bin/kafka-console-consumer.sh --topic demo_testing2 --bootstrap-server <EC2_Public_IP>:9092 --from-beginning
```

---

## ☁️ S3 Integration

1. Create an **S3 bucket** via the AWS Console
2. Create an **IAM User** with programmatic access and permissions for S3
3. Configure AWS CLI:

```bash
aws configure
```

4. Modify the Kafka consumer script to upload received data to S3 using `boto3` or `awscli`.

---

## 🔍 AWS Glue & Athena

### AWS Glue Crawler

1. Create a new Glue Crawler
2. Set the source as your S3 bucket
📘 **Note**: The AWS Glue Crawler populates the Glue Data Catalog, which Athena uses to query the structured data stored in S3.
3. Set the destination database
4. Run the crawler to populate schema

### Query in Athena

1. Go to **Athena Console**
2. Select the database
3. Query the table created by Glue Crawler

---

## ✅ Final Test

- ✅ Send CSV data via Kafka Producer
- ✅ Consumer processes and uploads data to S3
- ✅ Glue Crawler detects new schema
- ✅ Query the data from Athena

---

## 📌 Notes

- Make sure Kafka port `9092` is open in the EC2 security group
- Avoid exposing EC2 to the public for long durations
- Rotate IAM credentials regularly for security

---

## 📎 Optional

Let me know if you’d like to include:
- Automation scripts
- Kafka consumer Python code to upload to S3
