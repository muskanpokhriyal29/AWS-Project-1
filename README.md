# AWS-Project-1
# AWS 3-Tier Web Architecture

## Description
It’s a design pattern that splits an application into three main layers/tiers — user interface, functional logic, and data storage. This separation helps improve the scalability, maintainability, and overall management of the web application.

The three tiers are:

### 1. Presentation Tier (Client Tier):
This is the front end — the part users interact with directly. It includes the user interface built using technologies like HTML, CSS, JavaScript, and frameworks like React or Angular.

### 2. Application Tier (Logic/Middle Tier):
This layer handles the business logic and acts as a bridge between the frontend and the database. It processes user requests and coordinates data flow. Server-side technologies used here include Node.js, Python, PHP, and JSON for data exchange.

### 3. Database Tier:
This is the backend layer responsible for storing and retrieving data. It communicates with the application tier to perform data operations. Technologies used include SQL databases like MySQL and NoSQL databases like MongoDB or Cassandra.
![3tier](https://github.com/user-attachments/assets/f9bd2d10-98de-454b-888b-0f3a20285a46)
![3tier](https://github.com/user-attachments/assets/d5953e16-7d88-41a2-b7ec-888964960254)
![Test-App-Server](https://github.com/user-attachments/assets/5240ef35-f23a-4e4b-a404-6e4bdb9a84e0)

In this setup, a public-facing Application Load Balancer handles incoming traffic and routes it to the web tier EC2 instances. These instances run Nginx servers that serve a React.js frontend and forward API requests to an internal load balancer in the application tier. The app tier, built with Node.js, processes the requests, interacts with an MySQL single-AZ database, and sends the data back to the web tier. Each layer is equipped with load balancers, health checks, and autoscaling groups to ensure high availability and smooth scaling.

## Steps:

## Creating a 3-Tier Architecture with AWS Services

### Step 1: Create Two S3 Buckets
  - First, create an S3 bucket to store the code for your web server and app server.
  - Upload the code from your local machine to this S3 bucket.
  - create a second S3 bucket to store VPC flow logs.
![Screenshot 2025-05-29 022714](https://github.com/user-attachments/assets/1549b53d-7b00-4bb8-a52f-6a3438c044a5)

### Step 2: Create IAM Role with Policies
  - S3 Read Only.
  - SSM managed instance core.
    ![Screenshot 2025-05-29 022532](https://github.com/user-attachments/assets/06e2aa05-e923-4fb4-84da-7914ba4695a3)
![Screenshot 2025-05-29 022538](https://github.com/user-attachments/assets/c3e0e623-2e7e-4794-9e2c-23c3608cd6c3)

### Step 3: Create VPC, Subnets, Internet Gateway, NAT Gateway and Route Tables
  - Enable Auto-Assign Public IP for all public subnets in the web tier to allow internet access.
  Create flow logs for the VPC to capture network traffic, use the previously created S3 bucket to store these logs.
![Screenshot 2025-05-29 021154](https://github.com/user-attachments/assets/026c53a9-e0f6-4cb3-91c3-78f6e954d75b)
![Screenshot 2025-05-29 021203](https://github.com/user-attachments/assets/b21056d9-728a-48ce-b410-ab8ddef80157)
![Screenshot 2025-05-29 021341](https://github.com/user-attachments/assets/cd0769e2-83ae-4b5f-aa9e-45947b28fe46)
![Screenshot 2025-05-29 021352](https://github.com/user-attachments/assets/7a84bec2-3d28-4ac6-a831-626040390ce4)
![Screenshot 2025-05-29 021408](https://github.com/user-attachments/assets/2d010930-ceb1-48e2-9ece-8790ab78235f)
![Screenshot 2025-05-29 021417](https://github.com/user-attachments/assets/c66445dd-79b6-4490-9230-77120018adcc)

### Step 4: Create Security Groups
  - External Load Balancer SG of HTTP (80) from 0.0.0.0/0
  - Web Tier SG of HTTP, connects it to External Load Balancer SG
  - Internal Load Balancer SG of HTTP, connects it to Web Tier SG
  - App Tier SG of Port 4000 connects it to Internal Load Balancer SG
  - Database Tier SG of MySQL(3306) connects it to App Tier SG
![Screenshot 2025-05-29 015625](https://github.com/user-attachments/assets/50cbf5e7-81ec-4882-ae58-2448b6201e3a)

### Step 5: Create DB Subnet Group & RDS
  - Create a DB subnet group using two or more private subnets across different Availability Zones
  - Create a RDS instance
  - Assign the RDS instance in the DB subnet group you just created.
    ![Screenshot 2025-05-29 021519](https://github.com/user-attachments/assets/7efc8689-c1a9-4e9b-98c1-a52630c9b234)

### Step 6: Create Test App Server and Test Web Server, Install Packages(Nginx, Node.js (React)), Test Connections
  - use Commands to install packages
  - Create AMI
    ![Screenshot 2025-05-29 015550](https://github.com/user-attachments/assets/c86a385c-d49f-408f-87e7-8c469599098e)
  - Create launch template using AMI
    ![Screenshot 2025-05-29 015533](https://github.com/user-attachments/assets/12f83629-3fba-4511-841e-15d03dcc5dc2)
  - Create target group
    ![Screenshot 2025-05-29 015659](https://github.com/user-attachments/assets/de64ac2b-1521-4334-bea9-1d53b88e68c1)
  - Create internal and external load balancer
    ![Screenshot 2025-05-29 015649](https://github.com/user-attachments/assets/79475824-9628-4261-999f-d72c0d778207)
  - Create autoscaling group
  - ![Screenshot 2025-05-29 015711](https://github.com/user-attachments/assets/9b34706b-13ec-47f1-a7c5-6a8ba2728f4d)
  - Edit nginx.conf file in local system by adding the DNS name of Internal LB & upload the file in S3.

### Step 7: Add External ALB DNS to Route 53
  - Create a new DNS record in Route 53 to point your domain (or subdomain) to the external Application Load Balancer's DNS name. This allows users to access your application using a friendly URL.

### Step 8: Create CloudWatch Alarms Along with SNS
![Screenshot 2025-05-29 020314](https://github.com/user-attachments/assets/f2de3643-c82c-4034-994d-dd4fced46e0b)
![Screenshot 2025-05-29 020135](https://github.com/user-attachments/assets/e9960f5b-a9a8-4bf0-9c09-3c901c8fcb65)
![Screenshot 2025-05-29 022059](https://github.com/user-attachments/assets/78a88889-99d5-445d-870f-5420131e9395)
![Screenshot 2025-05-29 022159](https://github.com/user-attachments/assets/eb2c2f32-928c-4cbd-a6c9-2b103c49c58b)

### Step 9: Create CloudTrail
![Screenshot 2025-05-29 015447](https://github.com/user-attachments/assets/6e033dba-3949-41fa-b797-ed132b74e1bd)

### Step 10: Deleting the Entire Infrastructure
  - It is good to delete the entire infrastructure to avoid the charges in AWS.
  - Delete CloudFront
  - Delete CloudWatch alarms
  - Delete records from Route 53
  - Delete load balancers, target groups, ASG, launch templates
  - Delete security group
  - Delete NAT gateway
  - Release elastic IP
  - Delete VPC
  - Delete RDS subnet group and RDS.

## Workshop Instructions:
https://catalog.us-east-1.prod.workshops.aws/workshops/85cd2bb2-7f79-4e96-bdee-8078e469752a/en-US
