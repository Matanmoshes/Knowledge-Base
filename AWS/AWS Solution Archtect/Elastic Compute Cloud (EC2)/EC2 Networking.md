### <span style="color:#ffc000">EC2 Networking Options - Key Points</span>

#### <span style="color:#ffc000">Introduction</span>
- **Objective**: Understand different networking options with EC2.
- **Components**:
  - ENIs (Elastic Network Interfaces)
  - Enhanced Networking (EN)
  - ENA vs. VF (Elastic Network Adapter vs. Virtual Function)
  - EFA (Elastic Fabric Adapter)
  - Exam tips

#### <span style="color:#ffc000">Networking Options</span>

![[Pasted image 20240608124757.png]]

1. **<span style="color:#d68a8a">ENI (Elastic Network Interface)</span>**:
   - **Definition**: Virtual network card for basic day-to-day networking.
   - **Features**:
     - Private IPv4 address
     - MAC address
     - Public IPv4 address
     - One or more security groups (virtual firewalls)
     - Multiple IPv6 addresses
   - **Use Cases**:
     - Creating management networks
     - Using network and security appliances in VPC
     - Creating dual-homed instances with distinct subnets for different roles or workloads
     - Low budget, high availability solutions

2. **<span style="color:#d68a8a">Enhanced Networking (ENA)</span>**:
   - **Definition**: High-performance networking offering between 10 and 100 gigabits per second.
   - **Technology**: Uses Single Root I/O Virtualization (SR-IOV) for higher I/O performance and lower CPU utilization.
   - **Benefits**:
     - Higher bandwidth
     - Higher packets per second performance
     - Consistently lower inter-instance latency
   - **Options**:
     - **Elastic Network Adapter (ENA)**:
       - Supports speeds up to 100 gigabits per second
       - Preferred for modern, high-performance requirements
     - **Intel 82599 Virtual Function (VF)**:
       - Supports speeds up to 10 gigabits per second
       - Used in older instances
   - **Recommendation**: Prefer ENA over VF for better performance.

3. **<span style="color:#d68a8a">EFA (Elastic Fabric Adapter)</span>**:
   - **Definition**: Network device to accelerate high-performance computing (HPC) and machine learning applications.
   - **Benefits**:
     - Lower and more consistent latency
     - Higher throughput than traditional TCP transport in cloud-based HPC systems
   - **Features**:
     - **OS-bypass**: Allows HPC and machine learning applications to bypass the OS kernel, communicating directly with the EFA device for faster performance and lower latency.
     - **Support**: Only available on Linux, not supported on Windows.
   - **Use Cases**: HPC, machine learning, scenarios requiring high throughput and low latency.

#### <span style="color:#ffc000">Exam Tips</span>

1. **Know the Three Network Adapter Types**:
   - **ENI**: Basic networking; use for separate management, logging, or monitoring networks at low cost.
   - **Enhanced Networking**: High throughput, reliable networking between 10 and 100 gigabits per second.
   - **EFA**: High performance computing or machine learning; OS-bypass for lower latency.

2. **Scenarios**:
   - **ENI**: Use for low-budget, high availability, and multiple separate networks.
   - **Enhanced Networking**: Use ENA for modern high-performance needs, VF for older instances.
   - **EFA**: Use for high-performance computing, machine learning, and when OS-level bypass is needed.

This summary provides a comprehensive guide to understanding EC2 networking options, essential for selecting the right networking solutions for different use cases and preparing for the AWS Certified Solutions Architect - Associate exam.