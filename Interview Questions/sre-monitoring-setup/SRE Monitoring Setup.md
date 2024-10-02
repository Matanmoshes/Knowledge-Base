# Introduction

This project establishes a comprehensive monitoring stack utilizing Docker containers orchestrated by Docker Compose, deployed on an AWS EC2 instance managed by Terraform. The monitoring stack includes:

- **Web Application:** A Flask-based web application.
- **Prometheus:** For metrics collection and alerting.
- **Grafana:** For visualization and dashboarding.
- **Alertmanager:** For handling alerts from Prometheus.
- **Node Exporter & Blackbox Exporter:** For system and service monitoring.

Continuous Integration (CI) is handled via GitHub Actions, which builds and pushes Docker images to Docker Hub. Continuous Deployment (CD) is achieved using Terraform to provision and configure the necessary AWS infrastructure.

>[!Note]
>While not mandatory, I utilize a custom domain name for this web application setup. I added an A record to my hosted zone in Route 53 pointing to the EC2 public IP, allowing access to the web application, Prometheus, and Grafana using `http://sre-project.matan-moshe.online` along with the required ports (e.g., `:9090` for Prometheus)

---
# Prerequisites

Before proceeding, ensure you have the following:

- **AWS Account:** With permissions to create EC2 instances, VPCs, subnets, and security groups.
- **S3 Bucket**: S3 Bucket that will use for terraform backend to all the `tfstate` files.
- **Dynamodb Table**: DynamoDB table for managing state locking, to prevents concurrent updates.
- **Docker Hub Account:** To store Docker images.
- **GitHub Repository:** Where the project code is hosted.
- **Terraform Installed:** Version 1.3.0 or later.
- **GitHub Actions Secrets:** Configure the following secrets in your GitHub repository:
  - `AWS_ACCESS_KEY_ID`
  - `AWS_SECRET_ACCESS_KEY`
  - `OPENWEATHER_API_KEY`
  - `SMTP_AUTH_PASSWORD`
  - `DOCKER_USERNAME`
  - `DOCKER_PASSWORD`

---

# Clone the project repo

- Create a directory for the project and move to it:
```Bash
sudo mkdir monitoring_project
cd monitoring_project
```
- Clone the project repo:
```Bash
git clone https://github.com/Matanmoshes/sre-monitoring-setup.git
```

---
# Project Structure

```
sre-monitoring-setup/
├── Dockerfile
├── app
│   ├── __init__.py
│   ├── app.py
│   ├── static
│   │   ├── background.jpg
│   │   └── icons
│   └── templates
│       └── index.html
├── monitoring
│   ├── alertmanager.yml
│   ├── alerts.yml
│   ├── blackbox.yml
│   ├── docker-compose.yml
│   ├── prometheus.yml
│   
├── requirements.txt
├── terraform
│   ├── alb.tf
│   ├── backend.tf
│   ├── ec2_instance.tf
│   ├── outputs.tf
│   ├── security_groups.tf
│   ├── variables.tf
│   ├── vpc.tf
│   └── user-data.sh.tpl
└── tests
    └── test_app.py
```

---
# Continuous Integration (CI)

The CI pipeline is managed using GitHub Actions. Upon pushing new code to the repository, the CI workflow builds the Docker image and pushes it to Docker Hub.

### CI Workflow Overview

- **Workflow File:** `.github/workflows/ci-pipeline`
- **Key Steps:**
  1. **Checkout Code:** Uses `actions/checkout@v2` to clone the repository.
  2. **Docker Hub Login:** Utilizes `docker/login-action@v2` to authenticate with Docker Hub using GitHub Secrets.
  3. **Build and Push Docker Image:** Employs `docker/build-push-action@v4` to build the Docker image and push it to Docker Hub, passing the `OPENWEATHER_API_KEY` as a build argument.

### Screenshot

![[Pasted image 20240928122201.png]]

---
# Continuous Deployment (CD) with Terraform

Terraform is used to provision AWS infrastructure, including the EC2 instance, networking components, and security groups. It also configures the EC2 instance's user data to set up Docker containers.

### Terraform Configuration Overview
 - **Workflow File:** `.github/workflows/cd-pipeline`
