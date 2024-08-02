# Installing Apache Server


After installing Apache2 using `sudo apt install apache2` on a Debian-based Linux system (like Ubuntu), the Apache service should start automatically. However, it's always good to check and make sure it's running and to know how to manage the service for future needs.

Here's what you can do after installation:

1. **Check Apache Status:**
   ```bash
   sudo systemctl status apache2
   ```
   This command checks if Apache is active and running. If it's running, you should see a status indicating 'active (running)' in the output.

2. **Start Apache Manually (if needed):**
   If Apache is not running for any reason, you can start it manually:
   ```bash
   sudo systemctl start apache2
   ```

3. **Enable Apache to Start on Boot:**
   To ensure Apache starts automatically after a reboot, use:
   ```bash
   sudo systemctl enable apache2
   ```

4. **Access Apache Default Page:**
   - Open a web browser and navigate to `http://localhost` or `http://your-server-ip`. You should see the Apache default welcome page. This confirms that Apache is installed and running properly.

5. **Restart Apache:**
   If you make configuration changes later, restart Apache to apply those changes:
   ```bash
   sudo systemctl restart apache2
   ```

6. **Stop Apache:**
   If you need to stop Apache for any reason, use:
   ```bash
   sudo systemctl stop apache2
   ```

Remember, if you're planning to host a website or web application on Apache, you'll need to configure virtual hosts and potentially secure it with SSL/TLS, among other settings. These operations require additional steps beyond the basic installation.

---

Enabling the SSL module in Apache2 on an Ubuntu system involves a few steps. This process allows your Apache2 web server to handle HTTPS connections, which is essential for security, particularly if you're running a website that handles sensitive data or user logins. Here’s how to do it:

1. **Install Apache2:**
   - If you haven't already installed Apache2:
     ```bash
     sudo apt install apache2
     ```

2. **Enable the SSL Module:**
   - Use the `a2enmod` command to enable the SSL module:
     ```bash
     sudo a2enmod ssl
     ```
   - This command activates the SSL module within Apache.

3. **Restart Apache to Apply Changes:**
   - After enabling SSL, you need to restart Apache for the changes to take effect:
     ```bash
     sudo systemctl restart apache2
     ```

4. **Setting Up SSL Certificates:**
   - For SSL to function, you need to set up an SSL certificate. You can use a self-signed certificate for testing or obtain a certificate from a Certificate Authority (CA) for production environments.
   - To create a self-signed certificate (not recommended for public websites):
     ```bash
     sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
     ```
   - You'll need to answer some questions for the certificate's metadata.

5. **Configure Apache to Use SSL:**
   - Create or edit a Virtual Host file for SSL, typically located in `/etc/apache2/sites-available/`.
   - Example configuration snippet in a Virtual Host file:
     ```apache
     <VirtualHost *:443>
         ServerAdmin admin@example.com
         ServerName example.com
         DocumentRoot /var/www/html

         SSLEngine on
         SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
         SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key

         # Other directives...
     </VirtualHost>
     ```
   - Replace `example.com` with your domain and adjust the file paths to your certificates.

6. **Enable the SSL Site Configuration:**
   - If you created a new Virtual Host file, enable it:
     ```bash
     sudo a2ensite your-ssl-config.conf
     ```

7. **Reload Apache:**
   - Finally, reload Apache to apply all new configurations:
     ```bash
     sudo systemctl reload apache2
     ```

Remember, using a self-signed certificate will cause browsers to warn users about the site's security. For a production website, it's advised to use a certificate issued by a trusted CA, such as those provided by Let's Encrypt, which can be obtained for free.

---
### Transfer file to another machine

It requires the `.pem` key file to be able sent the file 

```bash
sudo scp -i /Users/matanmoshe/Downloads/test_08_04_24.pem /Users/matanmoshe/Downloads/test_08_04_24.pem ubuntu@34.232.66.175:/home/ubuntu
```

