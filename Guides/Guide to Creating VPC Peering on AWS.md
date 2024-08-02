### Guide to Creating VPC Peering on AWS

#### Introduction
VPC Peering allows you to connect two VPCs (Virtual Private Clouds) to route traffic between them using private IP addresses. This setup can be useful for scenarios like allowing communication between different environments (e.g., production and development), sharing resources across different accounts, or extending your infrastructure.

#### Prerequisites
- AWS Account with necessary IAM permissions.
- Two VPCs in the same or different AWS Regions.

### Step-by-Step Guide

#### Step 1: Create Two VPCs
1. **Login to AWS Management Console**.
2. Navigate to the **VPC Dashboard**.
3. **Create VPC**:
   - **VPC A**: 
     - Name: `VPC-A`
     - CIDR: `10.0.0.0/16`
   - **VPC B**: 
     - Name: `VPC-B`
     - CIDR: `10.1.0.0/16`

#### Step 2: Create Subnets in Each VPC
1. Navigate to the **Subnets** section in the VPC Dashboard.
2. **Create Subnet**:
   - For **VPC A**:
     - Subnet name: `Subnet-A`
     - VPC: `VPC-A`
     - CIDR: `10.0.1.0/24`
   - For **VPC B**:
     - Subnet name: `Subnet-B`
     - VPC: `VPC-B`
     - CIDR: `10.1.1.0/24`

#### Step 3: Create VPC Peering Connection
1. Navigate to the **Peering Connections** section.
2. **Create Peering Connection**:
   - Peering Connection Name: `Peering-A-B`
   - **Requester VPC**: Select `VPC-A`
   - **Accepter VPC**: Select `VPC-B` (can be in the same or different AWS account/region)

#### Step 4: Accept the Peering Connection
1. After creating the peering connection, navigate to the **Peering Connections** section.
2. Select the created peering connection (`Peering-A-B`).
3. Click **Actions** and select **Accept Request**.

#### Step 5: Update Route Tables for Both VPCs
1. Navigate to the **Route Tables** section in the VPC Dashboard.
2. **Update Route Table for VPC A**:
   - Select the route table associated with `VPC-A`.
   - Add a new route:
     - Destination: `10.1.0.0/16` (CIDR of VPC-B)
     - Target: Select the VPC Peering connection (`Peering-A-B`)

3. **Update Route Table for VPC B**:
   - Select the route table associated with `VPC-B`.
   - Add a new route:
     - Destination: `10.0.0.0/16` (CIDR of VPC-A)
     - Target: Select the VPC Peering connection (`Peering-A-B`)

#### Step 6: Update Security Groups
1. Navigate to the **Security Groups** section.
2. **Update Security Group for VPC A**:
   - Allow inbound traffic from `10.1.0.0/16`.
   - Allow outbound traffic to `10.1.0.0/16`.

3. **Update Security Group for VPC B**:
   - Allow inbound traffic from `10.0.0.0/16`.
   - Allow outbound traffic to `10.0.0.0/16`.

#### Step 7: Verify the Peering Connection
1. Launch EC2 instances in `Subnet-A` and `Subnet-B`.
2. Configure the instances to allow ICMP (ping) requests.
3. Use the EC2 instance in `Subnet-A` to ping the private IP address of the EC2 instance in `Subnet-B` and vice versa.

### Exercise: Hands-on VPC Peering

#### Objective
To establish a peering connection between two VPCs and verify connectivity.

#### Steps
1. **Create VPCs**:
   - `VPC-C` with CIDR `10.2.0.0/16`
   - `VPC-D` with CIDR `10.3.0.0/16`

2. **Create Subnets**:
   - `Subnet-C` in `VPC-C` with CIDR `10.2.1.0/24`
   - `Subnet-D` in `VPC-D` with CIDR `10.3.1.0/24`

3. **Create and Accept Peering Connection**:
   - Name: `Peering-C-D`
   - Requester VPC: `VPC-C`
   - Accepter VPC: `VPC-D`

4. **Update Route Tables**:
   - For `VPC-C`: Route to `10.3.0.0/16` through `Peering-C-D`
   - For `VPC-D`: Route to `10.2.0.0/16` through `Peering-C-D`

5. **Update Security Groups**:
   - Allow traffic between `10.2.0.0/16` and `10.3.0.0/16`.

6. **Launch EC2 Instances**:
   - Instance in `Subnet-C`
   - Instance in `Subnet-D`

7. **Verify Connectivity**:
   - Ping the instance in `Subnet-D` from the instance in `Subnet-C` and vice versa.

By following these steps and completing the exercise, you will have hands-on experience in setting up and verifying VPC peering connections on AWS.