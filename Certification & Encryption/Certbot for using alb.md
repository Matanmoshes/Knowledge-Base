## Guide: Installing Certbot, Creating SSL Certificates, Uploading to ACM, and Attaching to ALB

This guide provides detailed steps for installing **Certbot** on an EC2 instance, generating SSL certificates, transferring them to your local machine, uploading them to **AWS Certificate Manager (ACM)**, and attaching them to an **AWS Application Load Balancer (ALB)**.

Reference: https://certbot.eff.org/instructions?ws=other&os=pip

---

### Prerequisites:
- An **EC2 instance** where you can install **Certbot** to generate the certificates.
- Your **domain name** properly configured in **Route 53** (or another DNS provider) to point to your ALB.
- An **Application Load Balancer (ALB)** already set up in AWS.

---

### Step 1: **SSH into the EC2 Instance**

Start by SSHing into your EC2 instance where you will generate the SSL certificate.

```bash
ssh -i ~/.ssh/id_rsa ubuntu@18.210.24.204
```

Make sure you're logged in with **sudo privileges**.

---

### Step 2: **Install Certbot and Dependencies**

You need to install Certbot along with the necessary dependencies. These steps differ depending on your Linux distribution.

#### For APT-based distributions (Ubuntu, Debian):

1. **Update your package list**:
   ```bash
   sudo apt update
   ```

2. **Install Python 3, Certbot, and Augeas (for handling configuration changes)**:
   ```bash
   sudo apt install python3 python3-venv libaugeas0
   ```

#### For RPM-based distributions (Fedora, CentOS):

1. **Install Python 3 and Augeas**:
   ```bash
   sudo dnf install python3 augeas-libs
   ```

   > **Note**: On older distributions, you may need to use `yum` instead of `dnf`, and you might need to install `python36` instead of `python3`.

---

### Step 3: **Remove Old Certbot Versions (if applicable)**

If you have any older Certbot installations (such as certbot-auto), remove them before proceeding to avoid conflicts.

#### For APT-based distributions:
```bash
sudo apt-get remove certbot
```

#### For RPM-based distributions:
```bash
sudo dnf remove certbot
```

---

### Step 4: **Set Up a Python Virtual Environment**

This isolates Certbot from your system’s Python installation and ensures compatibility.

1. **Create a Python virtual environment** for Certbot:
   ```bash
   sudo python3 -m venv /opt/certbot/
   ```

2. **Activate the virtual environment** and update pip:
   ```bash
   sudo /opt/certbot/bin/pip install --upgrade pip
   ```

---

### Step 5: **Install Certbot**

1. **Install Certbot** inside the virtual environment:
   ```bash
   sudo /opt/certbot/bin/pip install certbot
   ```

2. **Link Certbot to the system path** for easier use:
   ```bash
   sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
   ```

---

### Step 6: **Generate SSL Certificates Using Certbot**

Now that Certbot is installed, you can use it to generate SSL certificates for your domain.

#### **Using Standalone Mode** (Temporarily Spin Up a Web Server):

1. Run the following command to generate the certificate. Certbot will temporarily run a web server to complete the domain ownership verification:
   ```bash
   sudo certbot certonly --standalone
   ```

2. When prompted, enter your **domain name** (e.g., `matan-moshe.online`).

Once the process is complete, Certbot will generate the SSL certificate and store it in `/etc/letsencrypt/live/matan-moshe.online/`.

#### **Verify the Certificates**:

You can check the directory to ensure the certificates were generated correctly:
```bash
sudo ls /etc/letsencrypt/live/matan-moshe.online/
```

You should see:
```
fullchain.pem  privkey.pem
```

---

### Step 7: **Transfer the Certificates to Your Local Machine**

To upload the certificates to **AWS ACM**, you need to transfer them from your EC2 instance to your local machine.

1. Use **scp** to copy the certificate files from your EC2 instance to your local machine:

   ```bash
   scp -i /path/to/your-key.pem ubuntu@your-ec2-public-ip:/etc/letsencrypt/live/matan-moshe.online/fullchain.pem ~/letsencrypt/matan-moshe.online/
   scp -i /path/to/your-key.pem ubuntu@your-ec2-public-ip:/etc/letsencrypt/live/matan-moshe.online/privkey.pem ~/letsencrypt/matan-moshe.online/
   ```

   These commands will download the certificate (`fullchain.pem`) and private key (`privkey.pem`) to your local directory `~/letsencrypt/matan-moshe.online/`.

---

### Step 8: **Import the Certificates into AWS Certificate Manager (ACM)**

Now, import the SSL certificate and private key into **AWS Certificate Manager (ACM)**.

1. **Open the AWS Management Console**, and go to the **ACM Console**:  
   [ACM Console](https://console.aws.amazon.com/acm/home).

2. **Import the certificate**:
   - Click **Import a certificate**.
   - For the **Certificate body**, open the `fullchain.pem` file on your local machine and copy its contents.
     ```bash
     cat ~/letsencrypt/matan-moshe.online/fullchain.pem
     ```
     Paste this content into the **Certificate body** field in ACM.

   - For the **Private key**, open the `privkey.pem` file and copy its contents:
     ```bash
     cat ~/letsencrypt/matan-moshe.online/privkey.pem
     ```
     Paste this content into the **Private key** field in ACM.

   - For the **Certificate chain**, leave it empty (the chain is already included in `fullchain.pem`).

3. Review the details and click **Import**.

---

### Step 9: **Attach the Certificate to Your ALB**

Now that the certificate is in ACM, you can assign it to your **Application Load Balancer (ALB)**.

1. **Go to the EC2 Console**, then select **Load Balancers** from the left-hand menu.

2. Choose the **ALB** you want to secure with SSL.

3. In the **Listeners** tab, click on the **HTTPS (443)** listener. If you don’t have one yet, create an HTTPS listener.

4. Under **SSL certificate**, select **Choose ACM certificate**, and from the dropdown, select the certificate you just imported.

5. Configure the **Security Policy** (use **ELBSecurityPolicy-2016-08** or a newer policy for better security).

6. Click **Save** to apply the changes.

---

### Step 10: **Update Route 53 DNS Records**

Ensure that your domain is properly routed to your ALB.

1. Go to **Route 53** > **Hosted Zones**.
2. Add or update an **A Record (Alias)** for your domain (`matan-moshe.online`) to point to your ALB's DNS name.
3. Wait for DNS propagation (this can take a few minutes).

---

### Step 11: **Test the HTTPS Setup**

Once the DNS changes have propagated, test your HTTPS setup by visiting your domain:

```
https://matan-moshe.online
```

Ensure the SSL certificate is valid and the connection is secure (look for the lock icon in the address bar).

---

### Step 12: **Set Up Automatic Certificate Renewal**

Let’s Encrypt certificates expire every 90 days, so it's important to set up automatic renewal.

1. Add a **cron job** to check for renewal twice a day:
   ```bash
   echo "0 0,12 * * * root /opt/certbot/bin/python -c 'import random; import time; time.sleep(random.random() * 3600)' && sudo certbot renew -q" | sudo tee -a /etc/crontab > /dev/null
   ```

2. You can test the renewal process by running:
   ```bash
   sudo certbot renew --dry-run
   ```

---


