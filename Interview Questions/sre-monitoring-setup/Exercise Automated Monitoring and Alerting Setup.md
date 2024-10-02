# Introduction

This project establishes a comprehensive monitoring stack utilizing Docker containers orchestrated by Docker Compose, deployed on an AWS EC2 instance managed by Terraform. The monitoring stack includes:

- **Web Application:** A Flask-based web application.
- **Prometheus:** For metrics collection and alerting.
- **Grafana:** For visualization and dashboarding.
- **Alertmanager:** For handling alerts from Prometheus.
- **Node Exporter & Blackbox Exporter:** For system and service monitoring.

Continuous Integration (CI) is handled via GitHub Actions, which builds and pushes Docker images to Docker Hub. Continuous Deployment (CD) is achieved using Terraform to provision and configure the necessary AWS infrastructure.

>[!Note]
>While not mandatory, I utilize a custom domain name for this web application setup. I added an A record to my hosted zone in Route 53 pointing to the EC2 >public IP, allowing >access to the web application, Prometheus, and Grafana using http://sre-project.matan-moshe.online along with the required ports (e.g., :9090 for Prometheus)

---


# Prerequisites

Before proceeding, ensure you have the following:

- **AWS Account:** With permissions to create EC2 instances, VPCs, subnets, and security groups.
- **Key Pair:** To ssh tothe ec2 machine you will need to generate `.pem` key pair.
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
├── README.md
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

## CI Workflow Overview

- **Workflow File:** `.github/workflows/ci-pipeline`
- **Key Steps:**
  1. **Checkout Code:** Uses `actions/checkout@v2` to clone the repository.
  2. **Docker Hub Login:** Utilizes `docker/login-action@v2` to authenticate with Docker Hub using GitHub Secrets.
  3. **Build and Push Docker Image:** Employs `docker/build-push-action@v4` to build the Docker image and push it to Docker Hub, passing the `OPENWEATHER_API_KEY` as a build argument.

### Screenshot

