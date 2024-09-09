### Guide: Configuring UFW Firewall on a New Linux Machine (Including Restriction of Incoming Traffic)

When you log in to a new Linux machine, one of the most important security measures is setting up a firewall to restrict unauthorized access. **UFW (Uncomplicated Firewall)** is a user-friendly tool to manage firewall rules on Ubuntu and other Linux distributions. This guide covers the steps to set up UFW, including how to restrict **incoming traffic**.

---

### **Step 1: Update the System**

First, make sure your system is up to date:

```bash
sudo apt update && sudo apt upgrade -y
```

This ensures that your system has the latest security patches and software updates.

---

### **Step 2: Check if UFW is Installed**

UFW is pre-installed on most Ubuntu-based distributions. To check if UFW is installed, run:

```bash
sudo ufw status
```

If UFW is installed, it will return something like:

```bash
Status: inactive
```

If UFW is not installed, you can install it with:

```bash
sudo apt install ufw
```

---

### **Step 3: Allow SSH Connections**

Since you’ll likely be accessing the server remotely via SSH, it’s critical to allow SSH traffic **before** enabling UFW. Otherwise, you might lock yourself out of the server.

```bash
sudo ufw allow OpenSSH
```

This opens **port 22** (the default SSH port) and ensures you won’t lose access when UFW is enabled.

---

### **Step 4: Enable UFW and Deny All Incoming Traffic by Default**

Once you’ve allowed SSH, you can enable UFW and block all **incoming traffic** by default, while allowing outgoing traffic. UFW follows a default policy of **denying all incoming connections** and **allowing all outgoing connections**. To explicitly set these policies, run:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

This ensures that any incoming connections are denied unless explicitly allowed.

Now, enable UFW:

```bash
sudo ufw enable
```

You should see a message like:

```bash
Firewall is active and enabled on system startup
```

---

### **Step 5: Allow Necessary Incoming Traffic**

Now that the default policy blocks all incoming traffic, you need to explicitly allow any required services (in addition to SSH). Below are some common examples.

1. **Allow HTTP (Port 80)**:

   If your machine is a web server that needs to serve HTTP traffic:

   ```bash
   sudo ufw allow http
   ```

2. **Allow HTTPS (Port 443)**:

   To allow secure web traffic:

   ```bash
   sudo ufw allow https
   ```

3. **Allow a Specific Port**:

   If you have a custom service running on a specific port, you can allow it by specifying the port number (e.g., 8080 for a web application):

   ```bash
   sudo ufw allow 8080/tcp
   ```

4. **Allow a Specific IP Address**:

   If you want to allow traffic only from a trusted IP address:

   ```bash
   sudo ufw allow from <IP_address>
   ```

   Example:

   ```bash
   sudo ufw allow from 192.168.1.100
   ```

5. **Allow a Service from a Specific IP Address**:

   To allow traffic for a specific service (e.g., SSH) only from a specific IP:

   ```bash
   sudo ufw allow from <IP_address> to any port 22
   ```

   Example:

   ```bash
   sudo ufw allow from 192.168.1.100 to any port 22
   ```

---

### **Step 6: Restrict Specific Incoming Traffic**

UFW allows you to block or restrict certain types of incoming traffic as well.

1. **Deny Specific IP Address**:

   If you want to block all traffic from a specific IP:

   ```bash
   sudo ufw deny from <IP_address>
   ```

   Example:

   ```bash
   sudo ufw deny from 203.0.113.45
   ```

2. **Deny a Specific Port**:

   If you need to block traffic to a specific port:

   ```bash
   sudo ufw deny <port>
   ```

   Example:

   ```bash
   sudo ufw deny 8080/tcp
   ```

3. **Rate Limiting for SSH (Prevent Brute Force)**:

   To protect SSH from brute force attacks, you can enable rate limiting. This will block IP addresses that have too many connection attempts in a short time:

   ```bash
   sudo ufw limit OpenSSH
   ```

4. **Block Traffic by Protocol**:

   You can block traffic for a specific protocol (e.g., UDP or TCP):

   ```bash
   sudo ufw deny proto <protocol> from any to any port <port>
   ```

   Example (block UDP traffic on port 53):

   ```bash
   sudo ufw deny proto udp from any to any port 53
   ```

---

### **Step 7: Enable Logging for Incoming Traffic**

To monitor the firewall activity, you can enable logging. UFW offers different logging levels: `off`, `low`, `medium`, `high`, and `full`.

Enable logging with:

```bash
sudo ufw logging medium
```

This helps you monitor any blocked or allowed traffic on your system.

---

### **Step 8: Check and Manage UFW Rules**

You can view the current UFW rules at any time by running:

```bash
sudo ufw status numbered
```

This will display all active firewall rules with a number next to each. If you need to delete a rule, you can do so by referring to its number. For example:

```bash
sudo ufw delete <rule_number>
```

---

### **Step 9: Reload or Reset UFW (Optional)**

If you make changes to your firewall rules and want to reload the configuration, use:

```bash
sudo ufw reload
```

To completely reset UFW (remove all rules and disable it):

```bash
sudo ufw reset
```

---

### **Step 10: Verify UFW Settings**

After you’ve configured UFW, it’s always a good idea to verify that everything is working as expected. Check your firewall rules again:

```bash
sudo ufw status verbose
```

This will display detailed information about your UFW configuration, including which ports are open and which are restricted.

---

### **Conclusion**

Setting up UFW on a new machine is a crucial step in securing your server or computer. By following these steps, you ensure that all **incoming traffic is restricted** unless explicitly allowed. This minimizes the risk of unauthorized access and keeps your system secure. Remember to regularly review and update your firewall rules as your system's needs change.