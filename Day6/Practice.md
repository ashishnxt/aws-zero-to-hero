# Deploying a Node.js Todo Application Using Amazon ECR & ECS (Fargate)

## Project Overview

This project demonstrates how to **containerize a Node.js Todo application**, push the Docker image to **Amazon ECR**, deploy it using **Amazon ECS with Fargate**, and **monitor logs using Amazon CloudWatch** for troubleshooting.

<img width="3195" height="1999" alt="Screenshot 2026-01-17 155157" src="https://github.com/user-attachments/assets/1d69c966-29e1-47e9-8832-52da186183d7" />

---

## 1. Setup Amazon ECR (Elastic Container Registry)

<img width="3199" height="1999" alt="image" src="https://github.com/user-attachments/assets/695a7169-ced8-41cc-bb4c-97e5b1347285" />

1. Create an ECR repository
   **Repository name:** `node-todo-app`

2. Build and tag the Docker image:

   ```bash
   node-todo-app:latest
   ```

3. Push the Docker image to the ECR repository.

---

## 2. Setup EC2 (For Build & Push Operations)

<img width="3199" height="1999" alt="Screenshot 2026-01-17 145829" src="https://github.com/user-attachments/assets/69bebcf0-d3bb-4b68-b14e-c4d73a76fade" />

### 2.1 Create an EC2 Instance

* Launch an EC2 instance (Amazon Linux preferred)

### 2.2 Install Required Tools

Install the following packages:

* AWS CLI v2
* Docker

### 2.3 Application Setup

1. Clone the Node.js Todo application repository:

   ```bash
   git clone <todo-app-repo-url>
   ```

2. Configure AWS CLI:

   * Create an IAM user
   * Attach **ECR** and **ECS** policies
   * Configure AWS CLI using access keys

3. Build the Docker image:

   ```bash
   docker build -t node-todo-app:latest .
   ```

4. Authenticate Docker with ECR and push the image to the ECR registry.

---

## 3. Setup Amazon ECS (Elastic Container Service)

### 3.1 Create ECS Cluster

* **Cluster name:** `elastic-rabbit-gj6qi6`
* **Infrastructure:** AWS Fargate

---

## 4. ECS Task Definition

<img width="3199" height="1999" alt="Screenshot 2026-01-17 145614" src="https://github.com/user-attachments/assets/366fb534-44bc-4be1-82bd-708f4e383be4" />

<img width="3199" height="1999" alt="Screenshot 2026-01-17 145650" src="https://github.com/user-attachments/assets/b9f0804b-680f-4a8d-970a-3e81f36978de" />

A **Task Definition** is similar to a `docker run` command.
It defines how a container should run in ECS, including image, ports, environment variables, resources, and logging.

### 4.1 Task Definition Configuration

* **Launch type:** AWS Fargate
* **OS architecture:** Linux / x86_64
* **Task size:** 1 vCPU

### 4.2 IAM Task Role

<img width="3199" height="1999" alt="Screenshot 2026-01-17 160650" src="https://github.com/user-attachments/assets/eced309c-9a28-4f13-b7b9-3cc55c284b68" />

<img width="3195" height="1988" alt="Screenshot 2026-01-17 160727" src="https://github.com/user-attachments/assets/de574d2e-2705-4923-8a4a-33d5b2236359" />

1. Create an IAM role
   **Role name:** `ecsTaskExecution`

2. Attach policies:

* `AmazonECSTaskExecutionRolePolicy`
* `AmazonEC2ContainerServiceFullAccess`

3. Assign this role as the **Task Role**.

---

### 4.3 Container Configuration


<img width="3194" height="1999" alt="Screenshot 2026-01-17 150038" src="https://github.com/user-attachments/assets/5d9b2883-6e28-461c-9a6a-f2d1f1f31bbd" />

<img width="3199" height="1999" alt="Screenshot 2026-01-17 150054" src="https://github.com/user-attachments/assets/218bf4c8-2e43-4c5b-b676-73b7fe2e9724" />

* **Container name:** `Notes-todo`
* **Image:** ECR image URI (`notes-todo_image_url`)
* **Port mapping:**

  * Container port: `8000`
  * Protocol: TCP

#### Logging Configuration (CloudWatch)

* Enable **AWS Logs (CloudWatch Logs)**
* Log group format:

  ```
  /ecs/Notes-todo
  ```
* Log stream:

  ```
  ecs/Notes-todo
  ```

This ensures all container logs are sent to CloudWatch.

Create and register the task definition.

---

## 5. Run Task in ECS Cluster

<img width="3199" height="1999" alt="Screenshot 2026-01-17 150259" src="https://github.com/user-attachments/assets/eccf52ab-003f-47ee-b599-8339e037cb19" />

<img width="3199" height="1999" alt="Screenshot 2026-01-17 150406" src="https://github.com/user-attachments/assets/167cc620-f84c-4d4d-a599-6b4a01fd6777" />

<img width="3187" height="1999" alt="Screenshot 2026-01-17 153401" src="https://github.com/user-attachments/assets/2afc92e5-a949-4bf9-a8a3-165e1c2ee78e" />

<img width="3198" height="1993" alt="Screenshot 2026-01-17 154930" src="https://github.com/user-attachments/assets/efc0c3df-e781-4d93-b3ca-c3256a163a70" />

1. Navigate to the ECS cluster: `elastic-rabbit-gj6qi6`
2. Run the task using the created task definition
3. Select:

   * VPC
   * Subnet
   * Security Group

---

<img width="3145" height="1999" alt="Screenshot 2026-01-17 154945" src="https://github.com/user-attachments/assets/0836b3ce-194a-4118-a00b-33c1fceafd4b" />


## 6. Security Group Configuration

<img width="3190" height="1986" alt="Screenshot 2026-01-17 155259" src="https://github.com/user-attachments/assets/14cb07c8-4dc3-4ee5-8e24-69eca4f45376" />

Add an **inbound rule** to the ECS task security group:

* **Port:** `8000`
* **Protocol:** TCP
* **Source:** `0.0.0.0/0` (restrict in production)

---

## 7. Monitoring & Troubleshooting (CloudWatch Logs)

<img width="3187" height="1982" alt="Screenshot 2026-01-17 155501" src="https://github.com/user-attachments/assets/81c9294c-e605-42c6-8b22-ba50dd814bec" />

<img width="3176" height="1982" alt="Screenshot 2026-01-17 155429" src="https://github.com/user-attachments/assets/09250594-4bb9-4ca0-ac4a-d5080f1145a7" />

<img width="3188" height="1980" alt="Screenshot 2026-01-17 155445" src="https://github.com/user-attachments/assets/b81aee93-83b8-4f9b-9cba-ab2a9e1e9a3f" />

* Verify that **all logs are present in CloudWatch Log Manager**
* Check logs related to:

  * ECS **Cluster**
  * Running **Tasks**
  * Application **Containers**

CloudWatch logs help in:

* Debugging application errors
* Identifying container crashes
* Monitoring startup and runtime issues
* Troubleshooting ECS task failures

