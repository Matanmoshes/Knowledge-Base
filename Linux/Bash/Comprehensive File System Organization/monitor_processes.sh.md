
Here's a Bash script designed to monitor active processes and send alerts if predefined CPU or memory usage thresholds are exceeded. The script will utilize `ps` to fetch process usage details and will use `mail` for sending alert notifications.

### Script Setup

```bash
#!/bin/bash

# Thresholds for CPU and memory usage (as percentages)
CPU_THRESHOLD=80
MEM_THRESHOLD=70

# Email address for alerts
ALERT_EMAIL="admin@example.com"

# Function to check and alert on high resource usage
monitor_usage() {
    # Fetch all processes with ps, skipping the header (ps output fields: PID, CPU, MEM, CMD)
    ps -eo pid,%cpu,%mem,cmd --sort=-%cpu | awk 'NR>1 {if ($2 >= '"$CPU_THRESHOLD"' || $3 >= '"$MEM_THRESHOLD"') print $0;}' |
    while read pid cpu mem cmd; do
        # Form the alert message
        message="High resource usage detected -> CPU: $cpu%, MEM: $mem%, COMMAND: $cmd, PID: $pid"
        echo "$message" | mail -s "High Resource Usage Alert" "$ALERT_EMAIL"
    done
}

# Main function to continuously monitor the processes
main() {
    while true; do
        monitor_usage
        sleep 60  # Check every minute
    done
}

# Run the main function
main
```


---
### Code Breakdown

### Script Initialization and Configuration

```bash
#!/bin/bash
```
- **Shebang**: Specifies that the script should be run using Bash, the most common shell in Linux environments.

```bash
# Thresholds for CPU and memory usage (as percentages)
CPU_THRESHOLD=80
MEM_THRESHOLD=70

# Email address for alerts
ALERT_EMAIL="admin@example.com"
```
- **Thresholds**: Variables `CPU_THRESHOLD` and `MEM_THRESHOLD` are set to define the limits for CPU and memory usage, beyond which an alert will be triggered. These are set at 80% for CPU and 70% for memory.
- **Alert Email**: `ALERT_EMAIL` variable stores the email address to which alerts will be sent when thresholds are exceeded.

### Function Definition: `monitor_usage`

```bash
monitor_usage() {
    ps -eo pid,%cpu,%mem,cmd --sort=-%cpu | awk 'NR>1 {if ($2 >= '"$CPU_THRESHOLD"' || $3 >= '"$MEM_THRESHOLD"') print $0;}' |
    while read pid cpu mem cmd; do
        message="High resource usage detected -> CPU: $cpu%, MEM: $mem%, COMMAND: $cmd, PID: $pid"
        echo "$message" | mail -s "High Resource Usage Alert" "$ALERT_EMAIL"
    done
}
```
- **Process Listing**: Uses `ps` command with `-eo` option to specify a custom format (`pid,%cpu,%mem,cmd`), which outputs process ID, CPU usage, memory usage, and command line of each process.
- **Sorting**: The `--sort=-%cpu` option sorts the processes by CPU usage in descending order.
- **Filtering**: The `awk` command filters this list to include only those processes where the CPU usage (`$2`) is greater than or equal to `CPU_THRESHOLD` or the memory usage (`$3`) is greater than or equal to `MEM_THRESHOLD`.
- **Reading and Alerting**: The filtered list is piped into a `while read` loop, which reads each line into variables `pid`, `cpu`, `mem`, and `cmd`. It then forms a message and sends this message via email using the `mail` command.

### Main Function: `main`

```bash
main() {
    while true; do
        monitor_usage
        sleep 60  # Check every minute
    done
}
```
- **Infinite Loop**: This loop runs indefinitely (`while true`), making the script a continuous monitoring tool.
- **Call to Monitor Usage**: Inside the loop, `monitor_usage` is called to check and alert on process usage.
- **Sleep**: The `sleep 60` command pauses the script for 60 seconds after each execution of `monitor_usage`, thus setting the monitoring interval. This can be adjusted based on how frequently you want to check the system processes.

### Script Execution

```bash
main
```
- **Start Point**: The `main` function is called to start the monitoring when the script is run. This is the entry point of the script.

### Summary

This script is a practical tool for system administrators who need to keep an eye on resource usage without manual monitoring. It automates the process of checking resource consumption and alerts administrators through email when usage goes beyond set thresholds, helping in proactive management of system resources.

### Execution Requirements

To run this script:
- Ensure `mail` utilities are correctly configured on your system to send emails.
- Run the script with appropriate permissions, likely as a superuser, to access all processes.
- Consider running this script in a screen or tmux session, or as a background job, if it needs to run continuously on a server.