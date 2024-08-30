
# **Guide to Deploy a Flask Application on AWS ECS with Fargate:**

This guide will walk you through the steps to deploy a simple Flask application on AWS using ECS (Elastic Container Service) with Fargate. We'll start from scratch, including setting up the necessary infrastructure, creating the ECS cluster, defining the task definition, and setting up the ECS service.

---
# **Project Oever Overview:**

### **Prerequisites:**

1. **AWS Account**: Ensure you have an AWS account.
2. **AWS CLI Installed**: Install the AWS CLI and configure it with your credentials.
3. **Docker Installed**: Install Docker on your local machine to build and push Docker images.
4. **Basic Flask Application**: A simple Flask application in a Docker container. If you don’t have one, we’ll create a simple example.

---

# **Part 1: Setting Up the AWS Environment**

## **Step 1: Create a VPC**
1. **Go to the VPC Dashboard** in the AWS Management Console.
2. **Create a VPC**:
   - **Name**: `terraform-env-vpc`
   - **CIDR Block**: `10.0.0.0/16`
   - **Tenancy**: Default
   - Click "Create VPC".

   **AWS CLI Command**:
   ```bash
   aws ec2 create-vpc --cidr-block 10.0.0.0/16 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=terraform-env-vpc}]'
   ```

## **Step 2: Create Public Subnets**
1. **Create Subnet 1**:
   - **Name**: `terraform-env-public-subnet-1`
   - **VPC**: Select `terraform-env-vpc`
   - **CIDR Block**: `10.0.1.0/24`
   - **Availability Zone**: `us-east-1a`
   - Click "Create Subnet".

   **AWS CLI Command**:
   ```bash
   aws ec2 create-subnet --vpc-id <VPC_ID> --cidr-block 10.0.1.0/24 --availability-zone us-east-1a --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=terraform-env-public-subnet-1}]'
   ```

2. **Create Subnet 2**:
   - **Name**: `terraform-env-public-subnet-2`
   - **VPC**: Select `terraform-env-vpc`
   - **CIDR Block**: `10.0.2.0/24`
   - **Availability Zone**: `us-east-1b`
   - Click "Create Subnet".

   **AWS CLI Command**:
   ```bash
   aws ec2 create-subnet --vpc-id <VPC_ID> --cidr-block 10.0.2.0/24 --availability-zone us-east-1b --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=terraform-env-public-subnet-2}]'
   ```

## **Step 3: Create an Internet Gateway**
1. **Create Internet Gateway**:
   - **Name**: `terraform-env-igw`
   - Click "Create".

   **AWS CLI Command**:
   ```bash
   aws ec2 create-internet-gateway --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=terraform-env-igw}]'
   ```

2. **Attach the Internet Gateway to the VPC**:
   - Select the Internet Gateway.
   - Click "Actions" -> "Attach to VPC".
   - Select `terraform-env-vpc`.
   - Click "Attach".

   **AWS CLI Command**:
   ```bash
   aws ec2 attach-internet-gateway --vpc-id <VPC_ID> --internet-gateway-id <IGW_ID>
   ```

## **Step 4: Create a Route Table**
1. **Create a Route Table**:
   - **Name**: `terraform-env-public-route-table`
   - **VPC**: Select `terraform-env-vpc`
   - Click "Create".

   **AWS CLI Command**:
   ```bash
   aws ec2 create-route-table --vpc-id <VPC_ID> --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=terraform-env-public-route-table}]'
   ```

2. **Create a Route to the Internet Gateway**:
   - Select the route table you created.
   - **Destination**: `0.0.0.0/0`
   - **Target**: Select the `terraform-env-igw`.

   **AWS CLI Command**:
   ```bash
   aws ec2 create-route --route-table-id <ROUTE_TABLE_ID> --destination-cidr-block 0.0.0.0/0 --gateway-id <IGW_ID>
   ```

3. **Associate the Route Table with Public Subnets**:
   - Select `terraform-env-public-subnet-1` and `terraform-env-public-subnet-2`.

   **AWS CLI Command**:
   ```bash
   aws ec2 associate-route-table --route-table-id <ROUTE_TABLE_ID> --subnet-id <SUBNET1_ID>
   aws ec2 associate-route-table --route-table-id <ROUTE_TABLE_ID> --subnet-id <SUBNET2_ID>
   ```

---

# **Part 2: Setting Up ECS**

## **Step 1: Create an ECS Cluster**
1. **Go to the ECS Dashboard** in the AWS Management Console.
2. **Create a Cluster**:
   - **Cluster name**: `weather-app-cluster`
   - Click "Create".

   **AWS CLI Command**:
   ```bash
   aws ecs create-cluster --cluster-name weather-app-cluster
   ```