![image](https://github.com/user-attachments/assets/09fb4937-e82d-4f74-8c86-da77ce657cf2)

---
# Continuous Deployment (CD) with Terraform

Terraform is used to provision AWS infrastructure, including the EC2 instance, networking components, and security groups. It also configures the EC2 instance's user data to set up Docker containers.

## Terraform Configuration Overview
 - **Workflow File:** `.github/workflows/cd-pipeline`
- **Directory:** `terraform/`
- **Key Files:**
  - `variables.tf`: Defines input variables like `ami_id`, `instance_type`, `OPENWEATHER_API_KEY`, and `SMTP_AUTH_PASSWORD`.
  - `ec2_instance.tf`: Configures the EC2 instance, including user data script execution.
  - `user-data.sh.tpl`: Template script for initializing the EC2 instance, installing Docker, cloning the repository, and running Docker Compose with injected environment variables.

## **Variable Definitions (`variables.tf`)**

Defines necessary variables with descriptions and sensitivity flags to ensure secure handling.

```hcl
variable "aws_region" {
  description = "AWS region to deploy resources"
  default     = "us-east-1"
}

variable "vpc_cidr_block" {
  description = "CIDR block for the VPC"
  default     = "10.0.0.0/16"
}

variable "public_subnet_cidr" {
  description = "CIDR block for the public subnet"
  default     = "10.0.1.0/24"
}

variable "instance_type" {
  description = "EC2 instance type"
  default     = "t3.medium"
}

variable "ami_id" {
  description = "AMI ID for EC2 instance (Amazon Linux 2)"
  default     = "ami-0e86e20dae9224db8"  # Ubuntu Server 24.04  
}

variable "allowed_ip" {
  description = "Your IP address to allow SSH access (in CIDR notation)"
  default     = "0.0.0.0/0"  
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

variable "key_pair_name" {
  description = "Name of the key pair to use for EC2 instance"
  default     = "28-09-24-key"  
}
```

## **EC2 Instance Configuration (`ec2_instance.tf`)**

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

## **User Data Script Template (`user-data.sh.tpl`)**

A Bash script template that initializes the EC2 instance, installs Docker and Docker Compose, clones the repository, and runs Docker Compose with the necessary environment variables.

**Key Parts from the script:**
```Bash
## Export environment variables for Docker Compose
echo "export OPENWEATHER_API_KEY=${OPENWEATHER_API_KEY}" >> /etc/profile
echo "export SMTP_AUTH_PASSWORD=${SMTP_AUTH_PASSWORD}" >> /etc/profile
#
# This is only the setup part 
sudo -H -u ubuntu git clone https://github.com/Matanmoshes/sre-monitoring-setup.git /home/ubuntu/sre-monitoring-setup

chown -R ubuntu:ubuntu /home/ubuntu/sre-monitoring-setup
cd /home/ubuntu/sre-monitoring-setup/monitoring

mkdir -p prometheus-data grafana-data
chown -R ubuntu:ubuntu prometheus-data grafana-data

docker-compose up -d
echo "User data script completed at $(date)" >> /var/log/user-data.log
```

### Screenshot

![image](https://github.com/user-attachments/assets/efcac146-b8ff-4dcf-9265-8c115f2496d6)

---

# Monitoring Setup

I've implemented a monitoring setup using Docker Compose. This setup integrates several key components: **Prometheus**, **Grafana**, **Alertmanager**, **Node Exporter**, and **Blackbox Exporter**. Additionally, I've added to my Python Flask application a library  `prometheus_flask_exporter` that integrating the prometheus to expose custom metrics.

```Diagram
+-----------------+        +-----------------+        +-----------------+
|   Node Exporter | <----> |    Prometheus   | <----> |   Alertmanager  |
+-----------------+        +-----------------+        +-----------------+
                               ^    ^    ^
                               |    |    |
                        +------+    |    +------+
                        |           |           |
               +----------------+  |  +----------------+ 
               | Blackbox Exporter|  |  | Flask App      |
               +----------------+  |  +----------------+
                                     |
                              +----------------+
                              |     Grafana    |
                              +----------------+
```

## Docker Compose Configuration

My monitoring setup is orchestrated using a `docker-compose.yml` file, which defines and manages all necessary services.

### Prometheus

Prometheus serves as the core of my monitoring stack, responsible for scraping and storing metrics from various sources.

- **Configuration:**
  - **Scrape Targets:** Configured to collect metrics from `Node Exporter`, my Flask application, and the `Blackbox Exporter`.
  - **Alerting Rules:** Defined in a separate `alerts.yml` file, which includes all my custom alert definitions.

- **Key Settings:**
```yaml
scrape_configs:
  - job_name: 'webapp'
    static_configs:
      - targets: ['webapp:5000']  

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['node_exporter:9100']

  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]  
    static_configs:
      - targets:
          - http://34.207.195.96:80  # Public IP of the web application
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - target_label: instance
        replacement: webapp
      - target_label: __address__
        replacement: blackbox_exporter:9115  

rule_files:
  - "alerts.yml"

  ```

### Grafana

I used Grafana for dashboarding solution, enabling me to visualize metrics collected by Prometheus.

- **Configuration:**
  - **Data Source:** Connected to Prometheus for real-time data visualization.
  - **Dashboards:** Import required dashboard  from

- **Key Settings:**
  ```yaml
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    volumes:
      - grafana-data:/var/lib/grafana
    ports:
      - "3000:3000"
  ```

### Alertmanager

Alertmanager handles alerts sent by Prometheus, managing notification routing, grouping, and suppression.

- **Configuration:**
  - **Receivers:** Configured to send notifications via email from mu private mailbox.
  - **Routing Rules:** Defined to direct alerts based on severity and other labels.

- **Key Settings:**
```yaml
global:
  resolve_timeout: 5m
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'matanmoshes66@gmail.com'
  smtp_auth_username: 'matanmoshes66@gmail.com'
  smtp_auth_password: '{{ env "SMTP_AUTH_PASSWORD" }}'
  smtp_require_tls: true

route:
  receiver: 'email-notifications'
  group_wait: 10s
  group_interval: 10m
  repeat_interval: 1h

receivers:
  - name: 'email-notifications'
    email_configs:
      - to: 'matan.moshe66@gmail.com'
        send_resolved: true
```

### Node Exporter

Node Exporter collects hardware and OS metrics from the host machine, providing insights into system-level performance.

- **Configuration:**
  - **Metrics Collected:** CPU usage, memory usage, disk, network statistics, and more.

- **Key Settings:**
  ```yaml
  node_exporter:
    image: prom/node-exporter
    ports:
      - "9100:9100"
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.ignored-mount-points=^/(sys|proc|dev|host|etc)($|/)'
  ```

### Blackbox Exporter

Blackbox Exporter probes endpoints over HTTP, TCP, and ICMP, allowing me to monitor the availability and responsiveness of my web application.

- **Configuration:**
  - **Probing Modules:** Configured to perform HTTP checks against my Flask application’s health endpoint.

- **Key Settings:**
```yaml
modules:
  http_2xx:
    prober: http
    timeout: 5s
    http:
      valid_http_versions: ["HTTP/1.1", "HTTP/2"]
      method: GET
      tls_config:
        insecure_skip_verify: false
      fail_if_ssl: false
      fail_if_not_ssl: false
  ```

### Integration with Python Flask Application

To expose application-specific metrics, I've integrated the **Prometheus Flask Exporter** library into my Flask application. 

#### Prometheus Flask Exporter Setup

- **Library Integration:**
  ```python
  from prometheus_flask_exporter import PrometheusMetrics
  
  app = Flask(__name__)
  metrics = PrometheusMetrics(app)
  ```

- **Custom Metrics:**
  I've defined two custom counters to track the total number of requests and error responses:
  
  ```python
  # Define custom metrics if needed
  REQUEST_COUNT = metrics.counter(
      'app_request_count', 'Total number of requests',
      labels={'endpoint': lambda: request.endpoint}
  )
  
  ERROR_COUNT = metrics.counter(
      'app_error_count', 'Total number of error responses',
      labels={'endpoint': lambda: request.endpoint, 'status': lambda: getattr(request, 'status_code', 200)}
  )
  ```

These metrics allow me to monitor the frequency of requests to different endpoints and track the occurrence of HTTP 5xx errors, which are critical for maintaining application reliability.

---

# Defined Alerts

I've established a series of Prometheus alerts. Each alert is used to monitor specific aspects of my infrastructure and application.

### HighCPUUsage

- **Description:**  
  Triggers when the average CPU usage across all cores on a host exceeds **80%** for more than **2 minutes**.

- **Purpose:**  
  To identify and address scenarios where the system's CPU resources are heavily utilized, which could lead to performance degradation or indicate inefficient processes running on the host.
  
```promql
avg(rate(node_cpu_seconds_total{mode!="idle"}[1m])) by (instance) > 0.8
```

### HighMemoryUsage

- **Description:**  
  It is triggered when memory usage on a host surpasses **90%** for over **2 minutes**.

- **Purpose:**  
  To monitor memory consumption and prevent potential application crashes or slowdowns due to insufficient available memory.

```promql
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes > 0.9
```

### WebappDown

- **Description:**  
  Alerts when the web application becomes unreachable or fails to respond for more than **2 minutes**.

- **Purpose:**  
  Ensures that the web application remains available to users. Immediate notification allows me to investigate and resolve downtime issues promptly, minimizing user impact.

```promql
probe_success{job="blackbox"} == 0
```

### HighResponseTime

- **Description:**  
  Activates when the **95th percentile** of HTTP response times for the web application exceeds **1 second** over the last **2 minutes**.

- **Purpose:**  
  To monitor the application's responsiveness and detect performance bottlenecks. High response times can adversely affect user experience, so this alert helps in maintaining optimal performance levels.

```promql
histogram_quantile(0.95, sum(rate(http_response_time_seconds_bucket{job="webapp"}[5m])) by (le)) > 1
```

### HighErrorCount

- **Description:**  
  Alerts when the rate of HTTP **5xx** errors exceeds **500** in the last **5 minutes**.

- **Purpose:**  
  To detect a errors in server-side errors, which could indicate underlying application issues, such as bugs or resource problem. Prompt detection allows for quick intervention to maintain application stability and user satisfaction.

```promql
rate(app_error_count{status=~"5.."}[5m]) > 500
```

---
# Deployment Steps

Follow these steps to deploy the monitoring stack:

### 1. Update Prometheus Configuration

Before deploying, update the Prometheus configuration to point to the correct EC2 public IP.
- **SSH into EC2 Instance:**

  ```bash
  ssh -i /path/to/your/key.pem ubuntu@<EC2_PUBLIC_IP>
  ```

- **Create or Edit the `.env` File**

1. **Navigate to Your Project Directory:**

```bash
cd ~/sre-monitoring-setup/monitoring
```

2. **Create the `.env` File (If It Doesn't Exist):**

```bash
touch .env
```

3. **Edit the `.env` File:**

Open the `.env` file using your preferred text editor. Here, we'll use `nano`:

```bash
nano .env
```

4. **Add the Environment Variables:**

Ensure that your `.env` file contains the following lines **without quotes** and **without spaces** in the `SMTP_AUTH_PASSWORD`:

```env
OPENWEATHER_API_KEY=epi-key
SMTP_AUTH_PASSWORD=app-google-password
```


- **File to Update:** `monitoring/prometheus.yml`

```yaml
scrape_configs:
  - job_name: 'webapp'
    static_configs:
      - targets: ['<EC2_PUBLIC_IP>:5000']
```

**Screenshot:**

![image](https://github.com/user-attachments/assets/11a8b9c6-978f-441a-88be-f5c69a44ec51)

---
## 2. Verify Docker Containers

After deployment, verify that all Docker containers are running correctly on the EC2 instance.

- **Run the docker-compose file:**
```Bash
cd /home/ubuntu/sre-monitoring-setup/monitoring
docker-compose up -d
```
- **Check Running Containers:**

  ```bash
  docker ps
  ```

**Screenshot:**

![image](https://github.com/user-attachments/assets/302509a3-e35c-417b-8207-9069d482162c)

---
## 3. Access Monitoring Tools

####  **Web Application**

- **URL:** `http://<EC2_PUBLIC_IP>/`
  
  >[!Note]
  > In my case the url can be `http://sre-project.matan-moshe.online'
  

**Screenshot :**
![image](https://github.com/user-attachments/assets/122d4f52-f368-421f-8063-f1be750f4f61)

#### **Prometheus**

- **URL:** `http://<EC2_PUBLIC_IP>:9090`

**Screenshot:**

![image](https://github.com/user-attachments/assets/324b0d93-f343-48bf-b6fe-6a780bfc8b45)

#### **Grafana**

- **URL:** `http://<EC2_PUBLIC_IP>:3000`
- **Default Credentials:**
  - **Username:** `admin`
  - **Password:** `admin` (You will be prompted to change this upon first login)

**Screenshot:**
![image](https://github.com/user-attachments/assets/a035a40d-785a-4a46-a5e7-f31c1594b72e)

---
## 4. Configure Grafana

1. **Login to Grafana:**
   - Navigate to `http://<EC2_PUBLIC_IP>:3000`
   - Enter the default credentials and set a new password.

   **Screenshot:**
![image](https://github.com/user-attachments/assets/7ae0bb1a-15b4-41d9-a180-6569ea36f38a)


2. **Add Prometheus as Data Source:**
   - Go to **Configuration** > **Data Sources** > **Add data source**.
   - Select **Prometheus**.
   - **URL:** `http://prometheus:9090`
   - Click **Save & Test**.

   **Screenshot :**
![image](https://github.com/user-attachments/assets/181a9ef9-2d9b-49ae-8009-1ddef751e78c)

   **Test Connection:**

   **Screenshot:**

![image](https://github.com/user-attachments/assets/580d4149-5294-4d5a-a0f2-20f8e7202720)


---

## 5. Verify Alerts

Ensure that alerts defined in `alerts.yml` are correctly configured and visible in Grafana.

 **Navigate to Alert Rules:**
   - Go to **Alerting** > **Alert rules** in Grafana.

**Those are the alerts I created:**
- HighCPUUsage: 
```promql
avg by (instance) (rate(node_cpu_seconds_total{mode!="idle"}[1m])) > 0.8
```
- HighMemoryUsage:
```promql
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes > 0.9
```
- WebappDown:
```promql
probe_success{job="blackbox"} == 0
```
- HighResponseTime:
```promql
histogram_quantile(0.95, sum by (le) (rate(http_response_time_seconds_bucket{job="webapp"}[5m]))) > 1
```

   **Screenshot:**
![image](https://github.com/user-attachments/assets/40b35633-fed0-4411-a08e-7bcd5b875f2b)



---
## 6. Create Dashboards

To visualize metrics such as network stats and server resource utilization, I've used the imported dashboard `Node Exporter Full` from grafana website and the second dashboard I created with promql queries.

### Custom Dashboard Queries

1. **CPU Usage node_exporter**
   
   *The CPU usage of the Flask app as measured over 30-second intervals.*
   
   ```promql
   rate(process_cpu_seconds_total{job="node_exporter"}[30s])
   ```

2. **Successful Flask Requests Per Second**
   
   *Number of successful Flask requests per second, shown per path.*
   
   ```promql
   rate(flask_http_request_duration_seconds_count{status="200"}[30s])
   ```

3. **Memory Usage Webapp**
   
   *The memory usage of the Flask app.*
   
   ```promql
   process_resident_memory_bytes{job="webapp"}
   ```

4. **Errors Per Second**
   
   *Number of failed (non HTTP 200) responses per second.*
   
   ```promql
   sum(rate(flask_http_request_duration_seconds_count{status!="200"}[30s]))
   ```

5. **Memory Usage node_exporter**
   
   *The memory usage of the host.*
   
   ```promql
   process_resident_memory_bytes{job="node_exporter"}
   ```

6. **Total Requests Per Minute**
   
   *The total number of requests measured over one-minute intervals, shown per HTTP response status code.*
   
   ```promql
   increase(flask_http_request_total[1m])
   ```

![image](https://github.com/user-attachments/assets/1e28765b-5c42-41a7-a0ee-f61dafa35597)


### Imported Node Exporter Full Dashboard

In addition to my custom dashboard, I imported the **Node Exporter Full** dashboard from Grafana's [official dashboard repository](https://grafana.com/grafana/dashboards/1860-node-exporter-full/). This comprehensive dashboard provides in-depth insights into the host system's performance, including CPU, memory, disk, and network metrics. Integrating this dashboard alongside my custom panels ensures a holistic view of both application-specific and system-level metrics.

1. **Import Existing Dashboard Templates:**
   - Go to **Create** > **Import** in Grafana.

![image](https://github.com/user-attachments/assets/a07cfe11-7343-48f1-9262-1833639fa630)

   - Choose a pre-built dashboard template from the Grafana website. You need to copy the dashboard ID, in my case, I used the `Node Exporter Full` (id - 1860) because it already has what I need.


![image](https://github.com/user-attachments/assets/bbf4f107-0b98-41c6-9fe9-96a480859f3b)

![image](https://github.com/user-attachments/assets/19a9e7fe-1eef-4386-8bf0-84a301907949)

![image](https://github.com/user-attachments/assets/effe4474-94ca-4259-af16-2813b898404e)


<img width="1236" alt="image" src="https://github.com/user-attachments/assets/5dadfef9-6719-46ac-b3bd-3dc7633f0f3c">



---

### 7. Test Alerting Mechanism

To ensure that your alerting setup is functioning correctly, we need to test the entire alerting pipeline from Prometheus to Alertmanager and finally to your email.

#### **Trigger an Alert in Prometheus**

We can stop on the `webapp` container that way it will triger the rules we created:
- alert name WebappDown - 

- **Stop the `webapp` container:**
```Bash
docker-compose stop webapp
``` 

**Screenshot:**
![image](https://github.com/user-attachments/assets/5c849128-4cab-48de-9018-687d32a7f796)

- Edit the `alerts.yml' file to change the percentage of the memory usage to 10% from 90% in the 'HighMemoryUsage' so the alert would be triggered.

![image](https://github.com/user-attachments/assets/60c85f8f-71c5-467f-bc1b-020abd7356da)


#### **Verify Alert in Prometheus**

-  **Access Prometheus UI:**
    
    Navigate to `http://<EC2_PUBLIC_IP>:9090`.
    
- **Check Alert Status:**
    
    - Go to **Status** > **Alerts**.
    - Confirm that the alert you triggered is active.
    
    ![image](https://github.com/user-attachments/assets/0bbf9911-d2fe-4059-b038-ac6c5c71ba85)


#### **Check Alertmanager for Alert Receipt**

1. **Access Alertmanager UI:**
    
    Navigate to `http://<EC2_PUBLIC_IP>:9093`.
    
2. **Verify Alert Reception:**
    
    - Ensure that Alertmanager has received the alert.
    - Check the **Alerts** tab for active alerts.
    
    **Screenshot:**
![image](https://github.com/user-attachments/assets/f3927f8a-2c65-432c-9465-2c4d7ec175b9)

#### 8. **Confirm Email Notification**

1. **Check Your Email:**
    
    - Verify that an email notification has been received at `matanmoshes66@gmail.com`.
    - The email should contain details about the triggered alert.
    
    **Screenshot :**
<img width="1200" alt="image" src="https://github.com/user-attachments/assets/d4f72d53-910b-4f38-b877-15b8e3a5bdd3">
<img width="1206" alt="image" src="https://github.com/user-attachments/assets/8d03b147-73b3-443b-8f3e-c5c9c31dd69a">



2. **Troubleshoot if No Email Received:**
    
    - Ensure that the `SMTP_AUTH_PASSWORD` is correct.
    - Check Alertmanager logs for any SMTP-related errors.
  ```Bash
  docker logs alertmanager
  ```    

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

  ---

## Reference

- https://grafana.com/docs/alloy/latest/reference/components/prometheus/prometheus.exporter.blackbox/
- https://www.stackhero.io/en/services/Prometheus/documentations/Blackbox-Exporter
- https://dev.to/chafroudtarek/part-1-how-to-set-up-grafana-and-prometheus-using-docker-i47
- https://grafana.com/docs/grafana-cloud/send-data/metrics/metrics-prometheus/prometheus-config-examples/docker-compose-linux/
- https://prometheus.io/docs/alerting/latest/configuration/
- https://grafana.com/grafana/dashboards/1860-node-exporter-full/
- https://prometheus.io/docs/guides/node-exporter/
- https://pypi.org/project/prometheus-flask-exporter/