- **Directory:** `terraform/`
- **Key Files:**
  - `variables.tf`: Defines input variables like `ami_id`, `instance_type`, `OPENWEATHER_API_KEY`, and `SMTP_AUTH_PASSWORD`.
  - `ec2_instance.tf`: Configures the EC2 instance, including user data script execution.
  - `user-data.sh.tpl`: Template script for initializing the EC2 instance, installing Docker, cloning the repository, and running Docker Compose with injected environment variables.

#### **Variable Definitions (`variables.tf`)**

Defines necessary variables with descriptions and sensitivity flags to ensure secure handling.

```hcl
variable "ami_id" {
  description = "AMI ID for the EC2 instance"
  type        = string
}

variable "instance_type" {
  description = "Type of EC2 instance"
  type        = string
  default     = "t2.micro"
}

variable "key_pair_name" {
  description = "Name of the SSH key pair"
  type        = string
}

variable "OPENWEATHER_API_KEY" {
  description = "API key for OpenWeather"
  type        = string
  sensitive   = true
}

variable "SMTP_AUTH_PASSWORD" {
  description = "Gmail App Password for SMTP authentication"
  type        = string
  sensitive   = true
}
```

#### **EC2 Instance Configuration (`ec2_instance.tf`)**

Provisions the EC2 instance and injects the user data script using the `templatefile` function to interpolate variables securely.

```hcl
resource "aws_instance" "monitoring_instance" {
  ami                    = var.ami_id
  instance_type          = var.instance_type
  subnet_id              = aws_subnet.public_subnet.id
  vpc_security_group_ids = [aws_security_group.instance_sg.id]
  key_name               = var.key_pair_name

  user_data = templatefile("${path.module}/user-data.sh.tpl", {
    OPENWEATHER_API_KEY = var.OPENWEATHER_API_KEY
    SMTP_AUTH_PASSWORD  = var.SMTP_AUTH_PASSWORD
  })

  tags = {
    Name = "monitoring-instance"
  }
}
```

#### **User Data Script Template (`user-data.sh.tpl`)**

A Bash script template that initializes the EC2 instance, installs Docker and Docker Compose, clones the repository, and runs Docker Compose with the necessary environment variables.


### Screenshot

![[Pasted image 20240928124314.png]]

---
# Deployment Steps

Follow these steps to deploy the monitoring stack:

### 1. Update Prometheus Configuration

Before deploying, update the Prometheus configuration to point to the correct EC2 public IP.
- **SSH into EC2 Instance:**

  ```bash
  ssh -i /path/to/your/key.pem ubuntu@<EC2_PUBLIC_IP>
  ```

- **File to Update:** `monitoring/prometheus.yml`

```yaml
scrape_configs:
  - job_name: 'webapp'
    static_configs:
      - targets: ['<EC2_PUBLIC_IP>:5000']
```

**Screenshot:**

![[Pasted image 20240928113313.png]]

---
### 2. Verify Docker Containers

After deployment, verify that all Docker containers are running correctly on the EC2 instance.

- **Check Running Containers:**

  ```bash
  docker ps
  ```

**Screenshot:**

![[Pasted image 20240928115112.png]]

---
### 3. Access Monitoring Tools

####  **Web Application**

- **URL:** `http://<EC2_PUBLIC_IP>/`
  
  >[!Note]
  > In my case the url can be `http://sre-project.matan-moshe.online'
  

**Screenshot :**
![[Pasted image 20240928114141.png]]

#### **Prometheus**

- **URL:** `http://<EC2_PUBLIC_IP>:9090`

**Screenshot:**

![[Pasted image 20240928114307.png]]

#### **Grafana**

- **URL:** `http://<EC2_PUBLIC_IP>:3000`
- **Default Credentials:**
  - **Username:** `admin`
  - **Password:** `admin` (You will be prompted to change this upon first login)

**Screenshot:**
![[Pasted image 20240928114508.png]]

---
### 4. Configure Grafana

1. **Login to Grafana:**
   - Navigate to `http://<EC2_PUBLIC_IP>:3000`
   - Enter the default credentials and set a new password.

   **Screenshot:**
![[Pasted image 20240928131025.png]]


2. **Add Prometheus as Data Source:**
   - Go to **Configuration** > **Data Sources** > **Add data source**.
   - Select **Prometheus**.
   - **URL:** `http://prometheus:9090`
   - Click **Save & Test**.

   **Screenshot :**
