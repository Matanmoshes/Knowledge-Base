Here’s a step-by-step guide to help you copy the SSH public key from one EC2 Ubuntu instance to another EC2 Ubuntu instance and set up passwordless SSH access.

### **Step 1: Generate an SSH Key Pair**

You've already run the command to generate an SSH key:

```bash
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa
```

This command creates:
- A private key (`~/.ssh/id_rsa`)
- A public key (`~/.ssh/id_rsa.pub`)

### **Step 2: Copy the Public Key to the Second EC2 Instance**

Now, you need to copy the contents of the `id_rsa.pub` file (your public key) to the `~/.ssh/authorized_keys` file on the second EC2 instance.

1. **Display the Public Key**:
   
   On your first EC2 instance, display the contents of the `id_rsa.pub` file by running:

   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

   This will output your public key, something like:

   ```
   ssh-rsa AAAAB3Nza...your_public_key... user@hostname
   ```

2. **Log in to the Second EC2 Instance**:
   
   From your first EC2 instance, SSH into the second EC2 instance using the existing login credentials:

   ```bash
   ssh ubuntu@<second_ec2_ip_address>
   ```

3. **Create the `.ssh` Directory (if it doesn't exist)**:
   
   On the second EC2 instance, run the following commands to ensure the `.ssh` directory exists:

   ```bash
   mkdir -p ~/.ssh
   chmod 700 ~/.ssh
   ```

4. **Add the Public Key to `authorized_keys`**:
   
   Still on the second EC2 instance, run the following command to open the `authorized_keys` file:

   ```bash
   nano ~/.ssh/authorized_keys
   ```

   - Paste the public key that you copied from the first EC2 instance (`id_rsa.pub`).
   - Save the file and exit the editor (`Ctrl+O`, `Enter`, `Ctrl+X`).

5. **Set Permissions for `authorized_keys`**:
   
   Make sure the `authorized_keys` file has the correct permissions:

   ```bash
   chmod 600 ~/.ssh/authorized_keys
   ```

6. **Exit the Second EC2 Instance**:

   After copying the public key and setting permissions, exit the SSH session:

   ```bash
   exit
   ```

### **Step 3: Test SSH Access from the First EC2 to the Second EC2**

Now, back on the first EC2 instance, test the passwordless SSH access:

```bash
ssh ubuntu@<second_ec2_ip_address>
```

If everything is set up correctly, you should be able to SSH into the second EC2 instance without being prompted for a password.

### **Optional: Disable Password Authentication (For Security)**

Once the key-based authentication is working, you can disable password-based authentication on the second EC2 instance for added security:

1. Edit the SSH configuration file on the second EC2 instance:

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. Find and change the following lines to:

   ```bash
   PasswordAuthentication no
   PermitRootLogin no
   ```

3. Save the file and restart the SSH service:

   ```bash
   sudo systemctl restart ssh
   ```

Now your second EC2 instance will only allow key-based SSH authentication.

### **Conclusion**

You have successfully set up passwordless SSH access from one EC2 Ubuntu instance to another using SSH keys. This setup enhances security and convenience when managing multiple servers.