1. **`sudo`**: Executes the command with superuser privileges. It's not typically needed for `scp` unless the file requires root access.

2. **`scp`**: The command for securely copying files over SSH.

3. **`-i /Users/matanmoshe/Downloads/test_08_04_24.pem`**: Specifies the private SSH key to use for authentication. This key should be the one that corresponds to the public key on the remote server.

4. **Source File Path**: `/Users/matanmoshe/Downloads/test_08_04_24.pem` is the file you want to copy. Ensure this file path is correct.

5. **Destination Specification**: `ubuntu@34.232.66.175:/home/ubuntu` specifies that you are copying the file to the remote server `34.232.66.175` with the username `ubuntu`. The file will be placed in the `/home/ubuntu` directory on the server.

---

### Extra Challenge

>[!Task] 
>Modify the SSL/TLS configuration on the server to use a stronger cipher suite or implement Perfect Forward Secrecy (PFS). Edit the SSL configuration file (`default-ssl.conf`) and adjust the SSLCipherSuite directive.
>
Implement client authentication using SSL certificates. This involves generating client certificates and configuring the server to require client authentication. This step is more advanced and requires additional configuration.
>
These commands provide a basic setup for establishing a secure connection using SSL. Feel free to customize and expand upon them based on your specific requirements and preferences.

To enhance the security of your SSL/TLS configuration on an Apache server and implement client authentication, you'll need to modify the Apache SSL configuration and manage certificates. Here's a step-by-step guide:

### 1. Modify Cipher Suites for Stronger Encryption and Perfect Forward Secrecy (PFS):

1. **Edit the SSL Configuration:**
   - Open your `default-ssl.conf` (or equivalent SSL config file):
     ```bash
     sudo vi /etc/apache2/sites-available/default-ssl.conf
     ```
   - Alternatively, you might want to edit the global SSL configuration file, often found at `/etc/apache2/mods-available/ssl.conf`.

2. **Adjust `SSLCipherSuite`:**
   - Find the `SSLCipherSuite` directive. If it’s not there, you can add it.
   - Set it to a string that prioritizes strong ciphers and enables PFS. For example:
     ```
     SSLCipherSuite EECDH+AESGCM:EDH+AESGCM
     ```
   - This configuration prioritizes elliptic curve Diffie-Hellman (ECDH) and ephemeral Diffie-Hellman (DHE) key exchange mechanisms, which enable PFS, along with AES GCM for encryption.

3. **Enable `SSLHonorCipherOrder`:**
   - Ensure that the server’s preference for cipher suites is respected:
     ```
     SSLHonorCipherOrder on
     ```

4. **Restart Apache:**
   - After making changes, restart Apache to apply:
     ```bash
     sudo systemctl restart apache2
     ```

### 2. Implement Client Authentication Using SSL Certificates:

1. **Generate Client Certificates:**
   - Each client needs a certificate signed by a CA that the server trusts.
   - Use OpenSSL to generate client certificates, similar to how you generated the server certificate.

2. **Configure Apache for Client Authentication:**
   - In your SSL configuration file, set up directives for client authentication:
     ```
     SSLCACertificateFile /path/to/ca-certificate.crt
     SSLVerifyClient require
     SSLVerifyDepth  10
     ```
   - `SSLCACertificateFile` should point to the CA certificate that you used to sign the client certificates.
   - `SSLVerifyClient require` forces client authentication.
   - `SSLVerifyDepth` specifies how deeply to verify the certificate chain.

3. **Distribute Client Certificates:**
   - Securely distribute the generated client certificates and corresponding private keys to the intended users or devices.

4. **Restart Apache Again:**
   - Restart Apache to apply these new settings.

>[!Note]
>- These configurations provide a fundamental setup for secure SSL connections and client authentication. Depending on your environment's specific needs, additional tuning might be necessary.
>- Always back up configuration files before making changes.
>- After making changes, it's good practice to test your configuration using tools like SSL Labs' SSL Test to ensure that everything is set up correctly.