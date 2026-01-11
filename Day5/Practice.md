<img width="2783" height="1356" alt="Screenshot 2026-01-11 164010" src="https://github.com/user-attachments/assets/deae1241-7ced-42a6-bc7a-758610fcab6f" /># Chapter 3: AWS Billing, Monitoring, and VPC

## Topic Overview

This chapter covers the fundamentals of **AWS Virtual Private Cloud (VPC)**, subnet design, internet connectivity using **Internet Gateway and NAT Gateway**, VPC peering, and a basic introduction to **AWS Billing and CloudWatch monitoring**.

<img width="2783" height="1356" alt="Screenshot 2026-01-11 164010" src="https://github.com/user-attachments/assets/a6c436f5-254f-4d5f-9887-82dc657ad742" />


---

## 1. VPC (Virtual Private Cloud)

A **VPC** is a logically isolated virtual network in AWS where you can launch AWS resources.

### VPC Creation

* **VPC Name:** `aws-hero-to-zero`
* **CIDR Block:** `10.0.0.0/24`

**Explanation:**

* The CIDR block `10.0.0.0/24` provides **256 total private IP addresses**.
* This CIDR defines the *maximum IP range* available inside the VPC.

---

## 2. Subnet Design (Public & Private)

Subnets are created **inside a VPC** and use a portion of the VPC CIDR block.

### Subnet Division

The `/24` VPC CIDR is divided into **two equal `/25` subnets**:

| Subnet Type | Name        | CIDR Block      |
| ----------- | ----------- | --------------- |
| Public      | aws-public  | `10.0.0.0/25`   |
| Private     | aws-private | `10.0.0.128/25` |

---

## 3. Public Subnet Configuration

### CIDR Block: `10.0.0.0/25`

* IP range: `10.0.0.0 – 10.0.0.127`
* Total IPs: **128**

### AWS Reserved IP Addresses

AWS reserves **5 IP addresses in every subnet**:

| Reserved IP | Address      | Purpose                      |
| ----------- | ------------ | ---------------------------- |
| 1           | `10.0.0.0`   | Network address              |
| 2           | `10.0.0.1`   | VPC router (default gateway) |
| 3           | `10.0.0.2`   | AWS DNS                      |
| 4           | `10.0.0.3`   | Reserved for future use      |
| 5           | `10.0.0.127` | Broadcast (reserved by AWS)  |

**Usable IPs:** `10.0.0.4 – 10.0.0.126` → **123 usable IP addresses**

---

## 4. Private Subnet Configuration

### CIDR Block: `10.0.0.128/25`

* IP range: `10.0.0.128 – 10.0.0.255`
* Total IPs: **128**
* AWS reserved IPs: **5**
* **Usable IPs:** **123**

---

## 5. EC2 Instance Deployment

### EC2 in Public Subnet

* Launch an EC2 instance inside the **aws-public** subnet.

⚠️ **Important Concept:**

* A subnet is **not public by default**.
* A subnet becomes **public only when**:

  * It is associated with a route table that has a route to an **Internet Gateway (IGW)**.

Without an Internet Gateway:

* EC2 instance **cannot access the internet**
* EC2 instance is **not publicly accessible**

---

## 6. Internet Gateway (IGW)

### Create Internet Gateway

* **Name:** `AWS-igw`
* Attach the IGW to the VPC `aws-hero-to-zero`

### Route Table for Public Subnet

* **Route Table Name:** `aws-route-public`

Routes:

* `10.0.0.0/24` → local
* `0.0.0.0/0` → Internet Gateway (AWS-igw)

### Subnet Association

* Associate this route table with **aws-public subnet**

Now the subnet becomes **public and internet-accessible**.

---

## 7. Connecting Private Subnet to Internet using NAT Gateway

### Objective

Allow **private subnet instances** to access the internet **without being publicly accessible**.

### Steps

1. Create a **NAT Gateway** in the **public subnet**

   * Allocate an **Elastic IP**
2. Create a **Private Route Table**
3. Add route:

   * `0.0.0.0/0` → NAT Gateway
4. Associate this route table with the **aws-private subnet**

### Result

* Private subnet instances can:

  * Access the internet (updates, downloads)
  * Communicate with public subnet instances
* Internet **cannot initiate** connections to private instances

---

## 8. Connecting Two Different VPCs (VPC Peering)

### Steps

1. Create a **VPC Peering Connection** between two VPCs
2. Update route tables in **both VPCs**

Example Routes:

* `10.0.0.0/24` → VPC Peering Connection
* `192.0.0.0/16` → VPC Peering Connection

### Security Group Configuration

On both EC2 instances:

* Allow **Inbound ICMP (ping)** from the peer VPC CIDR

Result:

* EC2 instances in different VPCs can **ping and communicate** with each other

---

## 9. AWS Billing and CloudWatch Monitoring

### Billing Overview

AWS Billing helps monitor service usage and costs.

### Task: Billing Alert Setup

**Requirement:**

* Receive an email alert if AWS bill exceeds **$5**

### Steps

1. Enable **Billing Alerts**
2. Use **Amazon CloudWatch** to create a billing alarm
3. Configure **SNS (Simple Notification Service)**
4. Subscribe your email to the SNS topic

### Result

* Email notification is sent when billing threshold exceeds `$5`

---

## Summary

* VPC defines the total IP space
* Subnets divide the VPC CIDR
* Internet Gateway enables public access
* NAT Gateway provides secure outbound internet for private subnets
* VPC Peering connects multiple VPCs
* CloudWatch + SNS helps control AWS billing

---

✅ This setup follows AWS best practices and is commonly used in real-world production environments.
