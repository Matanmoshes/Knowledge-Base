## Guide for setting up a Jenkins build agent and configuring it to work with a Jenkins master (controller) on Ubuntu:

## **On the Build Agent Server (Ubuntu 24)**

### 1. **Install Java**
Jenkins requires Java to run. Install OpenJDK 11 or higher:

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
```

Verify the Java installation:

```bash
java -version
```

### 2. **Create Jenkins User**
To ensure security, create a dedicated `jenkins` user:

```bash
sudo useradd -m -s /bin/bash jenkins
sudo passwd jenkins
```

You can assign this user `sudo` privileges if needed:

```bash
sudo usermod -aG sudo jenkins
```

### 3. **Generate SSH Key for Jenkins User**
Generate an SSH key for the `jenkins` user on the build agent:

```bash
sudo su - jenkins
ssh-keygen -t rsa -b 4096
```

Copy the public key:

```bash
cat ~/.ssh/id_rsa.pub
```

### 4. **Install Git (Optional)**
If your build jobs require Git:

```bash
sudo apt install git -y
```

### 5. **Install Other Build Tools**
Install any build tools you need on the build agent, depending on your project requirements. For example:

- **Maven**:
   ```bash
   sudo apt install maven -y
   ```
- **Docker**:
   ```bash
   sudo apt install docker.io -y
   sudo usermod -aG docker jenkins
   ```
- **Python:**
```bash
sudo apt-get install python3 -y
sudo apt-get install pip -y
```

### 6. **Enable the Jenkins Agent to Connect via SSH**
Make sure SSH is running and the firewall allows it.

- To check if SSH is running:
   ```bash
   sudo systemctl status ssh
   ```

- If needed, enable and start it:
   ```bash
   sudo systemctl enable ssh
   sudo systemctl start ssh
   ```

- Open port 22 in UFW (if enabled):
   ```bash
   sudo ufw allow OpenSSH
   sudo ufw enable
   ```

### 7. **Install JDK and Tools (Optional)**
If you are running Java-based builds or require additional tools (e.g., Node.js, Python, etc.), install those on the agent machine now.

---

## **On the Jenkins Master (Controller)**

### 1. **Install Plugins**
To manage and connect to the build agent, install these plugins:
- **SSH Build Agents Plugin**
- **Node and Label Parameter Plugin**

1. Go to **Manage Jenkins > Manage Plugins**.
2. Search for and install the above plugins without restarting Jenkins.

### 2. **Add Build Agent Node in Jenkins**
1. Go to **Manage Jenkins > Manage Nodes and Clouds**.
2. Click **New Node** to add the build agent.
3. Configure the node:
   - **Name**: Choose a name for your agent (e.g., `Ubuntu-Agent`).
   - **Permanent Agent**.
   - **Remote root directory**: `/home/jenkins`.
   - **Labels**: Add labels for filtering jobs, such as `ubuntu-agent`.
   - **Usage**: Choose "Use this node as much as possible" if you want the agent to be used for every job unless specified otherwise.
   - **Launch method**: Select **Launch agents via SSH**.

4. **Configure SSH Details**:
   - **Host**: Provide the public IP address or hostname of the build agent (EC2 instance).
   - **Credentials**: Click `Add` next to **Credentials** and select **SSH Username with Private Key**.
     - **Username**: `jenkins`
     - **Private Key**: Choose **Enter directly** and paste the private key you generated on the agent server (`~/.ssh/id_rsa`).
     - **Passphrase**: Leave empty if you didn’t set a passphrase for the key.

5. Click **Save**.

### 3. **Test the Connection**
After configuring the node, Jenkins will try to connect to the build agent via SSH. If successful, you will see the node marked as connected (green dot).

---

## **Optional Configurations for Jenkins Jobs**

### 1. **Specify Node/Label for a Job**
To run a build on a specific agent, go to the job configuration:

1. Under **Restrict where this project can be run**, check the box.
2. Enter the label of the agent you configured (e.g., `ubuntu-agent`).

### 2. **Run a Test Build**
Create or trigger a simple job and verify if it runs on the build agent. You can view logs and details under **Build Executor Status** or the job's console output.

---

## **Final Checks and Maintenance**

### 1. **Firewall (Security Group on AWS)**
Ensure that port 22 (SSH) is open in the security group for your EC2 instance, and limit access to your Jenkins master’s IP.

### 2. **Monitor Resources**
To avoid any resource exhaustion, monitor CPU, memory, and disk usage on the build agent:

```bash
htop
df -h
```

### 3. **Install Additional Build Tools (if needed)**
If your builds require additional tools (e.g., Python, Node.js, Docker, etc.), ensure these are installed on the agent server.

---

With this setup, you now have a Jenkins build agent running on an Ubuntu 24 server, connected to your Jenkins master via SSH. You can assign jobs to run on this agent based on its labels or manually specify jobs to run on this agent.