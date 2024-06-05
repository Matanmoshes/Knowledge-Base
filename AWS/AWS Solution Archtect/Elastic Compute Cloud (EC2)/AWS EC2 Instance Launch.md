### AWS EC2 Instance Launch - Key Points

#### Introduction
- **Objective**: Learn how to launch an EC2 instance.
- **AWS Console**: Access the EC2 service under the Compute section.

#### Step-by-Step Process

1. **Accessing EC2 Service**:
   - Navigate to the AWS console.
   - Select the **Northern Virginia region** (or your preferred region).
   - Go to **Compute** > **EC2**.

2. **Launching an Instance**:
   - Click on **Launch Instance**.
   - **Name the Instance**: Example - MyWebServer1.

3. **Selecting an AMI (Amazon Machine Image)**:
   - Choose **Amazon Linux AMI** (free tier eligible).
   - Other options: macOS, Ubuntu, Windows, Red Hat, etc.

4. **Choosing Instance Type**:
   - Default: **t2.micro** (1 vCPU, 1 GB memory).
   - For larger instances: Example - c5.18xlarge (72 vCPUs, 144 GB memory).

5. **Creating a Key Pair**:
   - Name the key pair (e.g., MyKeyPair).
   - Download the key pair file (.pem).

6. **Configuring Network Settings**:
   - **Security Group**: Virtual firewall for the instance.
   - Create a new security group (e.g., launch-wizard-1).
   - Allow **SSH (port 22)**, **HTTPS (port 443)**, and **HTTP (port 80)** traffic.

7. **Configuring Storage**:
   - Default: 8 GB **gp3** (General Purpose SSD).
   - Adjust storage size if needed.

8. **Launching the Instance**:
   - Click **Launch Instance**.
   - View the instance state (initially pending).

#### Connecting to the Instance
1. **Instance Details**:
   - View instance details, security group settings, and public IP address.
   - Example: Public IP address for web server setup.

2. **Connecting**:
   - Select the instance and click **Connect**.
   - Use **EC2 Instance Connect** for browser-based access.
   - Alternatively, use terminal (Mac) or Putty (Windows) for SSH access.

3. **Instance Management**:
   - **Elevate Privileges**: Use `sudo su` to become root.
   - **Update System**: Run `dnf update -y`.
   - **Disconnect**: Type `exit` to log out.

#### Terminating the Instance
1. **Terminate Instance**:
   - Select the instance and choose **Terminate**.
   - Instance state will change to **shutting down** and then **terminated**.

2. **Observations**:
   - Note the availability zone (e.g., us-east-1c).
   - Understand high availability architecture using multiple availability zones.

#### Summary
- **First EC2 Instance**: Successfully launched and terminated.
- **Next Steps**: Proceed to the next lecture or reach out with any questions.

This summary provides a comprehensive guide to launching an EC2 instance in AWS, essential for hands-on experience in managing cloud infrastructure.