## **Step 2: Create a Task Definition**
1. **Go to Task Definitions** in the ECS Dashboard.
2. **Create a New Task Definition**:
   - **Task Definition Name**: `weather-app-task`
   - **Network Mode**: `awsvpc`
   - **Task Execution Role**: Select an existing role or create a new one.
   - **Requires Compatibilities**: Select `FARGATE`.
   - **CPU**: `256` (0.25 vCPU)
   - **Memory**: `512` (0.5 GB)

   **AWS CLI Command**:
   - Create a file named `task-definition.json` with the following content:
     ```json
     {
       "family": "weather-app-task",
       "networkMode": "awsvpc",
       "containerDefinitions": [
         {
           "name": "flask_app",
           "image": "matanm66/weather-app:latest",
           "cpu": 256,
           "memory": 512,
           "portMappings": [
             {
               "containerPort": 5000,
               "protocol": "tcp"
             }
           ],
           "environment": [
             {
               "name": "OPENWEATHER_API_KEY",
               "value": "<YOUR_API_KEY>"
             }
           ],
           "essential": true
         }
       ],
       "requiresCompatibilities": [
         "FARGATE"
       ],
       "cpu": "256",
       "memory": "512"
     }
     ```
   - Register the task definition:
     ```bash
     aws ecs register-task-definition --cli-input-json file://task-definition.json
     ```

## **Step 3: Create a Service**
1. **Go to Services** in the ECS Dashboard.
2. **Create a New Service**:
   - **Cluster**: Select `weather-app-cluster`.
   - **Service Name**: `weather-app-service`
   - **Number of Tasks**: `1`
   - **Task Definition**: Select `weather-app-task:latest`
   - **Assign Public IP**: `ENABLED`

   **AWS CLI Command**:
   ```bash
   aws ecs create-service --cluster weather-app-cluster --service-name weather-app-service --task-definition weather-app-task --desired-count 1 --launch-type FARGATE --network-configuration "awsvpcConfiguration={subnets=[<SUBNET1_ID>,<SUBNET2_ID>],securityGroups=[<ECS_SG_ID>],assignPublicIp=ENABLED}" --load-balancers "targetGroupArn=<TARGET_GROUP_ARN>,containerName=flask_app,containerPort=5000"
   ```

---

# **Part 3: Setting Up the Load Balancer**

## **Step 1: Create an Application Load Balancer (ALB)**
1. **Go to the EC2 Dashboard** in the AWS Management Console.
2. **Create a Load Balancer**:
   - **Name**: `terraform-env-alb`
   - **Scheme**: Internet-facing
   - **Listeners**: HTTP on port `80`
   - **VPC**: `terraform-env-vpc`
   - **Availability Zones**: Select the two public subnets.

   **AWS CLI Command**:
   ```bash
   aws elbv2 create-load-balancer --name terraform-env-alb --subnets <SUBNET1_ID> <SUBNET2_ID> --security-groups <ALB_SG_ID> --scheme internet-facing --type application
   ```

3. **Configure Target Group**:
   - **Target Group Name**: `terraform-env-target-group`
   - **Protocol**: `HTTP`
   - **Port**: `5000`
   - **Health Check Path**: `/`

   **AWS CLI Command**:
   ```bash
   aws elbv2 create-target-group --name terraform-env-target-group --protocol HTTP --port 5000 --vpc-id <VPC_ID> --health-check-protocol HTTP --health-check-port 5000 --target-type ip
   ```

4. **Create Listener for the ALB**:
   - **Port**: `80`
   - **Protocol**: `HTTP`
   - **Target Group**: `terraform-env-target-group`

   **AWS CLI Command**:
   ```bash
   aws elbv2 create-listener --load-balancer-arn <ALB_ARN> --protocol HTTP --port 80 --default-actions Type=forward,TargetGroupArn=<TARGET_GROUP_ARN>
  

 ```

---

# **Part 4: Testing and Accessing Your Application**

1. **Retrieve the ALB DNS Name**:
   - Go to the EC2 Dashboard, then Load Balancers.
   - Select your ALB and find the DNS name in the description.

   **AWS CLI Command**:
   ```bash
   aws elbv2 describe-load-balancers --names terraform-env-alb --query "LoadBalancers[0].DNSName" --output text
   ```

2. **Access Your Application**:
   - Open a web browser and navigate to `http://<ALB-DNS-NAME>`.

---

