# AWS Basics Project: EC2, RDS, DynamoDB & Lambda

## 📌 Overview

This project demonstrates the basic usage and integration of **AWS EC2, RDS, DynamoDB, IAM, and Lambda** services. It covers both **relational (SQL)** and **non-relational (NoSQL)** databases, along with **serverless computing** using AWS Lambda.

---

## 🛠️ Services Used

* **Amazon EC2**
* **Amazon RDS (MySQL)**
* **Amazon DynamoDB**
* **AWS Lambda**
* **AWS IAM**
* **Amazon CloudWatch**

---

## 1️⃣ Amazon RDS (Relational Database Service)

### Description

Amazon RDS is a **managed relational database service**.
In this project:

* **MySQL** is used as the database engine
* Data is stored in **table format (rows & columns)**

### Steps

1. Create an **RDS instance**
2. Select **MySQL**
3. Configure database name, username, and password
4. Enable public access (for learning/testing)
5. Set security group rules to allow MySQL (port **3306**)

---

## 2️⃣ Amazon EC2 Setup

### Description

EC2 is used to connect and interact with the RDS database.

### Configuration

* Instance type: **t2.micro**
* OS: Amazon Linux
* Security group:

  * Allow **SSH (22)**
  * Allow **MySQL (3306)**

### Connect EC2 to RDS

1. SSH into EC2 instance
2. Install MySQL client:

   ```bash
   sudo yum install mysql -y
   ```
3. Connect to RDS:

   ```bash
   mysql -h <RDS-ENDPOINT> -u <USERNAME> -p
   ```

---

## 3️⃣ IAM (Identity and Access Management)

### EC2 IAM Role

Create an IAM role and attach it to the EC2 instance with permissions:

* **AmazonRDSFullAccess**
* **CloudWatchFullAccess**

This allows EC2 to securely interact with AWS services.

---

## 4️⃣ Amazon DynamoDB (NoSQL Database)

### Description

DynamoDB is a **NoSQL database**.

* Data is stored in **key–value pairs**
* Schema-less
* Highly scalable

### Table Setup

* Partition key: `id`
* Data format: JSON

---

## 5️⃣ AWS Lambda (Serverless Computing)

### Description

AWS Lambda is a **serverless service**, meaning:

* No server management required
* Runs on AWS-managed infrastructure
* Executes code only when triggered

Lambda is used **instead of EC2** to interact with DynamoDB.

---

## 6️⃣ Lambda IAM Role

### Problem

While accessing DynamoDB from Lambda, an **authorization error** occurred.

### Solution

Create a Lambda execution role with:

* **AmazonDynamoDBFullAccess**
* **CloudWatchFullAccess**

Attach this role to the Lambda function.

---

## 7️⃣ Lambda Function Creation

### Function Details

* Runtime: **Python**
* Function name: `test`
* Purpose: Access DynamoDB table

---

## 8️⃣ Lambda Event Trigger

An event is configured to trigger the Lambda function.
(This can be API Gateway, test event, or manual trigger.)

---

## 9️⃣ Lambda Functions Code

### 🔹 Insert Data into DynamoDB

```
import json
import boto3

def lambda_handler(event, context):
    print(event["message"])
#connect the db
    dynamodb = boto3.resource("dynamodb")

# table access from db
    table = dynamodb.Table("learners")


    item_to_insert = {
    "learner_id": event["learner_id"],
    "learner_name": event["learner_name"],
    "learner_age": event["learner_age"] ,
    "learner_course": event["learner_course"]
     }

    try:
        response = table.put_item(Item=item_to_insert)

        return {
        "statusCode": 200,
        "body": json.dumps('item hase sucessfully added')
        }
    except Exception as e:
        return{
        "statusCode": 500,
        "body": json.dumps(str(e))
        }


```

---

### 🔹 Delete Data from DynamoDB

```
import json
import boto3

def lambda_handler(event, context):

#connect the db
    dynamodb = boto3.resource("dynamodb")

# table access from db
    table = dynamodb.Table("learners")


    key_to_delete = {
    "learner_id": event["learner_id"]
    
     }

    try:
        response = table.delete_item(Key=key_to_delete)

        return {
        "statusCode": 200,
        "body": json.dumps('item hase sucessfully deleted')
        }
    except Exception as e:
        return{
        "statusCode": 500,
        "body": json.dumps(str(e))
        }




```

---

## 🔍 Monitoring

* Logs are captured in **Amazon CloudWatch**
* Useful for debugging Lambda execution and permission issues

