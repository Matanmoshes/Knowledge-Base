# guide: installing Apache Tomcat on Ubuntu 22.04 and setting up Jenkins using Tomcat

---

## Step 1: Install Java (OpenJDK 11)

Tomcat requires Java to run. Install OpenJDK 11 using the following command:

```bash
sudo apt install openjdk-11-jdk
```

To verify the Java installation, use:

```bash
java --version
```

You should see an output similar to:

```bash
openjdk 11.0.24 2024-07-16
OpenJDK Runtime Environment (build 11.0.24+8-post-Ubuntu-1ubuntu322.04)
OpenJDK 64-Bit Server VM (build 11.0.24+8-post-Ubuntu-1ubuntu322.04, mixed mode, sharing)
```

---

## Step 2: Create a User for Tomcat

To ensure Tomcat runs securely, create a dedicated user without login permissions:

```bash
sudo useradd -m -U -d /opt/tomcat -s /bin/false tomcat
```

This command creates the `tomcat` user with the home directory set to `/opt/tomcat`.

---

## Step 3: Download and Extract Tomcat

Download Apache Tomcat 9:

```bash
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.94/bin/apache-tomcat-9.0.94.tar.gz
```

Extract the downloaded file:

```bash
tar -xvf apache-tomcat-9.0.94.tar.gz
```

Move the extracted files to `/opt/tomcat`:

```bash
sudo mv apache-tomcat-9.0.94/* /opt/tomcat
```

Verify the directory contents:

```bash
sudo ls /opt/tomcat
```

---

## Step 4: Set Permissions

Give ownership of the Tomcat files to the `tomcat` user:

```bash
sudo chown -R tomcat:tomcat /opt/tomcat
```

Verify the permissions:

```bash
sudo ls -lah /opt/tomcat/
```

You should see that `tomcat` is the owner of all files in this directory.

---

## Step 5: Configure Tomcat Service

Create a systemd service file for Tomcat:

```bash
sudo nano /etc/systemd/system/tomcat.service
```

Add the following content:

```bash
[Unit]
Description=Apache Tomcat
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

ExecReload=/bin/kill $MAINPID
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

---

## Step 6: Enable and Start Tomcat

Reload systemd to apply the new service:

```bash
sudo systemctl daemon-reload
```

Enable Tomcat to start at boot:

```bash
sudo systemctl enable tomcat
```

Start the Tomcat service:

```bash
sudo systemctl start tomcat
```

Check the service status:

```bash
sudo systemctl status tomcat
```

You should see that Tomcat is running.

---

## Step 7: Configure Tomcat Users

Backup the `tomcat-users.xml` file before editing:

```bash
sudo cp /opt/tomcat/conf/tomcat-users.xml /opt/tomcat/conf/tomcat-users.xml.bak
```

Edit the `tomcat-users.xml` file to add an admin user:

```bash
sudo nano /opt/tomcat/conf/tomcat-users.xml
```

Add the following lines before `</tomcat-users>`:

```xml
<role rolename="admin-gui"/>
<role rolename="manager-gui"/>
<user username="admin" password="a1a1a1" roles="admin-gui,manager-gui"/>
```

Save the file and restart Tomcat:

```bash
sudo systemctl restart tomcat
```

---

### Step 8: Configure Manager and Host-Manager Access

Backup the `context.xml` files for both the Manager and Host-Manager:

```bash
sudo cp /opt/tomcat/webapps/manager/META-INF/context.xml /opt/tomcat/webapps/manager/META-INF/context.xml.bak
sudo cp /opt/tomcat/webapps/host-manager/META-INF/context.xml /opt/tomcat/webapps/host-manager/META-INF/context.xml.bak
```

Edit both files to allow access to the Manager and Host-Manager web apps:

```bash
sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
```

Comment out the following section by adding `<!--` and `-->` around it:

```xml
<!--
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->
```

Repeat this for the `host-manager`:

```bash
sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
```

Restart Tomcat:

```bash
sudo systemctl restart tomcat
```

---

## Step 9: Install Jenkins

Download the Jenkins WAR file:

```bash
wget https://get.jenkins.io/war-stable/2.462.2/jenkins.war -P /opt/tomcat/webapps/
```

Start Jenkins by restarting Tomcat:

```bash
sudo systemctl restart tomcat
```

Access Jenkins via `http://<server-ip>:8080/jenkins`.

---

## Step 10: Retrieve Initial Admin Password

![[Pasted image 20240916204944.png]]

![[Pasted image 20240916205641.png]]

Retrieve the initial admin password for Jenkins:

```bash
sudo cat /opt/tomcat/.jenkins/secrets/initialAdminPassword
```

Use this password to complete the Jenkins setup via the web interface.


---

### Reference:

https://cloudinfrastructureservices.co.uk/how-to-install-apache-tomcat-server-on-ubuntu-22-04/





