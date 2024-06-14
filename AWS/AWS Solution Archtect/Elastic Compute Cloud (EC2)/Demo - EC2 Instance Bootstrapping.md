### EC2 Instance Bootstrapping

**Introduction**
Amazon Elastic Compute Cloud (Amazon EC2) is a web service that provides secure, resizable compute capacity in the cloud. This guide walks through configuring a web server to corporate standards manually and then using a user data script to automate the setup process.

**Solution**

**Step 1: Log in to the AWS Management Console**
- Ensure you're in the N. Virginia (us-east-1) Region.

**Manually Install Software on webserver-01**

#### **1. Set Up Apache2**
- Navigate to EC2 in the AWS Management Console.
- Select the webserver-01 instance and click Connect.
- Use EC2 Instance Connect to open a terminal window, or use your local terminal:
  ```bash
  ssh cloud_user@<PUBLIC_IP_ADDRESS>
  ```
- Update and install the packages:
  ```bash
  sudo apt-get update && sudo apt-get upgrade -y
  ```
- Install Apache2:
  ```bash
  sudo apt-get install apache2 -y
  ```
- Confirm the installation by accessing the public IP address of the instance in a web browser.

#### **2. Set Up the AWS CLI Tool**
- Install unzip:
  ```bash
  sudo apt-get install unzip -y
  ```
- Download and install the AWS CLI tool:
  ```bash
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
  ```
- Verify the installation:
  ```bash
  aws --version
  ```

#### **3. Edit the Web Page's index.html File**
- Grant access to the file:
  ```bash
  sudo chmod 777 /var/www/html/index.html
  ```
- Add instance metadata to the file:
  ```bash
  echo '<html><h1>Bootstrap Demo</h1><h3>Availability Zone: ' > /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/placement/availability-zone >> /var/www/html/index.html
  echo '</h3> <h3>Instance Id: ' >> /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/instance-id >> /var/www/html/index.html
  echo '</h3> <h3>Public IP: ' >> /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/public-ipv4 >> /var/www/html/index.html
  echo '</h3> <h3>Local IP: ' >> /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/local-ipv4 >> /var/www/html/index.html
  echo '</h3></html> ' >> /var/www/html/index.html
  ```
- Refresh the Apache web page to view the changes.

#### **4. Install MySQL**
- Install MySQL:
  ```bash
  sudo apt-get install mysql-server -y
  ```

# **Use a Bootstrap Script to Build webserver-02 and Debug Issues**

#### **1. Set Up the Script**
- In the EC2 dashboard, launch a new instance named webserver-02.
- Select Ubuntu Server 24.04 LTS (HVM), SSD Volume Type.
- Choose the t3.micro instance type.
- Enable Auto-assign public IP.
- Select an existing security group (EC2SecurityGroup).
- In Advanced details, paste the following bootstrap script:
```bash
  #!/bin/bash
  sudo apt-get update -y
  sudo apt-get install apache2 unzip -y
  sudo systemctl enable apache2
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
  echo '<html><h1>Bootstrap Demo</h1><h3>Availability Zone: ' > /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/placement/availability-zone >> /var/www/html/index.html
  echo '</h3> <h3>Instance Id: ' >> /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/instance-id >> /var/www/html/index.html
  echo '</h3> <h3>Public IP: ' >> /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/public-ipv4 >> /var/www/html/index.html
  echo '</h3> <h3>Local IP: ' >> /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/local-ipv4 >> /var/www/html/index.html
  echo '</h3></html> ' >> /var/www/html/index.html
  sudo apt-get install mysql-server
  sudo systemctl enable mysql
  ```
- Launch the instance.

#### **2. Debug Issues**
- Connect to webserver-02 and verify Apache installation:
  ```bash
  sudo systemctl status apache2
  ps aux | grep apache
  ```
- Verify MySQL installation and running status:
  ```bash
  sudo systemctl status mysql
  ps aux | grep mysql
  ```
- Install MySQL manually if needed:
  ```bash
  sudo apt-get install mysql-server -y
  sudo systemctl enable mysql
  ```

# **Use a Fixed Bootstrap Script to Build webserver-03**

#### **1. Set Up the Script**
- In the EC2 dashboard, launch a new instance named webserver-03.
- Select Ubuntu Server 24.04 LTS (HVM), SSD Volume Type.
- Choose the t3.micro instance type.
- Enable Auto-assign public IP.
- Select an existing security group (EC2SecurityGroup).
- In Advanced details, paste the corrected bootstrap script:

```bash
  #!/bin/bash
  sudo apt-get update -y
  sudo apt-get install apache2 unzip -y
  sudo systemctl enable apache2
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
  echo '<html><h1>Bootstrap Demo</h1><h3>Availability Zone: ' > /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/placement/availability-zone >> /var/www/html/index.html
  echo '</h3> <h3>Instance Id: ' >> /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/instance-id >> /var/www/html/index.html
  echo '</h3> <h3>Public IP: ' >> /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/public-ipv4 >> /var/www/html/index.html
  echo '</h3> <h3>Local IP: ' >> /var/www/html/index.html
  curl http://169.254.169.254/latest/meta-data/local-ipv4 >> /var/www/html/index.html
  echo '</h3></html> ' >> /var/www/html/index.html
  sudo apt-get install mysql-server -y
  sudo systemctl enable mysql
  ```
- Launch the instance.

#### **2. Connect to and Verify webserver-03**
- Connect to webserver-03 and verify Apache installation:
  ```bash
  sudo systemctl status apache2
  ps aux | grep apache
  ```
- Verify MySQL installation:
  ```bash
  systemctl status mysql
  ps aux | grep mysql
  ```
- Verify AWS CLI installation:
  ```bash
  aws --version
  ```


### **Conclusion**
Congratulations — you've successfully completed the hands-on lab and learned how to use EC2 bootstrap scripts to automate server configuration!