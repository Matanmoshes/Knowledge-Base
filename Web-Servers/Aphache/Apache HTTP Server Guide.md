### Detailed Guide to Apache HTTP Server

**Introduction**
The Apache HTTP Server, commonly referred to as Apache, is a widely-used open-source web server software that allows you to serve web content over the internet. This guide covers the essential aspects of Apache that are important to know for effective use and management.

### Installation and Configuration

**1. Installation:**
- **On Debian/Ubuntu:**
  ```bash
  sudo apt-get update
  sudo apt-get install apache2 -y
  ```
- **On CentOS/RHEL:**
  ```bash
  sudo yum update
  sudo yum install httpd -y
  ```

**2. Start and Enable Apache:**
- **On Debian/Ubuntu:**
  ```bash
  sudo systemctl start apache2
  sudo systemctl enable apache2
  ```
- **On CentOS/RHEL:**
  ```bash
  sudo systemctl start httpd
  sudo systemctl enable httpd
  ```

**3. Check Apache Status:**
- **On Debian/Ubuntu:**
  ```bash
  sudo systemctl status apache2
  ```
- **On CentOS/RHEL:**
  ```bash
  sudo systemctl status httpd
  ```

### Configuration Files

**1. Main Configuration File:**
- **Path:** `/etc/apache2/apache2.conf` (Debian/Ubuntu) or `/etc/httpd/conf/httpd.conf` (CentOS/RHEL)
- **Purpose:** This file contains global settings for the Apache server. Key sections include modules to load, global server settings, and security settings.

**2. Virtual Hosts Configuration:**
- **Path:** `/etc/apache2/sites-available/` and `/etc/apache2/sites-enabled/` (Debian/Ubuntu) or `/etc/httpd/conf.d/` (CentOS/RHEL)
- **Purpose:** Virtual hosts allow Apache to host multiple websites on a single server. Each virtual host configuration file defines settings for a specific site.

**3. Environment Variables File:**
- **Path:** `/etc/apache2/envvars` (Debian/Ubuntu) or `/etc/sysconfig/httpd` (CentOS/RHEL)
- **Purpose:** This file is used to set environment variables for the Apache service.

### Virtual Hosts

**1. Default Virtual Host:**
- **Path:** `/etc/apache2/sites-available/000-default.conf` (Debian/Ubuntu) or `/etc/httpd/conf.d/welcome.conf` (CentOS/RHEL)
- **Purpose:** This is the default configuration for the main site served by Apache.

**2. Creating a New Virtual Host:**
- **Example Configuration:**
  ```apache
  <VirtualHost *:80>
      ServerAdmin webmaster@example.com
      ServerName example.com
      ServerAlias www.example.com
      DocumentRoot /var/www/html/example.com
      ErrorLog ${APACHE_LOG_DIR}/error.log
      CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
  ```
- **Enable the Virtual Host:**
  - **On Debian/Ubuntu:**
    ```bash
    sudo a2ensite example.com.conf
    sudo systemctl reload apache2
    ```
  - **On CentOS/RHEL:**
    Place the configuration file in `/etc/httpd/conf.d/` and restart Apache:
    ```bash
    sudo systemctl restart httpd
    ```

### Directory Structure

**1. Document Root:**
- **Path:** `/var/www/html`
- **Purpose:** This is the default directory where web content should be stored. You can create subdirectories for different sites.

**2. Log Files:**
- **Access Log:**
  - **Path:** `/var/log/apache2/access.log` (Debian/Ubuntu) or `/var/log/httpd/access_log` (CentOS/RHEL)
  - **Purpose:** Records all requests made to the server.
- **Error Log:**
  - **Path:** `/var/log/apache2/error.log` (Debian/Ubuntu) or `/var/log/httpd/error_log` (CentOS/RHEL)
  - **Purpose:** Records any errors encountered by the server.

### Security

**1. Directory Permissions:**
- Ensure appropriate permissions for web directories:
  ```bash
  sudo chown -R www-data:www-data /var/www/html (Debian/Ubuntu)
  sudo chown -R apache:apache /var/www/html (CentOS/RHEL)
  sudo chmod -R 755 /var/www/html
  ```

**2. Disabling Directory Listing:**
- Prevent users from viewing a list of files in a directory:
  ```apache
  <Directory /var/www/html>
      Options -Indexes
  </Directory>
  ```

**3. Enabling .htaccess Files:**
- Allow the use of `.htaccess` files for directory-level configuration:
  ```apache
  <Directory /var/www/html>
      AllowOverride All
  </Directory>
  ```

**4. Basic Authentication:**
- Protect a directory with a username and password:
  ```apache
  <Directory /var/www/html/secure>
      AuthType Basic
      AuthName "Restricted Content"
      AuthUserFile /etc/apache2/.htpasswd
      Require valid-user
  </Directory>
  ```
- Create the password file:
  ```bash
  sudo htpasswd -c /etc/apache2/.htpasswd username
  ```

### Modules

**1. Enabling and Disabling Modules:**
- **On Debian/Ubuntu:**
  - Enable a module:
    ```bash
    sudo a2enmod module_name
    sudo systemctl reload apache2
    ```
  - Disable a module:
    ```bash
    sudo a2dismod module_name
    sudo systemctl reload apache2
    ```

**2. Commonly Used Modules:**
- **mod_rewrite:** Allows URL rewriting.
  ```bash
  sudo a2enmod rewrite
  sudo systemctl reload apache2
  ```
- **mod_ssl:** Enables SSL for secure connections.
  ```bash
  sudo a2enmod ssl
  sudo systemctl reload apache2
  ```

### Performance Tuning

**1. Prefork vs. Worker MPM:**
- **Prefork MPM:** Uses multiple child processes with one thread each. Suitable for compatibility with non-thread-safe libraries.
- **Worker MPM:** Uses multiple child processes with many threads each. Suitable for high-traffic servers.

**2. Configuring MPM:**
- **Path:** `/etc/apache2/mods-available/mpm_prefork.conf` or `/etc/apache2/mods-available/mpm_worker.conf` (Debian/Ubuntu)
- **Example Settings:**
  ```apache
  <IfModule mpm_prefork_module>
      StartServers 5
      MinSpareServers 5
      MaxSpareServers 10
      MaxRequestWorkers 150
      MaxConnectionsPerChild 0
  </IfModule>
  ```

**3. Enabling the Desired MPM:**
- **On Debian/Ubuntu:**
  ```bash
  sudo a2dismod mpm_worker
  sudo a2enmod mpm_prefork
  sudo systemctl restart apache2
  ```

### Monitoring and Maintenance

**1. Monitoring Tools:**
- **Apache Status Module:**
  ```apache
  <Location /server-status>
      SetHandler server-status
      Require local
  </Location>
  ```
- Enable the status module and restart Apache:
  ```bash
  sudo a2enmod status
  sudo systemctl restart apache2
  ```

**2. Log Rotation:**
- **Logrotate Configuration:**
  - **Path:** `/etc/logrotate.d/apache2` (Debian/Ubuntu) or `/etc/logrotate.d/httpd` (CentOS/RHEL)
  - **Example Configuration:**
    ```plaintext
    /var/log/apache2/*.log {
        daily
        missingok
        rotate 14
        compress
        delaycompress
        notifempty
        create 640 root adm
        sharedscripts
        postrotate
            /etc/init.d/apache2 reload > /dev/null
        endscript
    }
    ```

### Conclusion

This guide provides a comprehensive overview of Apache HTTP Server, covering installation, configuration, security, performance tuning, and monitoring. Keep this as a reference to effectively manage your Apache web server.