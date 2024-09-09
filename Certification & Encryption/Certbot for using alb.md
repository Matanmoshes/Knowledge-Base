### **Detailed Guide: Using Certbot (Other Option) for Your Scenario**

Since your application is using an **AWS Application Load Balancer (ALB)** and you plan to manage the SSL certificates through **Certbot** on an **Nginx server**, we will follow the "Other" Certbot installation option and then upload the generated SSL certificates to AWS ACM. Here’s a step-by-step guide tailored to your use case.

Reference: https://certbot.eff.org/instructions?ws=other&os=pip

---

### **1. SSH into Your Server**

First, connect to your **EC2 instance** running Nginx:

```bash
ssh -i /path/to/your/key.pem ec2-user@your-instance-public-ip
```

---

### **2. Install System Dependencies**

Depending on your OS, install the required dependencies.

#### **For Ubuntu/Debian-based systems**:

```bash
sudo apt update
sudo apt install python3 python3-venv libaugeas0
```

#### **For CentOS/RHEL-based systems**:

```bash
sudo yum install python3 augeas-libs
```

---

### **3. Remove Old Certbot Versions**

If Certbot was previously installed using a package manager, remove it to avoid conflicts:

For Ubuntu/Debian:
```bash
sudo apt-get remove certbot
```

For CentOS/RHEL:
```bash
sudo yum remove certbot
```

---

### **4. Set Up a Python Virtual Environment for Certbot**

Now, let’s set up a virtual environment to install Certbot:

```bash
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
```

---

### **5. Install Certbot**

Run this command to install Certbot inside the virtual environment:

```bash
sudo /opt/certbot/bin/pip install certbot
```

Create a symbolic link so you can easily run Certbot from anywhere:

```bash
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
```

---

### **6. Generate the Certificate Using Certbot (DNS Validation)**

In your scenario, since your web server is behind an **AWS ALB**, and you don't want to stop it, you can use **DNS validation** to generate the certificate.

1. Run the Certbot command with **manual DNS validation**:

```bash
sudo certbot certonly --manual --preferred-challenges=dns
```

2. Certbot will ask you for the domain name. Enter your domain:
   ```
   matan-moshe.online
   ```

3. Certbot will provide a DNS challenge and ask you to create a **TXT record** in your DNS provider (Route 53 in your private AWS account).

   For example:
   ```
   _acme-challenge.matan-moshe.online TXT "random-token-from-certbot"
   ```

4. **Add the TXT record** in **Route 53**:
   - Log into your **AWS Route 53**.
   - Navigate to your hosted zone (`matan-moshe.online`).
   - Create a new **TXT** record with the name `_acme-challenge.matan-moshe.online` and the value provided by Certbot.

5. After the record is created, return to Certbot and press **Enter** to proceed with the verification.

6. Certbot will confirm successful validation and generate the certificate.

---

### **7. Locate the Certificate Files**

After successful verification, Certbot will generate the certificate files in:

- **Certificate**: `/etc/letsencrypt/live/matan-moshe.online/fullchain.pem`
- **Private Key**: `/etc/letsencrypt/live/matan-moshe.online/privkey.pem`

---

### **8. Upload the Certificate to AWS ACM**

Now, you need to upload the generated certificate to **AWS Certificate Manager (ACM)** so you can attach it to your **ALB**.

1. **Go to AWS ACM** in your AWS Console.
2. Choose **Import a certificate**.
3. Upload the following files:
   - **Certificate body**: Open the `fullchain.pem` file and paste its content.
   - **Private key**: Open the `privkey.pem` file and paste its content.
   - **Certificate chain**: Leave this empty (not required for Let's Encrypt).

---

### **9. Attach the Certificate to Your ALB**

After the certificate is successfully imported into **ACM**, you can attach it to your ALB.

1. In the **EC2 Console**, navigate to **Load Balancers**.
2. Select your ALB (`webserver-alb-285395319.us-east-1.elb.amazonaws.com`).
3. Under the **Listeners** tab, find your HTTPS listener and select **View/edit certificates**.
4. Add the newly imported ACM certificate.

---

### **10. Update Route 53 DNS**

To ensure traffic to your domain is routed correctly to the ALB:

1. In **Route 53**, create or update an **A record (Alias)** to point your domain (`matan-moshe.online`) to the ALB DNS name.

2. Wait for DNS propagation (usually within minutes, but up to 24-48 hours).

---

### **11. Set Up Automatic Certificate Renewal**

Let’s Encrypt certificates are only valid for 90 days, so you need to ensure they renew automatically. Set up a cron job for automatic renewal.

Run this command:

```bash
echo "0 0,12 * * * root /opt/certbot/bin/python -c 'import random; import time; time.sleep(random.random() * 3600)' && sudo certbot renew -q" | sudo tee -a /etc/crontab > /dev/null
```

This cron job will check for renewal twice a day.

---

### **12. Test the Setup**

Finally, visit your domain (`https://matan-moshe.online`) and ensure the site is served over HTTPS. You should see the padlock icon in the browser bar indicating the site is secure.

---

### **Summary**:

1. **Install Certbot** on your EC2 instance.
2. **Run Certbot** with DNS validation to generate the SSL certificate.
3. **Add the TXT record** in Route 53 to verify domain ownership.
4. **Upload the certificate to ACM** and attach it to your **ALB**.
5. **Update Route 53** to point your domain to the ALB.
6. **Set up automatic certificate renewal** with a cron job.
7. Test the HTTPS setup by visiting your domain.

Let me know if you need help with any of the steps!