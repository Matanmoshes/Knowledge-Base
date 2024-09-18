## EC2 User Data Script for Docker and Jenkins Setup on Amazon Linux 2

```bash
#!/bin/bash
# This is the EC2 User Data script for Amazon Linux 2 to install Docker and run Jenkins in a Docker container

# Update the system
sudo yum update -y

# Install Docker from Amazon Linux Extras
sudo amazon-linux-extras install docker -y

# Start Docker service and enable it to run on startup
sudo systemctl start docker
sudo systemctl enable docker

# Add the ec2-user to the Docker group so that we don't need to use sudo with Docker commands
sudo usermod -aG docker ec2-user

# Wait for usermod changes to take effect
newgrp docker

# Create Docker network for Jenkins
docker network create jenkins

# Pull the Jenkins BlueOcean Docker image
docker pull jenkins/jenkins:2.414.2-jdk11

# Run the Jenkins container
docker run --name jenkins-blueocean --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  jenkins/jenkins:2.414.2-jdk11

# Wait for Jenkins to fully start
sleep 30

# Install recommended plugins inside the Jenkins container
docker exec jenkins-blueocean jenkins-plugin-cli --plugins \
  "blueocean:1.25.3 docker-workflow:1.28 \
  git workflow-aggregator credentials-binding github ssh-agent \
  pipeline-stage-view email-ext matrix-auth ansicolor slack"

# Get the initial Jenkins admin password and store it in a file for easy access
docker exec jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword > /home/ec2-user/jenkins_admin_password

# Display where the Jenkins password can be found
echo "Jenkins initial admin password is stored in /home/ec2-user/jenkins_admin_password"
echo "Access Jenkins at http://<instance-public-ip>:8080"

```

---


###  **Additional Jenkins Plugins**: The following plugins have been added to the script:

- **Git Plugin**
- **Pipeline Plugin (workflow-aggregator)**
- **Credentials Binding Plugin**
- **GitHub Plugin**
- **SSH Agent Plugin**
- **Pipeline Stage View Plugin**
- **Email Extension Plugin**
- **Matrix Authorization Plugin**
- **AnsiColor Plugin**
- **Slack Notification Plugin**

---

## How to Use the User Data Script:

1. **Launch a New EC2 Instance**:
   - Go to the **AWS Management Console** > **EC2 Dashboard** > **Launch Instance**.

2. **Choose an AMI**:
   - Select **Amazon Linux 2 AMI (HVM) - Kernel 5.10** as the AMI.

3. **Configure Instance Details**:
   - Scroll down to **Advanced Details**.
   - Copy the above user data script and paste it into the **User Data** field.

4. **Select Instance Type**:
   - Choose an instance type, such as `t2.medium` or `t2.large`, to ensure Jenkins has enough resources.

5. **Add Storage**:
   - Use the default storage options or modify them as needed.

6. **Configure Security Group**:
   - Ensure that the security group allows:
     - **Port 22** for SSH access.
     - **Port 8080** for Jenkins access.
     - **Port 50000** for Jenkins agents.

7. **Launch the Instance**.

---
### Post-Launch:

1. **Access Jenkins**:
   - Once the instance is running, access **Jenkins** in your browser:
     ```
     http://<EC2-Public-IP>:8080
     ```

2. **Retrieve Jenkins Initial Admin Password**:
   - The initial Jenkins admin password will be stored in the file `/home/ec2-user/jenkins_admin_password`.
   - SSH into the instance to view it:
     ```bash
     ssh -i /path/to/your-key.pem ec2-user@<EC2-Public-IP>
     cat /home/ec2-user/jenkins_admin_password
     ```

