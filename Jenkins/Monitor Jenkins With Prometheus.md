## **Guide for Monitoring Jenkins with Prometheus**

Monitoring Jenkins with Prometheus gives you real-time insights into Jenkins metrics like job status, execution times, resource consumption, and more. By visualizing these metrics through Prometheus and tools like Grafana, you can ensure your Jenkins server is running optimally. This guide will walk you through setting up Prometheus to monitor Jenkins.

---

### **Step 1: Install Prometheus**

Follow these steps to set up Prometheus on your server.

#### **1.1. Download and Install Prometheus:**

1. **Download Prometheus:**
```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.46.0/prometheus-2.46.0.linux-amd64.tar.gz
```

2. **Extract the files:**
```bash
   tar xvf prometheus-2.46.0.linux-amd64.tar.gz
```

3. **Move Prometheus binaries:**
```bash
   sudo mv prometheus-2.46.0.linux-amd64/prometheus /usr/local/bin/
   sudo mv prometheus-2.46.0.linux-amd64/promtool /usr/local/bin/
```

4. **Create configuration and data directories:**
```bash
   sudo mkdir -p /etc/prometheus /var/lib/prometheus
   sudo mv prometheus-2.46.0.linux-amd64/prometheus.yml /etc/prometheus/
```

5. **Create Prometheus service file:**
```bash
   sudo nano /etc/systemd/system/prometheus.service
```

   Add the following content:
```ini
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
ExecStart=/usr/local/bin/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/var/lib/prometheus/

[Install]
WantedBy=multi-user.target
```

6. **Start and enable Prometheus:**
```bash
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

7. **Verify Prometheus is running:** Go to `http://<your-server-ip>:9090` in your browser to access the Prometheus UI.

---

### **Step 2: Install Jenkins Prometheus Metrics Plugin**

The **Prometheus Metrics Plugin** exposes Jenkins metrics in a format that Prometheus can scrape.

#### **2.1. Install the Prometheus Plugin in Jenkins:**

1. Go to **Manage Jenkins** > **Manage Plugins** > **Available** tab.
2. Search for the **Prometheus Metrics Plugin**.
3. Install the plugin and restart Jenkins if required.

#### **2.2. Configure the Prometheus Metrics Plugin:**

1. Go to **Manage Jenkins** > **Configure System**.
2. Scroll down to the **Prometheus** section.
3. Enable the **Expose metrics** option.
4. Set the endpoint for Prometheus scraping, e.g., `/prometheus`.

By default, metrics will be exposed at: `http://<your-jenkins-server>:8080/prometheus`.

---

### **Step 3: Configure Prometheus to Scrape Jenkins Metrics**

You need to configure Prometheus to scrape Jenkins metrics from the `/prometheus` endpoint.

#### **3.1. Edit Prometheus Configuration File:**

1. Open your Prometheus configuration file:
```bash
sudo nano /etc/prometheus/prometheus.yml
```

2. Add a new scrape job for Jenkins:
```yaml
scrape_configs:
    - job_name: 'jenkins'
    static_configs:
        - targets: ['<jenkins-server-ip>:8080']
    metrics_path: '/prometheus'
    scrape_interval: 15s
```

   Replace `<jenkins-server-ip>` with your Jenkins server’s IP address or hostname.

#### **3.2. Restart Prometheus:**

After modifying the configuration file, restart Prometheus for the changes to take effect:
```bash
sudo systemctl restart prometheus
```

---

### **Step 4: View Jenkins Metrics in Prometheus**

1. **Access Prometheus:** Go to `http://<your-prometheus-server-ip>:9090`.

2. **View Jenkins Metrics:**
   - In the **Prometheus** UI, go to the **Graph** tab.
   - Search for Jenkins-specific metrics, like `jenkins_job_duration_milliseconds` or `jenkins_builds_total`.

Prometheus will display graphs and query results for the Jenkins metrics.

---

### **Step 5: Visualizing Jenkins Metrics with Grafana**

Prometheus data can be visualized more effectively using Grafana. Grafana allows you to create custom dashboards for Jenkins metrics.

#### **5.1. Install Grafana:**

1. **Download and Install Grafana:**
```bash
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo apt-get update
sudo apt-get install grafana
```

2. **Start Grafana:**
```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

3. **Access Grafana:** Open your browser and go to `http://<your-grafana-server-ip>:3000`. The default credentials are `admin`/`admin`.

---

### **Step 6: Add Prometheus as a Data Source in Grafana**

1. **Login to Grafana** and go to **Configuration** > **Data Sources**.
2. Click **Add Data Source** and select **Prometheus**.
3. In the URL field, enter `http://<your-prometheus-server-ip>:9090`.
4. Click **Save & Test**.

---

By following these steps, you can successfully set up Jenkins monitoring with Prometheus and visualize metrics using Grafana.