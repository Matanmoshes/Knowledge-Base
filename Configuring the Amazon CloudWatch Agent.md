## Guide for Installing and Configuring the Amazon CloudWatch Agent (Linux)

This guide will walk you through setting up the Amazon CloudWatch Agent on a Linux instance to send custom logs to CloudWatch Logs. We'll cover creating the necessary IAM role, installing the CloudWatch agent, preparing the configuration file, starting the agent service manually, and finally, monitoring the logs using the CloudWatch console.

---

### Step 1: Create an IAM Role for EC2

First, you need to create an IAM role with the permissions necessary to send logs to CloudWatch Logs.

#### 1a: Create IAM Inline Policy
Role Name: `AmazonEC2SessionManagerRole`

**Attach this AWS-Managed Policy**:  
`AmazonSSMManagedInstanceCore`  
AmazonSSMManagedInstanceCore Role ARN:  
`arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore`

**Inline Policy (AllowCloudWatchLogsPut):**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents",
        "logs:DescribeLogStreams"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
```

#### 1b: Walkthrough for Creating EC2 Role
1. **Navigate to IAM** and create a new role.
![image](https://github.com/user-attachments/assets/01a7fe0c-a700-4bbb-9c2c-6b18274741ec)

2. **Select AWS service** as the trusted entity type.
![image](https://github.com/user-attachments/assets/3e43b63b-e250-4780-9185-de7c9db4c0af)

3. Choose **EC2** for the service and **EC2 Role for AWS Systems Manager** as the use case.
![image](https://github.com/user-attachments/assets/a683af8c-c2f1-4d55-a5f2-33ed62947988)

4. **Name the role** and click **Create**.
![image](https://github.com/user-attachments/assets/df3fe305-452d-4063-bd4c-0ac1dc5d2ac3)

5. Once the role is created, **Add permissions** and choose **Create inline policy**.
![image](https://github.com/user-attachments/assets/cb7aae26-3790-4e8d-9aba-730883dad0e9)

6. Select **JSON** editor and **paste the inline policy** content from above.
![image](https://github.com/user-attachments/assets/88656bdc-a3a1-4e81-98e6-ea82e3327fa2)

7. **Name your policy** and click **Create**.
![image](https://github.com/user-attachments/assets/2aac685e-3723-460a-87a3-28097ab5c544)

---

### Step 2: Install CloudWatch Agent

Once the IAM role is attached to your EC2 instance, you'll need to install the CloudWatch Agent.

#### 2a: User Data Script (Optional)
If you prefer to automate the installation during instance launch, use this user data script:

```bash
#!/bin/bash

sudo yum update -y

# Installing the CloudWatch agent
sudo yum install amazon-cloudwatch-agent -y

# Allowing rsyslogs to be collected and saved. This is NOT a default in AML2023
dnf install rsyslog -y
systemctl enable rsyslog --now

# Echo the config file contents into the JSON file.
sudo cat <<EOF >>/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
{
    "agent": {
        "metrics_collection_interval": 60,
        "run_as_user": "root",
        "region": "us-east-1",
        "debug": true
    },
    "logs": {
        "logs_collected": {
            "files": {
                "collect_list": [
                    {
                        "file_path": "/var/log/messages",
                        "log_group_name": "{instance_id}",
                        "log_stream_name": "messages",
                        "timezone": "UTC"
                    },
                    {
                        "file_path": "/var/log/secure",
                        "log_group_name": "{instance_id}",
                        "log_stream_name": "secure",
                        "timezone": "UTC"
                    }
                ]
            }
        }
    }
}
EOF

# Fetching local config and starting agent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -s -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

#### 2b: Manual Installation
If not using the user data script, manually install the agent:

1. **Update and install the CloudWatch agent**:
   ```bash
   sudo yum update -y
   sudo yum install amazon-cloudwatch-agent -y
   ```

2. **Install rsyslog for log collection (if needed)**:
   ```bash
   sudo dnf install rsyslog -y
   sudo systemctl enable rsyslog --now
   ```

---

### Step 3: Prepare the Configuration File (Manual Option)

If you didn’t use the user data script, you’ll need to manually create the CloudWatch agent configuration file.

**File Location**: `/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json`

**Sample Configuration File**:
```json
{
  "agent": {
    "metrics_collection_interval": 60,
    "run_as_user": "root",
    "region": "us-east-1",
    "debug": true
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/messages",
            "log_group_name": "{instance_id}",
            "log_stream_name": "messages",
            "timezone": "UTC"
          },
          {
            "file_path": "/var/log/secure",
            "log_group_name": "{instance_id}",
            "log_stream_name": "secure",
            "timezone": "UTC"
          }
        ]
      }
    }
  }
}
```

Create the file:
```bash
sudo vi /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
```

Paste the configuration JSON content, save and exit the editor.

---

### Step 4: Start the CloudWatch Agent (Manually)

If you didn’t use the user data script to start the agent, follow these steps:

1. **Start the agent and fetch the config file**:
   ```bash
   sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -s -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json
   ```

2. **Start the agent with an existing config**:
   ```bash
   sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a start -m ec2 -s
   ```

---

### Step 5: Monitor Logs Using CloudWatch Web Console

1. Go to the **AWS Management Console** and navigate to **CloudWatch**.
2. Click on **Logs** in the left-hand menu.
3. Find your **Log Groups**, which should display log groups corresponding to your instance.
4. Drill down into the log streams (e.g., `messages`, `secure`) to view the log entries sent from your EC2 instance.
![image](https://github.com/user-attachments/assets/b2e9c7ee-1bcd-46e7-b4a1-2016452223f6)

![image](https://github.com/user-attachments/assets/691472a4-2212-4ff2-bb15-8909c13987be)


---

### Conclusion

By following this guide, you’ve successfully installed and configured the Amazon CloudWatch agent on a Linux instance, set up log monitoring for custom files, and can now monitor logs in the CloudWatch console.
