## Guide to Troubleshooting Terraform and Apache Errors

This guide will walk you through troubleshooting Terraform execution errors and resolving Apache server issues on an EC2 instance. Follow these steps and notes to effectively manage these tasks.

### Prerequisites
- Access to AWS Management Console
- AWS CLI configured
- SSH key pair (.pem file) downloaded
- Basic knowledge of Linux commands and file permissions

### Steps to Follow

#### 1. Enter AWS Management Console and Change Region
- Log in to your AWS Management Console.
- Change the region to `eu-west-1`.

#### 2. Navigate to S3 and Download the PEM Key
- Go to the S3 service.
- Find and navigate to the `cloudops-test-keys` bucket.
- Download the PEM key file (`CloudOpsKey.pem`).

#### 3. Connect to the EC2 Instance
- Open Command Prompt on Windows.
- Use the following SSH command to connect to the EC2 instance:
  ```sh
  ssh -i "C:\Users\your_username\Downloads\CloudOpsKey.pem" ec2-user@54.247.10.194
  ```

#### 4. Elevate to Root User
- Run the command to switch to the root user:
  ```sh
  sudo su -
  ```

#### 5. Navigate to the Terraform Directory
- Change to the directory containing your Terraform files:
  ```sh
  cd /var/local/aws-test
  ```

#### 6. Initialize the Terraform Environment
- Initialize Terraform:
  ```sh
  terraform init
  ```

#### 7. Create an Execution Plan
- Run Terraform plan to see the required changes:
  ```sh
  terraform plan
  ```
  - If prompted for `aws_region` and `full_name`, provide the values:
    ```plaintext
    Enter a value: eu-west-1
    Enter a value: your_full_name
    ```

#### 8. Handle "No Space Left on Device" Error
- If you encounter an error regarding no space left on the device:
  ```plaintext
  Error locking state: Error acquiring the state lock: could not write lock info for "terraform.tfstate": write .terraform.tfstate.lock.info: no space left on device
  ```
  - Check disk space:
    ```sh
    df -H
    Filesystem      Size  Used Avail Use% Mounted on
	devtmpfs        489M   56K  488M   1% /dev
	tmpfs           497M     0  497M   0% /dev/shm
	/dev/xvda1      7.8G  7.8G     0 100% /
    ```
  - Delete unnecessary files to free up space:
    ```sh
    sudo rm -rf /var/log/DELETEME
    ```
  - Verify the disk space again:
    ```sh
    df -H
    Filesystem      Size  Used Avail Use% Mounted on
	devtmpfs        489M   56K  488M   1% /dev
	tmpfs           497M     0  497M   0% /dev/shm
	/dev/xvda1      7.8G  1.4G  6.4G  18% /
    ```

#### 9. Apply Terraform Configuration
- Re-run Terraform plan and apply the configuration:
  ```sh
  terraform plan
  terraform apply
  ```
  - Provide required values if prompted:
    ```plaintext
    Enter a value: eu-west-1
    Enter a value: your_full_name
    ```

#### 10. Troubleshoot Apache Server Issues
- If you cannot access the website, SSH into the new instance:
  ```sh
  ssh -i "C:\Users\your_username\Downloads\CloudOpsKey.pem" ubuntu@34.255.0.246
  ```
- Check the status of the Apache service:
  ```sh
  sudo systemctl status apache2
  ```
- Start the Apache service if it is inactive:
  ```sh
  sudo systemctl start apache2
  ```

#### 11. Fix Hostname Resolution Issue
- If you see `sudo: unable to resolve host` errors, edit the `/etc/hosts` file:
  ```sh
  sudo nano /etc/hosts
  ```
  - Ensure there is an entry for your instance hostname:
    ```plaintext
    127.0.0.1       localhost
    127.0.1.1       ip-10-0-163-180
    ```

#### 12. Correct Apache Configuration
- Edit the Apache configuration file if necessary:
  ```sh
  sudo nano /etc/apache2/sites-available/000-default.conf
  ```
  - Ensure the `DocumentRoot` is correctly set:
    ```plaintext
    DocumentRoot /var/www/html
    ```

#### 13. Set Correct Permissions
- Ensure the permissions and ownership of the web directory are correct:
  ```sh
  sudo chmod -R 755 /var/www/html
  sudo chown -R www-data:www-data /var/www/html
  ```

#### 14. Restart Apache Service
- Restart the Apache service to apply changes:
  ```sh
  sudo systemctl restart apache2
  ```

#### 15. Verify Website Access
- Access the website using the provided ELB URL:
  ```plaintext
  http://elb-web-example-gr-0095dee7-101026811.eu-west-1.elb.amazonaws.com/
  ```

### Important Notes

- **Recursive Flag (`-R`)**:
  - The `-R` flag in commands like `chmod` and `chown` means "recursive", applying the command to all files and directories within the specified directory.
  - Example: `chmod -R 755 /var/www/html` sets the permissions for `/var/www/html` and all its contents.

- **`/etc/hosts` File**:
  - The `/etc/hosts` file is used for hostname to IP address mapping.
  - Ensuring correct entries in `/etc/hosts` can resolve hostname resolution issues.
  - Example entry:
    ```plaintext
    127.0.0.1       localhost
    127.0.1.1       ip-10-0-163-180
    ```

By following these steps and notes, you should be able to troubleshoot and resolve common Terraform and Apache server issues effectively.