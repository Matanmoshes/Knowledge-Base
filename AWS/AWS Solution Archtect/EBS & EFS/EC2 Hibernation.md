### EC2 Hibernation Overview

### EBS Behaviors Reviewed

- **Stopping an Instance:**
    - Data remains on the EBS disk.
    - The instance can be restarted with data intact.
- **Terminating an Instance:**
    - By default, the root device volume is terminated.
    - You can configure it to save the EBS volume.

### EC2 Instance Startup Process

1. The operating system boots up.
2. The user data script (bootstrap script) is executed.
3. Applications start (e.g., Apache, MySQL).

### What is EC2 Hibernation?

- **Definition:** Hibernation suspends the EC2 instance by saving the contents of the instance's RAM to the EBS root volume.
- **Process:**
    - The operating system performs hibernation, suspending the instance to disk.
    - The EBS root volume and any attached data volumes are preserved.

### EC2 Hibernation in Action

1. **Initiate Hibernation:**
    
    - The instance's RAM contents are saved to the EBS root volume.
    - The instance is shut down and completely stopped.
2. **Resume from Hibernation:**
    
    - The EBS root volume is restored to its previous state.
    - RAM contents are reloaded.
    - Previously running processes are resumed.
    - Attached data volumes are reattached.
    - The instance retains its instance ID.
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/ae5789d5-3051-420d-8416-d817f388473e/2e659b51-5f8d-454b-b75c-05b6535eae88/Untitled.png)
    

### Benefits of EC2 Hibernation

- **Faster Boot Time:** The instance boots faster as the operating system does not need to reboot.
- **Preserved In-Memory State:** RAM contents and long-running processes are preserved.
- **Use Cases:** Suitable for long-running processes or services that take time to initialize.

### Exam Tips

- **Key Points:**
    - **Hibernation Definition:** EC2 hibernation preserves in-memory RAM on persistent storage (EBS), enabling faster boot times without reloading the operating system.
    - **Memory Limit:** RAM must be less than 150 GB for hibernation to work.
    - **Instance Support:** General purpose, compute, memory, and storage optimized instance families are supported.
    - **Operating Systems:** Available for Windows, Amazon Linux 2 AMI, and Ubuntu.
    - **Duration:** Instances can be hibernated for a maximum of 60 days.
    - **Instance Types:** Available for On-Demand and Reserved Instances.

By understanding these key concepts and processes, you will be well-prepared to answer questions about EC2 hibernation in your exam.