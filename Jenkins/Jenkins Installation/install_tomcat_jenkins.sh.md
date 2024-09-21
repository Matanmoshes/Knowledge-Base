
### Script: `install_tomcat_jenkins.sh`

```bash
#!/bin/bash

# Update system
echo "Updating system packages..."
sudo apt update -y

# Step 1: Install Java (OpenJDK 11)
echo "Installing OpenJDK 11..."
sudo apt install -y openjdk-11-jdk

# Step 2: Create Tomcat User
echo "Creating Tomcat user..."
sudo useradd -m -U -d /opt/tomcat -s /bin/false tomcat

# Step 3: Download and Extract Tomcat
TOMCAT_VERSION="9.0.81"  # Set the Tomcat version you need
echo "Downloading and extracting Tomcat..."
wget https://archive.apache.org/dist/tomcat/tomcat-9/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz

# If /opt/tomcat already exists, remove it
if [ -d "/opt/tomcat" ]; then
    sudo rm -rf /opt/tomcat
fi

# Extract and move Tomcat to /opt/tomcat
tar -xvf apache-tomcat-${TOMCAT_VERSION}.tar.gz
sudo mv apache-tomcat-${TOMCAT_VERSION} /opt/tomcat

# Make all shell scripts executable
sudo chmod +x /opt/tomcat/bin/*.sh

# Step 4: Set Permissions
echo "Setting permissions for Tomcat user..."
sudo chown -R tomcat:tomcat /opt/tomcat

# Step 5: Modify permissions for /opt/tomcat/conf/ to allow script access
echo "Modifying permissions for /opt/tomcat/conf/..."
sudo chmod 755 /opt/tomcat/conf/

# Step 6: Configure Tomcat Users
echo "Configuring Tomcat users..."
sudo bash -c 'cat <<EOF > /opt/tomcat/conf/tomcat-users.xml
<tomcat-users>
    <role rolename="admin-gui"/>
    <role rolename="manager-gui"/>
    <user username="admin" password="a1a1a1" roles="admin-gui,manager-gui"/>
</tomcat-users>
EOF'

# Step 7: Allow Manager and Host-Manager Access
echo "Configuring access to Manager and Host-Manager..."
sudo sed -i 's/<Valve className="org.apache.catalina.valves.RemoteAddrValve".*/<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\\.\d+\\.\d+\\.\d+|::1|0:0:0:0:0:0:0:1" \/> -->/' /opt/tomcat/webapps/manager/META-INF/context.xml

sudo sed -i 's/<Valve className="org.apache.catalina.valves.RemoteAddrValve".*/<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\\.\d+\\.\d+\\.\d+|::1|0:0:0:0:0:0:0:1" \/> -->/' /opt/tomcat/webapps/host-manager/META-INF/context.xml

# Step 8: Configure Tomcat as a Service
echo "Creating systemd service for Tomcat..."
sudo bash -c 'cat <<EOF > /etc/systemd/system/tomcat.service
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment=JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
Environment=CATALINA_PID=/opt/tomcat/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

ExecReload=/bin/kill -s HUP \$MAINPID
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOF'

# Step 9: Reload systemd, enable and start Tomcat service
echo "Reloading systemd and enabling Tomcat service..."
sudo systemctl daemon-reload
sudo systemctl enable tomcat
sudo systemctl start tomcat

# Verify if the service started correctly
if sudo systemctl status tomcat | grep -q "active (running)"; then
    echo "Tomcat started successfully."
else
    echo "Tomcat failed to start. Please check the logs."
    sudo journalctl -u tomcat
fi

# Step 10: Install Jenkins
echo "Downloading Jenkins WAR file..."
wget https://get.jenkins.io/war-stable/2.462.2/jenkins.war -P /opt/tomcat/webapps/

# Restart Tomcat to load Jenkins
echo "Restarting Tomcat to load Jenkins..."
sudo systemctl restart tomcat

# Step 11: Retrieve Jenkins initial admin password
echo "Jenkins setup completed. Retrieving initial admin password..."
if [ -f /opt/tomcat/.jenkins/secrets/initialAdminPassword ]; then
    sudo cat /opt/tomcat/.jenkins/secrets/initialAdminPassword
else
    echo "Jenkins is starting, try again in a few minutes."
fi

echo "Visit Jenkins at http://<your-server-ip>:8080/jenkins"


```


