## Manipulate data linux_commands

You are provided with a log file (`server.log`) containing information about various server events. Your task is to create a Bash script that processes this log file and generates a report with the following information:

1. The total number of requests made to the server.
2. A list of unique IP addresses that made requests to the server, sorted alphabetically.
3. The top 2 most requested URLs, along with their request count.
4. The number of requests that resulted in server errors (HTTP status codes 5xx).

**Initial Input (`server.log`):**

```
192.168.1.1 - - [01/Jan/2022:10:15:30 +0000] "GET /index.html HTTP/1.1" 200 1024
192.168.1.2 - - [01/Jan/2022:10:16:45 +0000] "POST /login HTTP/1.1" 200 512
192.168.1.1 - - [01/Jan/2022:10:17:10 +0000] "GET /about.html HTTP/1.1" 404 256
192.168.1.3 - - [01/Jan/2022:10:18:30 +0000] "GET /index.html HTTP/1.1" 200 1024
192.168.1.2 - - [01/Jan/2022:10:20:15 +0000] "GET /contact.html HTTP/1.1" 500 2048
192.168.1.1 - - [01/Jan/2022:10:19:10 +0000] "GET /about.html HTTP/1.1" 502 256
```

1. Analyze the provided `server.log` file.
2. Write a Bash script `process_log.sh` that generates a report based on the criteria outlined above.
3. Test your script to ensure it produces the correct output in `report.txt`.

**Advanced Task:**

1. The distribution of HTTP status codes (e.g., how many 200, 404, 500, etc.).
2. The average size of the responses (extracted from the last field in each log entry).
3. A breakdown of requests by hour.

### <span style="color:#ffc000">code</span>:

Below is a Bash script named `process_log.sh` designed to process the `server.log` file as per your requirements. It will generate a report with the specified metrics and handle advanced tasks like analyzing the distribution of HTTP status codes, calculating the average size of responses, and breaking down requests by hour.

```bash
#!/bin/bash

log_file="server.log"
report_file="report.txt"

# Function to count total number of requests
count_requests() {
    local total_requests=$(wc -l < "$log_file")
    printf "Total number of requests: %d\n" "$total_requests"
}

# Function to list unique IP addresses
list_unique_ips() { 
    local ips=$(awk '{print $1}' "$log_file" | sort -u)
    printf "List of unique IP addresses:\n%s\n" "$ips"
}

# Function to find the top 2 most requested URLs
top_two_urls() {
    local top_urls=$(awk '{print $7}' "$log_file" | sort | uniq -c | sort -nr | head -2)
    printf "Top 2 most requested URLs:\n%s\n" "$top_urls"
}

# Function to count requests resulting in server errors (HTTP status codes 5xx)
count_server_errors() {
    local error_count=$(awk '$9 ~ /^5/ {count++} END {print count}' "$log_file")
    printf "Number of requests that resulted in server errors: %d\n" "${error_count:-0}"
}

# Function to analyze HTTP status codes distribution
status_code_distribution() {
    local distribution=$(awk '{print $9}' "$log_file" | sort | uniq -c | sort -nr)
    printf "HTTP status code distribution:\n%s\n" "$distribution"
}

# Function to calculate the average size of the responses
average_response_size() {
    local total_size=$(awk '{total+=$10} END {print total}' "$log_file")
    local count=$(awk 'END {print NR}' "$log_file")
    local average_size=$(echo "scale=2; $total_size / $count" | bc)
    printf "Average size of the responses: %.2f bytes\n" "$average_size"
}

# Function to break down requests by hour
requests_by_hour() {
    local by_hour=$(awk -F'[:[]' '{print $2}' "$log_file" | cut -d':' -f1 | sort | uniq -c | sort -k2)
    printf "Breakdown of requests by hour:\n%s\n" "$by_hour"
}

# Main function to orchestrate the script
main() {
    {
        count_requests
        echo ""
        list_unique_ips
        echo ""
        top_two_urls
        echo ""
        count_server_errors
        echo ""
        status_code_distribution
        echo ""
        average_response_size
        echo ""
        requests_by_hour
    } > "$report_file"
}

# Execute main function
main
```


>[!info]
>Each function focuses on a specific task and outputs its findings to `report.txt` using redirection. Make sure that `server.log` is in the same directory as this script when running it, or modify the script to accept the log file path as an argument.

#### Code analysis 

### 1. `count_requests`
This function counts the total number of requests made to the server. It uses the `wc -l` command to count lines in the log file, which represents the number of requests, assuming each request is logged on a new line.

```bash
wc -l < "$log_file"
```
- `wc -l`: Counts the lines in the file.
- `"$log_file"`: Redirects the content of `server.log` directly into `wc`, avoiding a subshell invocation that `cat "$log_file" | wc -l` would entail.

### 2. `list_unique_ips`
This function extracts and lists all unique IP addresses that made requests to the server. It processes the first field from each log entry (typically the IP address), sorts them, and then filters out duplicates using `sort -u`.

```bash
awk '{print $1}' "$log_file" | sort -u
```
- `awk '{print $1}'`: Extracts the first column (IP address).
- `sort -u`: Sorts the IP addresses and removes duplicates.

### 3. `top_two_urls`
This function identifies the top two most requested URLs. It extracts the URLs (assuming they are located in the seventh field of each log entry), counts occurrences, sorts them in descending order, and then picks the top two.

```bash
awk '{print $7}' "$log_file" | sort | uniq -c | sort -nr | head -2
```
- `awk '{print $7}'`: Extracts the URL.
- `uniq -c`: Counts each unique line.
- `sort -nr`: Sorts numerically in reverse to get the most frequent at the top.
- `head -2`: Retrieves only the top two results.

### 4. `count_server_errors`
This function counts the number of requests that resulted in server errors (HTTP status codes starting with '5'). It scans the ninth field for status codes, counts those starting with '5', and prints the count.

```bash
awk '$9 ~ /^5/ {count++} END {print count}' "$log_file"
```
- `$9 ~ /^5/`: Matches lines where the ninth field starts with '5'.
- `count++`: Increments a counter each time a match is found.
- `END {print count}`: At the end of input, print the final count.

### 5. `status_code_distribution`
This function analyzes the distribution of all HTTP status codes. It sorts and counts each unique status code.

```bash
awk '{print $9}' "$log_file" | sort | uniq -c | sort -nr
```
- `awk '{print $9}'`: Extracts the status code.
- `uniq -c`: Counts each unique status code.
- `sort -nr`: Sorts the results numerically in reverse order.

### 6. `average_response_size`
This function calculates the average size of the responses by analyzing the last field, which presumably contains the size of the response in bytes. It totals these values and divides by the number of entries.

```bash
awk '{total+=$10} END {print total}' "$log_file"
echo "scale=2; $total_size / $count" | bc
```
- `total+=$10`: Adds the value of the tenth field (response size) to a running total.
- `scale=2`: Ensures the division result has two decimal places.
- `bc`: Performs precise floating-point arithmetic.

### 7. `requests_by_hour`
This function breaks down the number of requests by hour, extracting the hour from timestamps in the log entries and counting occurrences.

```bash
awk -F'[:[]' '{print $2}' "$log_file" | cut -d':' -f1 | sort | uniq -c | sort -k2
```
- `awk -F'[:[]' '{print $2}'`: Splits the line on colons and brackets, extracting the hour.
- `cut -d':' -f1`: Further refines the extraction of hours.
- `uniq -c`: Counts occurrences of each hour.
- `sort -k2`: Sorts by the second field (the hour).