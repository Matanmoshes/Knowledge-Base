### Summary of Amazon CloudWatch Overview

**Why Monitor?**  
Monitoring helps provide insights into your application and architecture performance, allowing you to identify potential issues in real time.

---

**What is CloudWatch?**  
Amazon CloudWatch is a monitoring and observability platform from AWS. It helps monitor multiple levels of your applications and resources, providing insights and alerting when something goes wrong.

---

**Key CloudWatch Features:**
1. **System Metrics**: 
   - Metrics that come out of the box, like CPU utilization and network throughput.
   - More managed services (like EC2, S3, SQS) usually have more default metrics.

2. **Application Metrics**:
   - By installing the CloudWatch agent, you can track custom metrics like memory usage and disk space.

3. **Alarms**:
   - Alarms trigger notifications based on metric conditions to alert you or your team when certain thresholds are reached.

---

**Types of Metrics:**
- **Default Metrics**:
  1. **CPU Utilization**: Measures how much of the virtual CPU is used over a period of time.
  2. **Network Throughput**: Measures network traffic in and out of a resource.
  
- **Custom Metrics** (require CloudWatch agent):
  1. **Memory Utilization**: Tracks how much memory is being used on an instance.
  2. **Storage Capacity**: Monitors disk space on Elastic Block Store (EBS) volumes.

---

**Setting Up CloudWatch Alarms (Demo Overview)**:
1. **Create an Alarm**: Navigate to the CloudWatch console, choose metrics, and set conditions for alarms.
2. **Metric Types**: You can set alarms based on metrics like CPU utilization or custom metrics.
3. **Customize Period and Threshold**: Specify a time period and set a threshold for triggering the alarm.
4. **Set Notifications**: Alarms can send notifications through SNS, perform actions like auto-scaling, or stop/reboot EC2 instances.
5. **Monitor Alarm States**: CloudWatch alarms transition between states like "In Alarm" based on conditions and missing data.

---

**Key Exam Tips:**
1. **CloudWatch is the go-to tool** for monitoring in AWS.
2. **No default alarms**: Alarms need to be configured for each resource.
3. **Understand default vs custom metrics**: Custom metrics (like memory utilization) require the CloudWatch agent.
4. **Basic vs Detailed Monitoring**: 
   - Basic (5-minute intervals) is free.
   - Detailed (1-minute intervals) costs extra.

---

This overview highlights how CloudWatch is used for monitoring, key features, and essential metrics, especially for exam preparation.