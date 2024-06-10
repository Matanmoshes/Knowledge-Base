### <span style="color:#ffc000">EC2 Metadata and User Data - Key Points
</span>
#### <span style="color:#ffc000">Introduction</span>
- **Objective**: Understand EC2 metadata and user data.
- **Components**:
  - Definition of EC2 metadata.
  - Retrieving metadata.
  - Using user data to save metadata.
  - Demonstration.
  - Exam tips.

#### <span style="color:#ffc000">What is EC2 Metadata?</span>
- **Definition**: Data about your EC2 instance.
- **Examples**: Private IP address, public IP address, MAC address, host name, security groups.
- **Purpose**: Provides information about the instance for various uses.

#### <span style="color:#ffc000">Retrieving Metadata</span>
- **Command**: Previously a simple curl command to an IP address.
  - Old Command: `curl http://169.254.169.254/latest/meta-data/`
  - **Current Command**: More complex, not required for the exam.
- **Exam Focus**: Understanding the concept of metadata rather than specific commands.

#### <span style="color:#ffc000">Using User Data to Save Metadata</span>
- **User Data**: Bootstrap scripts run when the instance first boots.
- **Combining User Data and Metadata**:
  - **Example Script**:
    ```bash
#!/bin/bash
# When connecting to the instance in your browser, make sure to specify:
# http://[Public IP address]

# Bootstrap script for an Amazon Linux 2 instance:

#!/bin/bash
yum update -y
yum install httpd -y
systemctl start httpd
systemctl enable httpd
cd /var/www/html
echo "<html><body><h1>My IP is" > index.html 
curl http://169.254.169.254/latest/meta-data/public-ipv4 >> index.html
echo "</h1></body></html>" >> index.html

#===============================================================================

# Bootstrap script for an Amazon Linux 2023 instance:

#!/bin/bash
yum update -y
yum install httpd -y
systemctl start httpd
systemctl enable httpd
cd /var/www/html
echo "<html><body><h1>My IP is" > index.html 
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
PUBLIC_IP=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/public-ipv4)
echo "$PUBLIC_IP" >> index.html
echo "</h1></body></html>" >> index.html
    ```
  
  - **Purpose**: Updates packages, installs and starts Apache, and creates a webpage displaying the public IP address.

#### <span style="color:#ffc000">Demonstration</span>

1. **Launch an EC2 Instance**:
   - Navigate to **EC2** in the AWS console.
   - Launch a new instance named **MyWebData**.
   - Use **Amazon Linux 2 AMI** and **t2.micro** instance type.
   - Select an existing security group (e.g., launch-wizard-1).
   - Paste the user data script into the **User data** field under Advanced details.
   - Launch the instance with a key pair.

2. **Verify the Setup**:
   - Wait for the instance to be running.
   - Connect to the instance using **EC2 Instance Connect**.
   - Elevate privileges with `sudo su`.
   - Navigate to `/var/www/html` and check for `index.html`.
   - Confirm the webpage displays the public IP address by accessing the instance's public IP in a web browser.

#### <span style="color:#ffc000">Exam Tips</span>

1. **User Data vs. Metadata**:
   - **User Data**: Bootstrap scripts for tasks like installing software, starting services, and creating files.
   - **Metadata**: Information about the EC2 instance, such as IP addresses and security groups.
   - **Usage**: User data can access and use metadata.

2. **Important IP Address**:
   - **Metadata IP**: `169.254.169.254`. Previously popular in exam questions but less so now.
   
3. **Command Knowledge**:
   - **Solutions Architect Exam**: Focus on understanding the concepts of user data and metadata, rather than specific commands.
   - **Developer Exam**: May require knowledge of specific commands.

This summary provides a comprehensive guide to understanding EC2 metadata and user data, essential for managing and automating EC2 instances in AWS, and prepares you for the AWS Certified Solutions Architect - Associate exam.