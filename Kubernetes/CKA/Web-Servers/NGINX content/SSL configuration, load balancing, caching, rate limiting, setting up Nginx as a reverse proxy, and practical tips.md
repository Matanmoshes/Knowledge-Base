### Advanced Nginx Topics: Detailed Guide

This guide covers advanced Nginx topics, including SSL/TLS configuration, load balancing, caching, rate limiting, setting up Nginx as a reverse proxy, and practical tips for logging, monitoring, and security hardening.

### 1. SSL/TLS Configuration

**Objective:** Secure your website with HTTPS.

>[!Do You Still Need to Create Key and Certificate with OpenSSL?]
>**No, you do not need to manually create keys and certificates with OpenSSL if you are using Certbot with Let's Encrypt.**
>
Certbot automates the entire process of generating the private key, creating the Certificate Signing Request (CSR), obtaining the certificate from Let's Encrypt, and configuring your web server to use the certificate. This makes the process much simpler and less error-prone compared to manually creating keys and certificates with OpenSSL.
#### Steps:

1. **Obtain an SSL Certificate**

   You can get an SSL certificate from a Certificate Authority (CA) like Let's Encrypt.

2. **Install Certbot**

   Certbot is a tool that automates the process of obtaining and renewing SSL certificates from Let's Encrypt.

   ```bash
   sudo apt install certbot python3-certbot-nginx
   ```

3. **Obtain and Install the Certificate**

   Run the following command to obtain and install the certificate for your domain:

   ```bash
   sudo certbot --nginx -d your_domain
   ```

   This command will automatically configure Nginx to use the obtained certificate.

4. **Test SSL Configuration**

   Restart Nginx to apply the new configuration:

   ```bash
   sudo systemctl restart nginx
   ```

5. **Verify by Accessing HTTPS**

   Open your browser and visit `https://your_domain` to verify that the SSL certificate is working correctly.


### 2. Load Balancing

**Objective:** Distribute traffic across multiple servers.

#### Steps:

1. **Create a Configuration File for Load Balancing**

   Open or create a new configuration file in the `/etc/nginx/conf.d/` directory:

   ```bash
   sudo nano /etc/nginx/conf.d/load-balancing.conf
   ```

2. **Add Load Balancing Configuration**

   Add the following configuration to distribute requests across multiple backend servers:

   ```nginx
   upstream backend {
       server backend1.example.com;
       server backend2.example.com;
   }

   server {
       listen 80;
       server_name your_domain;

       location / {
           proxy_pass http://backend;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

3. **Test and Restart Nginx**

   Test the configuration for syntax errors and restart Nginx:

   ```bash
   sudo nginx -t
   sudo systemctl restart nginx
   ```

### 3. Caching

**Objective:** Improve performance by caching content.

#### Steps:

1. **Configure Caching in Nginx Configuration**

   Open or create a new configuration file in the `/etc/nginx/conf.d/` directory:

   ```bash
   sudo nano /etc/nginx/conf.d/caching.conf
   ```

2. **Add Caching Configuration**

   Add the following configuration to enable caching:

   ```nginx
   http {
       proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m max_size=1g inactive=60m use_temp_path=off;

       server {
           listen 80;
           server_name your_domain;

           location / {
               proxy_cache my_cache;
               proxy_pass http://backend;
               proxy_set_header Host $host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header X-Forwarded-Proto $scheme;
               add_header X-Proxy-Cache $upstream_cache_status;
           }
       }
   }
   ```

3. **Test and Restart Nginx**

   Test the configuration for syntax errors and restart Nginx:

   ```bash
   sudo nginx -t
   sudo systemctl restart nginx
   ```

### 4. Rate Limiting

**Objective:** Protect your site from DDoS attacks by limiting the number of requests.

#### Steps:

1. **Configure Rate Limiting**

   Open or create a new configuration file in the `/etc/nginx/conf.d/` directory:

   ```bash
   sudo nano /etc/nginx/conf.d/rate-limiting.conf
   ```

2. **Add Rate Limiting Configuration**

   Add the following configuration to enable rate limiting:

   ```nginx
   http {
       limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;

       server {
           listen 80;
           server_name your_domain;

           location / {
               limit_req zone=mylimit burst=20 nodelay;
               proxy_pass http://backend;
           }
       }
   }
   ```

3. **Test and Restart Nginx**

   Test the configuration for syntax errors and restart Nginx:

   ```bash
   sudo nginx -t
   sudo systemctl restart nginx
   ```

### 5. Setting Up Nginx as a Reverse Proxy

**Objective:** Use Nginx to forward client requests to another server.

#### Steps:

1. **Basic Reverse Proxy Configuration**

   Open or create a new configuration file in the `/etc/nginx/conf.d/` directory:

   ```bash
   sudo nano /etc/nginx/conf.d/reverse-proxy.conf
   ```

2. **Add Reverse Proxy Configuration**

   Add the following configuration to set up Nginx as a reverse proxy:

   ```nginx
   server {
       listen 80;
       server_name your_domain;

       location / {
           proxy_pass http://127.0.0.1:3000;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

3. **Test and Restart Nginx**

   Test the configuration for syntax errors and restart Nginx:

   ```bash
   sudo nginx -t
   sudo systemctl restart nginx
   ```

### Practical Tips

#### 1. Logging and Monitoring

**Objective:** Keep track of access and errors for troubleshooting and analytics.

##### Steps:

1. **Configure Access and Error Logs**

   Open your Nginx configuration file and add the logging configuration:

   ```nginx
   server {
       listen 80;
       server_name your_domain;

       access_log /var/log/nginx/access.log;
       error_log /var/log/nginx/error.log;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

2. **View Logs**

   To view the logs in real-time, use the following command:

   ```bash
   sudo tail -f /var/log/nginx/access.log /var/log/nginx/error.log
   ```

#### 2. Security Hardening

**Objective:** Protect your Nginx server from common security threats.

##### Tips:

1. **Disable Server Tokens**

   This hides the Nginx version number in error pages and other responses:

   ```nginx
   http {
       server_tokens off;
   }
   ```

2. **Restrict Access to Certain Locations**

   Limit access to specific IP ranges for sensitive areas:

   ```nginx
   location /admin {
       allow 192.168.1.0/24;
       deny all;
   }
   ```

3. **Enable HTTP Security Headers**

   Add security headers to your responses:

   ```nginx
   add_header X-Content-Type-Options nosniff;
   add_header X-Frame-Options "SAMEORIGIN";
   add_header X-XSS-Protection "1; mode=block";
   ```

#### 3. Automating Nginx with Ansible

**Objective:** Use Ansible to automate Nginx configuration and management.

##### Simple Ansible Playbook Example

Create a playbook `nginx-playbook.yml`:

```yaml
---
- name: Configure Nginx
  hosts: webservers
  become: yes

  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Copy Nginx configuration file
      copy:
        src: ./nginx.conf
        dest: /etc/nginx/nginx.conf

    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
```

### Useful Resources

1. **Nginx Documentation**

   Official Nginx documentation: [nginx.org](https://nginx.org/en/docs/)

This detailed guide covers advanced Nginx topics and provides comprehensive steps for setting up and configuring Nginx for SSL/TLS, load balancing, caching, rate limiting, reverse proxying, logging, monitoring, and security hardening.