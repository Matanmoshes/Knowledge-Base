### Summary of CloudWatch Logs Overview

**What is CloudWatch Logs?**  
CloudWatch Logs is a service that allows you to monitor, store, and access log files from various AWS resources and on-premise solutions. It helps in centralizing log data for analysis, troubleshooting, and performance tracking.

---

**Key Terms to Know**:
1. **Log Events**: The fundamental records containing timestamps and data for specific activities in your system.
2. **Log Streams**: A sequence of log events from a single source, such as an EC2 instance or a specific log file.
3. **Log Groups**: Logical groupings of related log streams. For example, you can group logs from multiple servers or applications into a single log group.

---

**CloudWatch Logs Features**:
1. **Filter Patterns**: Enable you to extract specific data from log streams, helping monitor and troubleshoot systems by identifying patterns, like error messages.
2. **CloudWatch Logs Insights**: A SQL-like tool for querying and analyzing log data, making it easier to explore logs and gain insights.
3. **CloudWatch Alarms**: Alarms can be set to trigger actions (notifications or automated remediation) when specific log patterns or thresholds are met.

---

**Demo Overview**:
- Launch an EC2 instance, install the CloudWatch agent, and configure it to send custom logs (e.g., `/var/log/messages` and `/var/log/secure`) to CloudWatch.
- View the logs in CloudWatch under log groups and log streams.
- Use filter patterns to search for specific log entries.
- You can create alarms and automate actions (e.g., trigger Lambda functions) based on these logs.

---

**Exam Tips**:
1. **CloudWatch Logs is the primary tool** for monitoring logs, except when long-term storage without analysis is needed (in which case, S3 may be used).
2. **CloudWatch agent** is required for sending custom logs.
3. CloudWatch alarms can be set based on **filter patterns** to trigger alerts or automate actions.
4. **CloudWatch Logs Insights** is useful for querying logs using SQL-like commands.
5. Use **Kinesis Data Streams** when real-time log processing is required instead of CloudWatch.

This summary covers key concepts and tools for application monitoring using CloudWatch Logs.