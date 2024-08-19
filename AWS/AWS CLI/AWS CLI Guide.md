# AWS CLI

>[!  AWS CLI Cheatsheet]
>AWS CLI stands for Amazon Web Services Command Line Interface. When managing your AWS services there are a few options as far as tools go. Two of the most common options are using the AWS Console, or AWS CLI. The AWS Console is a web interface that you log into to manage your AWS services. In contrast to the AWS Console is AWS CLI. It is a great tool to manage AWS resources across different accounts, regions, and environments from the command line. It allows you to control services manually or create automation with scripts.
>https://www.bluematador.com/learn/aws-cli-cheatsheet#What-is-the-AWS-CLI

---
## Step 1: Install the AWS CLI

### For Windows

1. **Download the Installer**: Download the AWS CLI MSI installer for Windows from the following link:
   [AWS CLI MSI Installer](https://awscli.amazonaws.com/AWSCLIV2.msi)

2. **Run the Installer**: Double-click the downloaded MSI file and follow the on-screen instructions to complete the installation.

3. **Verify the Installation**: Open a Command Prompt and run the following command to verify the installation:
   ```cmd
   aws --version
   ```

### For macOS

1. **Install Using Homebrew**: If you have Homebrew installed, you can use it to install the AWS CLI. Open a Terminal and run:
   ```sh
   brew install awscli
   ```

2. **Verify the Installation**: Run the following command to verify the installation:
   ```sh
   aws --version
   ```

### For Linux

1. **Download the Installer**: Run the following commands to download the AWS CLI installer:
   ```sh
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   unzip awscliv2.zip
   sudo ./aws/install
   ```

2. **Verify the Installation**: Run the following command to verify the installation:
   ```sh
   aws --version
   ```


---
## Step 2: Configure the AWS CLI

1. **Open a Terminal or Command Prompt**: Open a terminal (macOS/Linux) or Command Prompt (Windows).

2. **Run the AWS Configure Command**: Run the following command to start the configuration process:
   ```sh
   aws configure
   ```

3. **Enter Your AWS Access Key ID**: You will be prompted to enter your AWS Access Key ID. You can find this in the AWS Management Console under IAM > Users > Your User > Security credentials.
   ```sh
   AWS Access Key ID [None]: your-access-key-id
   ```

4. **Enter Your AWS Secret Access Key**: Enter your AWS Secret Access Key, which you can find in the same location as the Access Key ID.
   ```sh
   AWS Secret Access Key [None]: your-secret-access-key
   ```

5. **Enter Your Default Region Name**: Enter the default AWS region you want to use (e.g., `us-east-1`). You can choose a different region based on your needs.
   ```sh
   Default region name [None]: us-east-1
   ```

6. **Enter Your Default Output Format**: Enter the default output format. Common choices are `json`, `text`, and `table`. For this guide, we'll use `json`.
   ```sh
   Default output format [None]: json
   ```

### Example Output

```sh
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-east-1
Default output format [None]: json
```

---

## Step 3: Verify Your Configuration

1. **Test Your Configuration**: Run the following command to list all S3 buckets in your account. This verifies that the AWS CLI is correctly configured and can interact with your AWS account.
   ```sh
   aws s3 ls
   ```

If the command returns a list of your S3 buckets, the configuration is successful.


---
## Troubleshooting

- **Invalid Access Key or Secret Key**: If you encounter an error regarding invalid credentials, double-check your Access Key ID and Secret Access Key in the AWS Management Console.
- **Network Issues**: Ensure you have a stable internet connection. Network issues can prevent the AWS CLI from connecting to AWS services.
- **Permissions**: Ensure that the IAM user whose credentials you are using has the necessary permissions to perform actions with the AWS CLI.