![[Pasted image 20240928114850.png]]

   **Test Connection:**

   **Screenshot:**

   ![[Pasted image 20240928115002.png]]


---

### 5. Verify Alerts

Ensure that alerts defined in `alerts.yml` are correctly configured and visible in Grafana.

1. **Navigate to Alert Rules:**
   - Go to **Alerting** > **Alert rules** in Grafana.

   **Screenshot:**
   ![[Pasted image 20240928115858.png]]

---
### 6. Create Dashboards

To visualize metrics such as network stats and server resource utilization, create dashboards in Grafana.

1. **Import Existing Dashboard Templates:**
   - Go to **Create** > **Import** in Grafana.
   - Choose a pre-built dashboard template from the Grafana website need to copy the dashboard id, in my case i used the `Node Exporter Full` (id - 1860) because its already have what i need.

   **Screenshot :**

   ![[Pasted image 20240928131423.png]]
![[Pasted image 20240928131812.png]]


![[Pasted image 20240928131858.png]]

![[Pasted image 20240928131937.png]]


---

### 7. Test Alerting Mechanism

To ensure that your alerting setup is functioning correctly, we need to test the entire alerting pipeline from Prometheus to Alertmanager and finally to your email.

#### **Trigger an Alert in Prometheus**

We can stop on the `webapp` container that way it will triger the rulles we created:
- alert name WebappDown - 

- **Stop the `webapp` container:**
```Bash
docker-compose stop webapp
``` 

**Screenshot:**
![[Pasted image 20240928134123.png]]
#### **Verify Alert in Prometheus**

-  **Access Prometheus UI:**
    
    Navigate to `http://<EC2_PUBLIC_IP>:9090`.
    
- **Check Alert Status:**
    
    - Go to **Status** > **Alerts**.
    - Confirm that the alert you triggered is active.
    
    **Screenshot :**
    ![[Pasted image 20240927213525.png]]

#### **Check Alertmanager for Alert Receipt**

1. **Access Alertmanager UI:**
    
    Navigate to `http://<EC2_PUBLIC_IP>:9093`.
    
2. **Verify Alert Reception:**
    
    - Ensure that Alertmanager has received the alert.
    - Check the **Alerts** tab for active alerts.
    
    **Screenshot:**
![[Pasted image 20240928135504.png]]

#### 8. **Confirm Email Notification**

1. **Check Your Email:**
    
    - Verify that an email notification has been received at `matanmoshes66@gmail.com`.
    - The email should contain details about the triggered alert.
    
    **Screenshot Placeholder:**
    
2. **Troubleshoot if No Email Received:**
    
    - Ensure that the `SMTP_AUTH_PASSWORD` is correct.
    - Check Alertmanager logs for any SMTP-related errors.
    
    bash
    
    Copy code
    
    `docker logs alertmanager`
    
    **Screenshot Placeholder:**

---
## Troubleshooting

If you encounter issues during deployment, follow these troubleshooting steps:

### 1. Terraform Apply Stuck

- **Issue:** Terraform `apply` command hangs or gets stuck.
- **Solution:**
  - Ensure that all required variables are correctly passed.
  - Verify that GitHub Actions are correctly configured to pass secrets.
  - Check the EC2 instance's system logs for errors.

  **Check Logs:**

  ```bash
  ssh -i /path/to/your/key.pem ubuntu@<EC2_PUBLIC_IP>
  sudo cat /var/log/user-data.log
  ```

### 2. Docker Containers Not Running

- **Issue:** Containers are not up or have exited.
- **Solution:**
  - SSH into the EC2 instance.
  - Check container logs for errors.

  ```bash
  docker logs <container_name>
  ```

### 3. Grafana Unable to Connect to Prometheus

- **Issue:** Grafana reports that Prometheus data source is unreachable.
- **Solution:**
  - Ensure that Prometheus is running and accessible.
  - Verify network configurations in `docker-compose.yml`.

### 4. Alerts Not Triggering

- **Issue:** No alerts are firing despite threshold breaches.
- **Solution:**
  - Verify alert rules in `alerts.yml`.
  - Check Alertmanager logs for any issues.

  ```bash
  docker logs alertmanager
  ```

