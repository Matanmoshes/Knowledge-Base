
---

# **Manual Jenkins Server Setup on Amazon Linux EC2**

This guide will walk you through the complete process of setting up Jenkins on an Amazon EC2 instance running Amazon Linux 2023, from launching the instance to accessing and securing Jenkins.

---

## **Step 1: Launch an EC2 Instance**

1. **Log in to AWS Management Console:**
   - Navigate to the [EC2 Dashboard](https://console.aws.amazon.com/ec2/).

2. **Launch a New Instance:**
   - Click **Launch Instance**.

3. **Choose an Amazon Machine Image (AMI):**
   - Select **Amazon Linux 2023** as your AMI.

4. **Choose an Instance Type:**
   - For most Jenkins setups, a `t2.micro` instance is sufficient for testing. Adjust based on your expected workload.

5. **Configure Instance Details:**
   - Ensure your instance is launched in a public subnet (to access Jenkins via the internet).
   - Assign an IAM role if you need access to other AWS services.

6. **Add Storage:**
   - The default 8 GB should suffice, but you can increase it depending on your needs.

7. **Configure Security Group:**
   - Create a new security group or use an existing one:
     - Allow inbound traffic on **Port 22** for SSH access.
     - Allow inbound traffic on **Port 8080** for Jenkins access.

8. **Review and Launch:**
   - Review your configuration and click **Launch**.
   - Select an existing key pair or create a new one for SSH access. **Download the key pair** (if creating a new one).

9. **Launch the Instance:**
   - Click **Launch Instances** to start your EC2 instance.

---

## **Step 2: Connect to Your EC2 Instance**

1. **Open Your Terminal:**
   - Navigate to the directory where your key pair (`.pem` file) is located.

2. **SSH into the Instance:**
   - Use the following command to connect, replacing `<your-key.pem>` with your key file and `<public-ip>` with your instance’s public IP:
     ```bash
     chmod 400 /path/to/your-key.pem
     ssh -i /path/to/your-key.pem ec2-user@<public-ip>
     ```

---

## **Step 3: Update the System**

1. **Update All Packages:**
   - It’s important to start with the latest updates:
     ```bash
     sudo dnf update -y
     ```

---

## **Step 4: Install Java**

1. **Install OpenJDK 11 (Amazon Corretto):**
   - Jenkins requires Java to run. Install Amazon Corretto 11:
     ```bash
     sudo dnf install -y java-11-amazon-corretto
     ```


---

## **Step 5: Install Jenkins**

### **Add Jenkins Repository**

1. **Add the Jenkins YUM Repository:**
   - Create a repository configuration file for Jenkins:

```bash
     sudo tee /etc/yum.repos.d/jenkins.repo <<EOF
     [jenkins]
     name=Jenkins-stable
     baseurl=https://pkg.jenkins.io/redhat-stable
     gpgcheck=1
     gpgkey=https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
     EOF
```



2. **Import the GPG Key:**
   - Import the Jenkins GPG key to verify packages:

```bash
     sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```

### **Install Jenkins**

1. **Install Jenkins:**
   - With the repository added, install Jenkins:

```bash
     sudo dnf install -y jenkins
```


---


## **Step 6: Start and Enable Jenkins**

1. **Start Jenkins Service:**
   - Start Jenkins so it runs immediately:

```bash
     sudo systemctl start jenkins
```

2. **Enable Jenkins on Boot:**
   - Ensure Jenkins starts automatically on reboot:

```bash
     sudo systemctl enable jenkins
```

3. **Verify Jenkins is Running:**
   - Check the status to confirm Jenkins is active:

```bash
     sudo systemctl status jenkins
```


---

## **Step 7: Adjust Firewall Settings (If Applicable)**

1. **Allow Port 8080:**
   - If you have a firewall enabled, allow traffic on Jenkins' default port:

```bash
     sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp
     sudo firewall-cmd --reload
```

---

## **Step 8: Access Jenkins**

1. **Open Jenkins in Your Browser:**
   - Visit Jenkins by navigating to:

```
     http://<public-ip>:8080
```
   - Replace `<public-ip>` with your EC2 instance’s public IP address.

2. **Unlock Jenkins:**
   - When prompted, retrieve the initial admin password:

```bash
     sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

   - Copy the password and paste it into the Jenkins setup screen in your browser.

---

## **Step 9: Complete Jenkins Setup**

1. **Install Suggested Plugins:**
   - Select "Install suggested plugins" to get started with a recommended set of plugins.

2. **Create Admin User:**
   - Fill in the details to create your first admin user.

3. **Set Jenkins URL:**
   - Confirm or set the Jenkins URL (usually auto-filled).

4. **Finish the Setup:**
   - Once setup is complete, click "Start using Jenkins" to access the dashboard.

---

## **Step 10: Secure Jenkins (Optional but Recommended)**

1. **Enable SSL:**
   - Consider setting up SSL for secure access by configuring Jenkins behind a reverse proxy like Nginx or using an SSL certificate directly in Jenkins.

2. **Configure User Permissions:**
   - Go to "Manage Jenkins" > "Configure Global Security" to set up roles and permissions for users.

3. **Implement Regular Backups:**
   - Set up a backup strategy to regularly back up Jenkins configuration and job data.

---

