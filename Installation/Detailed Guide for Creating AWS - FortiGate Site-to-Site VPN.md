## Detailed Guide for Creating AWS - FortiGate Site-to-Site VPN

### Purpose
This guide provides a detailed, step-by-step process to configure an IPsec VPN Tunnel between the IL office FortiGate Firewall device and an AWS (Amazon Web Services) cloud tenant.

### Scope

#### AWS Configurations:
1. Customer gateway
2. Target gateway (Virtual private gateway)
3. Routing (Optional)
4. Site-to-Site VPN connection
5. Static Routing
6. Download the configuration file

#### FortiGate Firewall Configurations:
1. Subnet profiles (Local)
2. Subnet profiles (Remote)
3. VPN Tunnels
4. Static Routes
5. Create Policy

### Needed Information:

#### AWS:
1. Public IP address of AWS VPN gateway endpoint
2. AWS LAN IP Range
3. Amazon VPC CIDR block
4. Encryption and authentication for the VPN tunnel

#### FortiGate Firewall:
1. Public IP Address for the device
2. WAN Interface
3. Local network IP Range (LAN/VLAN)
4. Encryption and authentication for the VPN tunnel

---

### AWS Configuration

#### Customer Gateway:

1. **Open AWS Console:**
   - Navigate to the [VPC Settings](https://console.aws.amazon.com/vpc/).

2. **Select the Correct VPC:**
   - Filter and select the correct VPC to work on (usually, there is only one VPC in small setups).

3. **Create Customer Gateway:**
   - Navigate to **Virtual Private Network (VPN) > Customer Gateways > Create Customer Gateway**.
   - Complete the form:
     - **Name tag** (optional): `Xact-IL-FortiGate-Firewall`
     - **BGP ASN**: Default
     - **Certificate ARN**: Default
     - **IP Address**: `213.57.X.X` (replace with your IP)
     - **Device** (optional): `FortiGate 50E`
   - Click **Create Customer Gateway**.

#### Virtual Private Gateway:

1. **Create Virtual Private Gateway:**
   - Navigate to **Virtual Private Network (VPN) > Virtual private gateways > Create virtual private gateway**.
   - Complete the form:
     - **Name tag** (optional): `VPG-FortiGate-IL`
     - **Autonomous System Number ASN**: Amazon default ASN
   - Click **Create Virtual Private Gateway**.

2. **Attach Virtual Private Gateway to VPC:**
   - Select the newly created Virtual Private Gateway.
   - Navigate to **Actions > Attach to VPC**.
   - Select the VPC and click **Attach to VPC**.

#### Routing (Optional):

**Note: Currently not intended for use.**

If manual route configuration is required:
1. Navigate to **Route Tables** in the left navigation pane.
2. Select the relevant route table associated with the subnet.
3. Go to the **Route Propagation** tab, click **Edit route propagation**.
4. Select the virtual private gateway created earlier and click **Save**.

#### Site-to-Site VPN Connection:

1. **Create VPN Connection:**
   - Navigate to **Virtual private network (VPN) > Site-to-Site VPN connections > Create VPN connection**.
   - Complete the form:
     - **Name tag** (optional): `S2S-AWS-to-Fortigate-IL`
     - **Target gateway type**: `Virtual private gateway`
     - **Virtual private gateway**: Select the created Virtual Private Gateway.
     - **Customer gateway**: `Existing`
     - **Customer gateway ID**: Select the created Customer Gateway.
     - **Routing options**: `Static`
     - **Static IP Prefixes**: `192.168.1.0/24` (FortiGate local LAN/VLAN Subnet prefix)
     - **Local IPv4 network CIDR**: `192.168.1.0/24` (FortiGate local LAN/VLAN)
     - **Remote IPv4 Network CIDR**: `172.31.22.0/24` (AWS Local subnet)
   - Click **Create VPN connection**.

#### Static Routing:

1. **Configure Route Table:**
   - Navigate to **Virtual Private Cloud > Route Tables > Select the existing table > Routes > Edit routes**.
   - Add a new route:
     - **Destination**: `192.168.1.0/24`
     - **Target**: Select the previously created Virtual Private Gateway.
   - Click **Save changes**.

#### Download the Configuration File:

1. **Download Configuration File:**
   - Navigate to **Virtual private network > Site-to-Site VPN connections**.
   - Select the VPN connection and choose **Download Configuration**.
   - Select the following options:
     - **Vendor**: `Generic`
     - **Platform**: `Generic`
     - **Software**: `Vendor Agnostic`
     - **IKE Version**: `IKEv2`
   - Download and review the configuration file for Phase 1 and Phase 2 information.

### FortiGate Configuration

#### Subnet Profiles (Local):

1. **Create Local Subnet Profile:**
   - Navigate to **Policy & Objects > Addresses > Create New > Address**.
   - Complete the form:
     - **Name**: `FortiGate_LAN`
     - **Type**: `Subnet`
     - **IP/Netmask**: `192.168.1.0/24`
     - **Interface**: `Any`
   - Click **OK**.

#### Subnet Profiles (Remote):

1. **Create Remote Subnet Profile:**
   - Create another subnet for AWS, similar to the previous configuration:
     - **Name**: `AWS_LAN`
     - **Type**: `Subnet`
     - **IP/Netmask**: `172.31.22.0/24`
     - **Interface**: `Any`
   - Click **OK**.

#### VPN Tunnels:

1. **Create VPN Tunnel:**
   - Navigate to **VPN > IPsec Tunnels > Create New > IPsec Tunnel**.
   - Complete the VPN Setup form:
     - **Name**: `FortiGate_VPN_to_AWS`
     - **Template Type**: `Custom`
   - Click **Next**.

2. **Network Configuration:**
   - Complete the Network form:
     - **IP Version**: `IPv4`
     - **Remote Gateway**: `Static IP Address`
     - **IP Address**: Enter the AWS WAN (Public) IP.
     - **Interface**: `HOT (wan1)`
     - **Local Gateway**: `Disabled`
     - **Mode Config**: `Unchecked`
     - **NAT Traversal**: `Disabled`
     - **Dead Peer Detection**: `Disabled`

3. **Authentication Configuration:**
   - Complete the Authentication form:
     - **Method**: `Pre-shared Key`
     - **Pre-shared Key**: Enter the pre-shared key from the Site-to-Site Connection configuration file.
     - **IKE Version**: `2`

4. **Phase 1 Proposal:**
   - Enter the Phase 1 information from the Site-to-Site connection file:
     - **Encryption**: N/A
     - **Authentication**: N/A
     - **Diffie-Hellman Group**: N/A
     - **Key Lifetime (Seconds)**: N/A

5. **Phase 2 Proposal:**
   - Complete the Phase 2 form:
     - **Local Address**: `192.168.1.0/24` (FortiGate LAN Subnet range)
     - **Remote Address**: `172.31.22.0/24` (AWS LAN Subnet range)

6. **Advanced Settings for Phase 2:**
   - Enter the Phase 2 information from the Site-to-Site connection file:
     - **Encryption**: N/A
     - **Authentication**: N/A
     - **Diffie-Hellman Group**: N/A
     - **Key Lifetime (Seconds)**: N/A

7. **Finalize and Save:**
   - Click **OK** to finalize the VPN tunnel configuration.

#### Static Routes:

1. **Create Static Route:**
   - Navigate to **Network > Static Routes > Create New**.
   - Complete the form:
     - **Destination**: `172.31.22.0/24` (AWS Subnet)
     - **Interface**: Select the newly created IPsec Tunnel
     - **Status**: Enable
   - Click **OK**.

#### Create Policy:

1. **Create LAN-to-AWS Policy:**
   - Navigate to **Policy & Objects > IPv4 Policy > Create New**.
   - Complete the form:
     - **Name**: `LAN-to-AWS`
     - **Incoming Interface**: `lan`
     - **Outgoing Interface**: Select the VPN tunnel
     - **Source**: `FortiGate_LAN`
     - **Destination**: `AWS_LAN`
     - **Service**: `ALL`
     - **Action**: `ACCEPT`
     - **Enable this policy**: `ON`
   - Click **OK**.

2. **Create AWS-to-LAN Policy:**
   - Complete the form:
     - **Name**: `AWS-to-LAN`
     - **Incoming Interface**: Select the VPN tunnel
     - **Outgoing Interface**: `lan`
     - **Source**: `AWS_LAN`
     - **Destination**: `FortiGate_LAN`
     - **Service**: `ALL`
     - **Action**: `ACCEPT`
     - **Enable this policy**: `ON`
  

 - Click **OK**.

---

### Sources
- [AWS Documentation](https://docs.aws.amazon.com/vpn/latest/s2svpn/SetUpVPNConnections.html#vpn-configure-customer-gateway-device)
- [Fortinet Documentation](https://docs.fortinet.com/document/fortigate-public-cloud/6.2.0/aws-administration-guide/506140/connecting-a-local-fortigate-to-an-aws-vpc-vpn#FGT)
- [techbast.com Tutorial](https://techbast.com/2016/07/sophos-xg-configuring-site-site-ipsec-vpn-failover-group.html)
- [Create Site-To-Site VPN with AWS](https://aws.amazon.com/vpn/site-to-site/)

This guide should provide you with a comprehensive understanding of how to set up a Site-to-Site VPN between AWS and a FortiGate firewall, ensuring secure and reliable connectivity between the two environments.