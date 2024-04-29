## Linux process

### Create a Bash script that performs the following actions:

1. Lists all current user processes, including the PID, PPID, and controlling terminal.
2. Demonstrates process creation by starting a background process.
3. Sends a signal to the background process to terminate it after a certain amount of time.
4. Adjusts the niceness of a process and demonstrates the change in priority.
5. Utilizes job control to stop and resume a process.

#### Version 1:

```bash
#!/bin/bash

# Function to list current user processes
list_user_processes() {
    printf "Current user processes including PID, PPID, and controlling terminal:\n"
    ps -o pid,ppid,tty,cmd -u "$(whoami)"
}

# Function to start a background process
start_background_process() {
    local cmd=$1
    $cmd &
    bg_pid=$!
    printf "Background process started with PID: %d\n" "$bg_pid"
}

# Function to send a signal to terminate a background process
terminate_background_process() {
    local pid=$1
    local delay=$2
    sleep "$delay"
    kill "$pid" && printf "Process %d terminated after %d seconds\n" "$pid" "$delay"
}

# Function to adjust the niceness of a process
adjust_niceness() {
    local pid=$1
    local new_nice=$2
    renice "$new_nice" -p "$pid" && printf "Niceness of process %d adjusted to %d\n" "$pid" "$new_nice"
}

# Function to demonstrate job control
job_control() {
    local cmd=$1
    $cmd &
    job_pid=$!
    printf "Job started with PID: %d\n" "$job_pid"

    # Stop the process
    kill -STOP "$job_pid"
    printf "Job %d stopped\n" "$job_pid"

    # Resume the process
    kill -CONT "$job_pid"
    printf "Job %d resumed\n" "$job_pid"
}

main() {
    # List current user processes
    list_user_processes

    # Start a background process
    start_background_process "sleep 30"

    # Terminate the background process after 10 seconds
    terminate_background_process "$bg_pid" 10

    # Adjust the niceness of the background process
    adjust_niceness "$bg_pid" 10

    # Demonstrate job control
    job_control "sleep 30"
}

main "$@"
```

>[!info]
This script encapsulates each functionality into a separate function. Run the script to observe the actions in order: listing processes, starting and terminating a background process, adjusting its niceness, and demonstrating job control.

##### Simplification of the code 

In the `printf` command:

```bash
kill "$pid" && printf "Process %d terminated after %d seconds\n" "$pid" "$delay"
```

Each `%d` is a format specifier used by `printf` for integer formatting. It instructs `printf` to format and insert an integer value at that position in the output string. In this specific line:

- The first `%d` is replaced with the value of `$pid`, which is expected to be the Process ID (PID) of the background process you are terminating. This PID is an integer.

- The second `%d` is replaced with the value of `$delay`, which is the number of seconds after which the process is terminated. `$delay` is also an integer.

So, if `$pid` is 1234 and `$delay` is 10, the output will be: "Process 1234 terminated after 10 seconds". This output is printed only if the `kill` command successfully sends the termination signal to the process.



---
#### Version 2:

```bash
#!/bin/bash

# Function to list current user processes
list_user_processes() {
    printf "Current user processes including PID, PPID, and controlling terminal:\n"
    ps -o pid,ppid,tty,cmd -u "$(whoami)"
}

# Function to start a predefined background process
start_background_process() {
    local cmd="sleep 30"
    $cmd &
    bg_pid=$!
    printf "Background process started with PID: %d\n" "$bg_pid"
}

# Function to send a signal to terminate a predefined background process
terminate_background_process() {
    local pid=$bg_pid
    local delay=10
    sleep "$delay"
    kill "$pid" && printf "Process %d terminated after %d seconds\n" "$pid" "$delay"
}

# Function to adjust the niceness of a predefined process
adjust_niceness() {
    local pid=$bg_pid
    local new_nice=10
    renice "$new_nice" -p "$pid" && printf "Niceness of process %d adjusted to %d\n" "$pid" "$new_nice"
}

# Function to demonstrate job control on a predefined job
job_control() {
    local cmd="sleep 30"
    $cmd &
    job_pid=$!
    printf "Job started with PID: %d\n" "$job_pid"

    # Stop the process
    kill -STOP "$job_pid"
    printf "Job %d stopped\n" "$job_pid"

    # Resume the process
    kill -CONT "$job_pid"
    printf "Job %d resumed\n" "$job_pid"
}

main() {
    # List current user processes
    list_user_processes

    # Start a background process
    start_background_process

    # Terminate the background process after a predefined delay
    terminate_background_process

    # Adjust the niceness of the background process
    adjust_niceness

    # Demonstrate job control on a predefined job
    job_control
}

main "$@"
```