---
## How to Use the Script:

1. **Create the script file**:
   - Create a new file, for example, `install_tomcat_jenkins.sh`:
     ```bash
     nano install_tomcat_jenkins.sh
     ```

2. **Paste the script into the file**:
   - Copy the above script and paste it into the file.

3. **Save and exit**:
   - Press `CTRL + O` to save the file, and `CTRL + X` to exit the editor.

4. **Make the script executable**:
   - Run the following command to make the script executable:
     ```bash
     chmod +x install_tomcat_jenkins.sh
     ```

5. **Run the script**:
   - Execute the script to install Apache Tomcat and Jenkins:
     ```bash
     ./install_tomcat_jenkins.sh
     ```


---

## What the Script Does:

1. **Installs OpenJDK 11**: Tomcat requires Java to run, so OpenJDK 11 is installed.
2. **Creates a User for Tomcat**: A user `tomcat` is created without login permissions for security purposes.
3. **Downloads and Installs Tomcat**: Downloads the Tomcat 9 archive, extracts it, and moves it to `/opt/tomcat`.
4. **Sets Permissions**: Assigns ownership of Tomcat files to the `tomcat` user.
5. **Configures Tomcat as a Service**: Creates a systemd service file to manage Tomcat as a service.
6. **Configures Tomcat Users**: Adds admin user credentials to access the Tomcat manager and admin interfaces.
7. **Configures Manager and Host-Manager Access**: Modifies Tomcat's `context.xml` files to allow access to the manager and host-manager applications from remote IPs.
8. **Installs Jenkins**: Downloads the Jenkins WAR file into the Tomcat `webapps` directory.
9. **Retrieves Jenkins Admin Password**: The script fetches the initial Jenkins admin password so you can complete the setup via the web interface.

Now, you can visit Jenkins at `http://<your-server-ip>:8080/jenkins` and use the retrieved password to complete the setup.

---


## Here’s the revised **user data** script:

### EC2 User Data Script for Ubuntu 24.04 (Tomcat and Jenkins Setup)

