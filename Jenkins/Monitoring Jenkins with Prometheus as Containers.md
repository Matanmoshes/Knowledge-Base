## **Guide for Monitoring Jenkins with Prometheus as Containers**

Running Jenkins and Prometheus as containers can simplify setup, provide easy portability, and improve scalability. In this guide, you will learn how to deploy Jenkins and Prometheus as Docker containers and configure Jenkins monitoring with Prometheus.

---

### **Step 1: Set Up Jenkins as a Docker Container**

First, we'll set up Jenkins as a container using the official Jenkins Docker image.

#### **1.1. Pull Jenkins Docker Image:**
```bash
docker pull jenkins/jenkins:lts
```

#### **1.2. Run Jenkins Container:**
```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```
- Jenkins will be available at `http://<your-server-ip>:8080`.
- Use the initial admin password found in `/var/jenkins_home/secrets/initialAdminPassword`.

---

### **Step 2: Install Prometheus Plugin in Jenkins**

To expose Jenkins metrics for Prometheus, you need to install the Prometheus Metrics Plugin.

#### **2.1. Access Jenkins UI:**
1. Open Jenkins at `http://<your-server-ip>:8080`.
2. Log in with the admin credentials (see the previous step for the initial password).

#### **2.2. Install the Prometheus Metrics Plugin:**
1. Go to **Manage Jenkins** > **Manage Plugins** > **Available** tab.
2. Search for the **Prometheus Metrics Plugin**.
3. Install the plugin and restart Jenkins if required.

#### **2.3. Configure the Prometheus Plugin:**
1. Navigate to **Manage Jenkins** > **Configure System**.
2. Scroll to the **Prometheus** section.
3. Enable **Expose metrics** and set the metrics path to `/prometheus`.

Jenkins metrics will be exposed at `http://<your-server-ip>:8080/prometheus`.

---

### **Step 3: Set Up Prometheus as a Docker Container**

Now we’ll set up Prometheus as a Docker container to scrape the Jenkins metrics.

#### **3.1. Create a Prometheus Configuration File:**
Create a file called `prometheus.yml` with the following content:
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'jenkins'
    metrics_path: '/prometheus'
    static_configs:
      - targets: ['<jenkins-server-ip>:8080']
```
Replace `<jenkins-server-ip>` with your Jenkins server IP address or hostname.

#### **3.2. Run Prometheus Container:**
```bash
docker run -d \
  --name prometheus \
  -p 9090:9090 \
  -v /path/to/prometheus.yml:/etc/prometheus/prometheus.yml \
  prom/prometheus
```
- Prometheus UI will be available at `http://<your-server-ip>:9090`.

---

### **Step 4: Verify Prometheus Scraping Jenkins Metrics**

1. Open Prometheus at `http://<your-server-ip>:9090`.
2. Go to the **Status** > **Targets** page.
3. Verify that Prometheus is scraping Jenkins metrics from the `/prometheus` endpoint.

---

### **Step 5: Set Up Grafana as a Docker Container (Optional, for Visualization)**

For better visualization of the metrics, you can run Grafana in a container and connect it to Prometheus.

#### **5.1. Pull Grafana Docker Image:**
```bash
docker pull grafana/grafana
```

#### **5.2. Run Grafana Container:**
```bash
docker run -d \
  --name grafana \
  -p 3000:3000 \
  -v grafana-storage:/var/lib/grafana \
  grafana/grafana
```
- Grafana will be available at `http://<your-server-ip>:3000`.
- The default login is `admin`/`admin`.

#### **5.3. Add Prometheus as a Data Source in Grafana:**
1. Access Grafana at `http://<your-server-ip>:3000`.
2. Go to **Configuration** > **Data Sources** > **Add Data Source**.
3. Choose **Prometheus** and enter the URL: `http://<your-server-ip>:9090`.
4. Click **Save & Test**.

#### **5.4. Create Jenkins Dashboards:**
- You can now create dashboards in Grafana to visualize Jenkins metrics like job durations, CPU usage, or build success/failure rates.
- You can also import pre-built Grafana Jenkins dashboards available on the Grafana website.

---

### **Step 6: Access Logs and Monitor Resource Usage**

To monitor logs and resource usage of Jenkins and Prometheus containers:

#### **6.1. View Logs:**
```bash
docker logs jenkins
docker logs prometheus
```

#### **6.2. Monitor Resource Usage:**
```bash
docker stats
```
This will show the CPU, memory, and network usage for both containers.

---

### **Summary**

By following these steps, you will have set up Jenkins and Prometheus as Docker containers and enabled Jenkins monitoring via Prometheus. Optionally, you can visualize metrics using Grafana for better insights.

- **Jenkins**: Exposes metrics at `/prometheus`.
- **Prometheus**: Scrapes Jenkins metrics and displays them in its UI.
- **Grafana**: (Optional) Visualizes metrics from Prometheus in custom dashboards.

This containerized setup ensures easy deployment, scalability, and efficient monitoring of Jenkins.