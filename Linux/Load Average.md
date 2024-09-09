### What is Load Average in Linux?

**Load average** is a metric in Linux that reflects the average number of processes (also known as **tasks**) that are either:
1. **Actively running on the CPU** (in execution).
2. **Waiting to be executed** (in a queue for CPU time).

It's an important indicator of how busy a system's CPU is over a period of time. The load average is typically displayed as three numbers, representing the system load over the last **1 minute**, **5 minutes**, and **15 minutes**.

For example:

```bash
load average: 1.25, 0.80, 0.60
```

This means:
- Over the last **1 minute**, the average number of processes either running or waiting was **1.25**.
- Over the last **5 minutes**, it was **0.80**.
- Over the last **15 minutes**, it was **0.60**.

### How is Load Average Calculated?

The load average metric is derived from the number of **processes** that are in either a **running state** or waiting for CPU execution over a period of time. It doesn't just measure CPU usage, but also considers the number of processes waiting on disk I/O or network operations.

#### Important Points:
- The **load average** doesn't represent a percentage (like CPU usage), but rather the number of processes or tasks waiting for resources.
- **Load average** includes:
  - Processes actively using the CPU.
  - Processes waiting to use the CPU (in the run queue).
  - Processes waiting for disk I/O, network, or other resources.

### Understanding "Tasks"

A **task** in Linux is essentially a process or thread that the operating system is managing. When a program runs, it spawns a process, which becomes a **task** handled by the CPU.

Tasks can be in various states:
1. **Running**: The process is actively using CPU resources.
2. **Waiting**: The process is ready to run but is waiting for CPU time.
3. **Sleeping**: The process is waiting for I/O or other resources (like disk, network).
4. **Stopped**: The process has been paused.
5. **Zombie**: The process has completed execution but hasn't been cleaned up yet by its parent process.

When we talk about **load average**, we mainly refer to the tasks that are either **running** or **waiting** for the CPU.

### Interpreting Load Average

The load average value tells you how many processes are competing for CPU time. However, to understand the significance of the load average, you need to know the number of CPU cores on the system:

- **Single-core CPU**: A load average of **1.00** means the system is fully utilized.
- **Multi-core CPU**: If your system has **4 cores**, a load average of **4.00** means the system is fully utilized, while a load average of **2.00** indicates the system is 50% utilized.

#### Examples:

- **Load average of `1.00` on a single-core system**: The system is fully loaded. One process is either running or waiting for CPU time.
- **Load average of `2.00` on a single-core system**: The system is overloaded. Two processes are competing for CPU time, so one of them is waiting in the queue.
- **Load average of `4.00` on a 4-core system**: The system is fully utilized, but all tasks are being processed in parallel with no delays.
- **Load average of `6.00` on a 4-core system**: The system is overloaded. There are more processes than the available CPU cores, meaning that some tasks are waiting to be executed.

#### Important Rule of Thumb:
- If the load average is **equal to or lower than the number of CPU cores**, your system is running efficiently.
- If the load average is **higher than the number of cores**, processes are waiting for CPU time, indicating that your system may be overloaded.

### Real-World Example of Load Average

Let’s say we have a **4-core** CPU system and the load average is displayed as:

```bash
load average: 3.50, 2.80, 2.60
```

This means:
- Over the last **1 minute**, an average of **3.50** tasks were either running or waiting for CPU.
- Over the last **5 minutes**, the average load was **2.80**.
- Over the last **15 minutes**, the average load was **2.60**.

**Interpretation**:
- Since the **1-minute load average (3.50)** is less than the total number of CPU cores (**4 cores**), the system is **slightly loaded** but still performing efficiently.
- The **5-minute and 15-minute averages** are even lower, indicating that the system has been under less load over time and may have spiked recently.

### What Causes High Load Average?

A high load average can be caused by:
- **High CPU usage**: Multiple processes competing for CPU resources.
- **Disk I/O bottlenecks**: Processes waiting for disk read/write operations.
- **Memory bottlenecks**: When a system runs out of memory and starts swapping.
- **Network I/O bottlenecks**: Processes waiting for network data.

### How to Check Load Average?

You can check the load average using various commands:

1. **Using `top`**:

```bash
top
```

The load average appears at the top of the screen:

```
load average: 1.25, 0.75, 0.50
```

2. **Using `uptime`**:

```bash
uptime
```

Example output:

```
08:15:23 up 5 days,  4:20,  3 users,  load average: 0.50, 0.30, 0.25
```

3. **Using `cat /proc/loadavg`**:

```bash
cat /proc/loadavg
```

Example output:

```
0.50 0.30 0.25 1/209 3456
```

Here, the first three numbers are the load averages for the past **1, 5, and 15 minutes**.

### Tasks in Load Average

Each process or task that is waiting for CPU time contributes to the load average. A **task** can either be:

- **Running**: Actively using the CPU.
- **Waiting**: Ready to execute but waiting for CPU time.
- **Blocked**: Waiting for resources like disk or network.

The more tasks that are **waiting** or **blocked**, the higher the load average.

#### Example:
If you have a load average of `6.00` on a **4-core system**, this means at least 2 processes are **waiting** at any given time, as the system only has 4 cores available for execution.

### Key Takeaways:

- **Load average** measures the **system load** in terms of the number of tasks either actively running or waiting for CPU time.
- The three values represent the load average over the last **1 minute**, **5 minutes**, and **15 minutes**.
- For a multi-core system, the load average should ideally not exceed the number of CPU cores. A load average higher than the number of cores indicates that tasks are waiting for resources.
- A high load average doesn't necessarily mean high CPU usage—it could be caused by processes waiting for disk, memory, or network resources.