```bash
#!/bin/bash

# Update system
echo "Updating system packages..."
yum update -y

# Step 1: Install Java (OpenJDK 11)
echo "Installing OpenJDK 11..."
yum install -y java-11-openjdk

# Step 2: Create Tomcat User
echo "Creating Tomcat user..."
useradd -m -U -d /opt/tomcat -s /bin/false tomcat

# Step 3: Download and Extract Tomcat
TOMCAT_VERSION="9.0.81"  # Set the Tomcat version you need
echo "Downloading and extracting Tomcat..."
wget https://archive.apache.org/dist/tomcat/tomcat-9/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz

# If /opt/tomcat already exists, remove it
if [ -d "/opt/tomcat" ]; then
    rm -rf /opt/tomcat
fi

# Extract and move Tomcat to /opt/tomcat
tar -xvf apache-tomcat-${TOMCAT_VERSION}.tar.gz
mv apache-tomcat-${TOMCAT_VERSION} /opt/tomcat

# Make all shell scripts executable
chmod +x /opt/tomcat/bin/*.sh

# Step 4: Set Permissions
echo "Setting permissions for Tomcat user..."
chown -R tomcat:tomcat /opt/tomcat

# Step 5: Configure Tomcat as a Service
echo "Creating systemd service for Tomcat..."
cat <<EOF > /etc/systemd/system/tomcat.service
[Unit]
Description=Apache Tomcat
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment=JAVA_HOME=/usr/lib/jvm/java-11-openjdk
Environment=CATALINA_PID=/opt/tomcat/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

ExecReload=/bin/kill -s HUP \$MAINPID
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOF

# Reload systemd and enable the Tomcat service
echo "Reloading systemd and enabling Tomcat service..."
systemctl daemon-reload
systemctl enable tomcat
systemctl start tomcat

# Step 6: Configure Tomcat Users
echo "Configuring Tomcat users..."
cat <<EOF > /opt/tomcat/conf/tomcat-users.xml
<tomcat-users>
    <role rolename="admin-gui"/>
    <role rolename="manager-gui"/>
    <user username="admin" password="a1a1a1" roles="admin-gui,manager-gui"/>
</tomcat-users>
EOF

# Step 7: Allow Manager and Host-Manager Access
echo "Configuring access to Manager and Host-Manager..."
sed -i 's/<Valve className="org.apache.catalina.valves.RemoteAddrValve".*/<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\\.\d+\\.\d+\\.\d+|::1|0:0:0:0:0:0:0:1" \/> -->/' /opt/tomcat/webapps/manager/META-INF/context.xml

sed -i 's/<Valve className="org.apache.catalina.valves.RemoteAddrValve".*/<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\\.\d+\\.\d+\\.\d+|::1|0:0:0:0:0:0:0:1" \/> -->/' /opt/tomcat/webapps/host-manager/META-INF/context.xml

# Restart Tomcat to apply the changes
echo "Restarting Tomcat..."
systemctl restart tomcat

# Step 8: Install Jenkins
echo "Downloading Jenkins WAR file..."
wget https://get.jenkins.io/war-stable/2.462.2/jenkins.war -P /opt/tomcat/webapps/

# Restart Tomcat to load Jenkins
echo "Restarting Tomcat to load Jenkins..."
systemctl restart tomcat

# Step 9: Retrieve Jenkins initial admin password
echo "Jenkins setup completed. Retrieving initial admin password..."
if [ -f /opt/tomcat/.jenkins/secrets/initialAdminPassword ]; then
    cat /opt/tomcat/.jenkins/secrets/initialAdminPassword
else
    echo "Jenkins is starting, try again in a few minutes."
fi

echo "Visit Jenkins at http://<your-server-ip>:8080/jenkins"

```

### How to Use this User Data Script

1. **Launch a New EC2 Instance**:
   - In the **AWS Management Console**, go to **EC2 Dashboard** and click on **Launch Instance**.

2. **Choose an AMI**:
   - Choose **Ubuntu 24.04** as the AMI (if available, or the latest Ubuntu version).

3. **Configure Instance Details**:
   - In the **Advanced Details** section, copy and paste the above script into the **User Data** field.

4. **Select Instance Type**:
   - Select an instance type such as `t2.medium` or `t2.large` to ensure enough resources for Jenkins and Tomcat.

5. **Add Storage**:
   - Use the default storage options or increase based on your needs.

6. **Configure Security Group**:
   - Ensure the security group allows:
     - **Port 22** for SSH.
     - **Port 8080** for accessing Jenkins.
     - **Port 50000** for Jenkins agent communication if necessary.

7. **Launch the Instance**.

### After Launch:

1. **Access Jenkins**:
   - Once the instance is running, access **Jenkins** in your browser at:
     ```
     http://<EC2-Public-IP>:8080/jenkins
     ```

2. **Retrieve the Initial Admin Password**:
   - SSH into your instance using the EC2 keypair:
     ```bash
     ssh -i /path/to/key.pem ubuntu@<EC2-Public-IP>
     ```
   - View the Jenkins admin password:
     ```bash
     cat /home/ubuntu/jenkins_admin_password
     ```

This script fully automates the installation of OpenJDK, Apache Tomcat, Jenkins, and the necessary configurations. Let me know if you need any additional adjustments!