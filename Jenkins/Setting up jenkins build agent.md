# **Setting up jenkins build agent**


### **Step 1: Generate SSH Key Pair on the Jenkins Master**

1. On the Jenkins master machine, generate an SSH key pair:

   ```bash
   ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa
   ```

   This will generate two files:
   - `~/.ssh/id_rsa` (Private key)
   - `~/.ssh/id_rsa.pub` (Public key)

---

### **Step 2: Copy the Public Key to the Jenkins Build Agent machine**

1. **Copy the public key to the build agent (slave machine)**:
   On the Jenkins master, use the `scp` command to copy the `id_rsa.pub` key to the build agent (replace `<build_agent_ip>` with your second EC2 instance's IP):

   ```bash
   ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@<build_agent_ip>
   ```

2. **Manually add the public key if needed**:
   If `ssh-copy-id` is unavailable, log in to the build agent:

   ```bash
   ssh ubuntu@<build_agent_ip>
   ```

   Then, run the following commands:

   - Create the `.ssh` directory (if it doesn't exist):

     ```bash
     mkdir -p ~/.ssh
     chmod 700 ~/.ssh
     ```

   - Append the public key to `authorized_keys`:

     ```bash
     echo "your_public_key" >> ~/.ssh/authorized_keys
     chmod 600 ~/.ssh/authorized_keys
     ```


---

### **Step 3: Create a Directory for Jenkins on the Build Agent**

1. On the build agent, create a directory to store Jenkins files:

   ```bash
   mkdir ~/jenkins-slave
   ```

   This directory will be used as the remote root directory for Jenkins jobs.


---

### **Step 4: Configure Jenkins Master Settings**

1. **Set Jenkins URL**:
   - Go to **Manage Jenkins** -> **Configure System**.
   - Set **Jenkins URL** to: `http://<jenkins_master_ip>:8080/jenkins/`.

2. **Set Jenkins Security for Port**:
   - Go to **Manage Jenkins** -> **Configure Global Security**.
   - Set the **Agent TCP port for JNLP agents** to `50000` (or any port you prefer).


---

### **Step 5: Add SSH Credentials in Jenkins**

1. **Add Jenkins Credentials**:
   - In Jenkins, navigate to **Manage Jenkins** -> **Manage Credentials** -> **Global credentials (unrestricted)**.
   - Click **Add Credentials** and set the following fields:
     - **Kind**: `SSH Username with private key`
     - **Scope**: `Global (Jenkins, nodes, items, all child items, etc)`
     - **ID**: `jenkins-slave-keys-2` (or any custom ID)
     - **Description**: `jenkins-slave-keys-2`
     - **Username**: `ubuntu`
     - **Private Key**: Choose `Enter directly`, and paste the private key content from `~/.ssh/id_rsa` (on the Jenkins master).
     - **Passphrase**: Leave empty if you didn’t set one.

2. **Click Add to save the credentials.**


![image](https://github.com/user-attachments/assets/81f9e9c3-8673-4690-9b0a-d1e50ccd646b)


---

### **Step 6: Create a New Jenkins Node**

1. **Create New Node**:
   - Go to **Manage Jenkins** -> **Manage Nodes and Clouds** -> **New Node**.
   - Enter the following details:
     - **Node Name**: `docker-slave-2` (or any custom name)
     - **Description**: `Slave docker on Ubuntu for build`
     - **Number of Executors**: `1`
     - **Remote Root Directory**: `/home/ubuntu/jenkins-slave`
     - **Labels**: `docker-slave-2` (optional, used to assign jobs to specific nodes)
     - **Usage**: `Only build jobs with label expressions matching this node`
   
2. **Set Launch Method**:
   - **Launch method**: `Launch agents via SSH`
   - **Host**: `<build_agent_ip>`
   - **Credentials**: Select the credentials you just added (`ubuntu (jenkins-slave-keys-2)`).
   
3. **Set Host Key Verification Strategy**:
   - **Host Key Verification Strategy**: Set to `Non-verifying Verification Strategy` (optional, but useful if you encounter host key issues).

4. **Set Availability**:
   - Set **Availability** to `Keep this agent online as much as possible`.

5. **Click Save.**

---

![image](https://github.com/user-attachments/assets/a5d56b73-0a69-4ab5-93f6-683458f587df)

![image](https://github.com/user-attachments/assets/709d7e0c-f263-4ac4-90a9-f5f752ed720e)


---

### **Step 7: Test the Connection**

Once you save the node configuration, Jenkins will attempt to connect to the build agent using SSH. You can check the connection logs to ensure everything is set up correctly.

If the connection is successful, the new build agent will appear online, and you can begin assigning jobs to it.

---

### **Summary**

- **Step 1**: Generate SSH keys on the Jenkins master.
- **Step 2**: Copy the public key to the build agent.
- **Step 3**: Create a directory for Jenkins on the build agent.
- **Step 4**: Configure Jenkins master settings (URL and security).
- **Step 5**: Add SSH credentials in Jenkins.
- **Step 6**: Create and configure a new Jenkins node (build agent).
- **Step 7**: Test the connection and start running jobs on the new build agent.

