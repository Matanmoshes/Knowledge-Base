---

---
---
## Purpose
---
The purpose of this document is to lay out the general configuration process for IPsec VPN Tunnel between the IL office FortiGate Firewall device and the AWS (Amazon Web Services) cloud tenant.
## Scope
---
##### AWS Configurations:
1.  Customer gateway.
2. Target gateway (Virtual private gateway).
3. Routing (Optional).
4. Site-to-Site VPN connection.
5. Static Routing. 
6. Download the configuration file.
##### FortiGate Firewall Configurations:
1. Subnet profiles (Local).
2. Subnets profiles (Remote).
3. VPN Tunnels.
4. Static Routes.
5. Create Policy.
## Needed information:
---
##### AWS:
1. Public IP address of AWS VPN gateway endpoint.
2. AWS LAN IP Range.
3. Amazon VPC CIDR block.
4. encryption and authentication for the VPN tunnel.
##### FortiGate Firewall:
1. Public IP Address for the device.
2. WAN Interface.
3. Local network IP Range (LAN\VLAN).
4. encryption and authentication for the VPN tunnel.
## AWS Configuration
---
##### Costumer Gateway:
1. Open AWS console at [VPC Settings](https://console.aws.amazon.com/vpc/)
2. Select and filter the correct VPC to work on (In Xact case only one VPC exists).
3. Navigate to Virtual Private Network (VPN) > Costumer Gateways > Create new Costumer Gateway.
4. Complete the following and then choose Create customer gateway:
	- Name tag – optional: **Xact-IL-FortiGate-Firewall**
	- BGP ASN: **Default**
	- Certificate ARN: **Default**
	- IP Address: **213.57.X.X**
	- Device – optional: **FortiGate 50E**
##### Create customer gateway:
Target gateway (Virtual private gateway)

1. Navigate to **Virtual Private Network (VPN) > Virtual private gateways > Create virtual private gateway.
2. Complete the following and then choose Create virtual private gateway:
	- Name tag – optional: VPG-FortiGate-IL
	- Autonomous System Number ASN: Amazon default ANS
	- Tags: Optional

3.  Create Virtual Private Gateway.
4. Select the newly created Virtual Private Gateway.
5. Navigate to **Actions > Select Attach to VPC**
6. Select the VPC and click **Attach to VPC**

##### Routing (Optional) - **Currently not intended for use**:

>[!Note]
>If you do not enable route propagation, you must manually enter the static routes used by your Site-to-Site VPN connection. To do this, select your route table, choose Routes, Edit. For Destination, add the static route used by your Site-to-Site VPN connection. For Target, select the virtual private gateway ID, and choose Save.

1. In the left navigation pane, navigate to Route Tables > Select the table that is associated with the subnet.
2. On the Route Propagation tab in the details pane, choose Edit route propagation, select the virtual private gateway that you created in the previous procedure, and then choose Save.

##### Site-to-Site VPN Connection:

1. In the left pane navigate to Virtual private network (VPN) > Site-to-Site VPN connections > Create VPN connection.
2. Complete the following and then choose Create VPN connection:

	- Name tag – optional: **S2S-AWS-to-Fortigate-IL**
	- Target private type: **Virtual private gateway**
	- Virtual private gateway: **Select the virtual private gateway that was previously created.**
	- Customer gateway: **Existing**
	- Customer gateway ID: **Select the Customer gateway that was previously created.**
	- Routing options: **Static**
	- Static IP Prefixes: FortiGate local LAN/VLAN Subnet prefix: **192.168.1.0/24**
	- Local IPv4 network CIDR: FortiGate local LAN/VLAN: **192.168.1.0/24**
	- Remote IPv4 Network CIDR – optional: AWS Local subnet: **172.31.22.0/24**

##### Static Routing:

1. In order to route AWS subnet through the Virtual Gateway, Navigate to **Virtual Private Cloud > Route Tables > Select the existing table > Routes > Edit routes.*
2. Select Add route.
3. Complete the following and then choose Save changes.
	- Destination: **192.168.1.0/24**
	- Target: **Select the previously created Virtual Gateway.**
	- 

##### Download the configuration file:

1. After creating Site-to-Site VPN connection, it is important to download the configuration file to use for configuring the customer gateway device, in the next section.
2. Navigate to **Virtual private network > Site-to-Site VPN connections**
3. Select the VPN connection and choose Download Configuration.
4. Select the following:
	- Vendor: **Generic**
	- Platform: **Generic**
	- Software: **Vendor Agnostic**
	- IKE Version: **IKEv2**

5. Collect the needed information under **“Phase 1”** and **“Phase 2”** in the downloaded configuration file.
6. Also collect the following information under **Outside IP Addresses:**
	- Customer Gateway
	- Virtual Private Gateway

## FortiGate configuration
---
##### Subnet profiles (Local):

1. Navigate Policy & Objects > Addresses > Create New > Address.
2. Complete the following before creating the address:
	- Name: **FortiGate_LAN**
	- Type: **Subnet**
	- IP/Netmask: **192.168.1.0/24**
	- Interface: **Any**

##### Subnet profiles (Remote):

1. Create another subnet for AWS, similar to the previous configuration:
	- Name: **AWS_LAN**
	- Type: **Subnet**
	- IP/Netmask: **172.31.22.0/24**
	- Interface: **Any**

##### VPN Tunnels:

1. Navigate to **VPN > IPsec Tunnels > Create New > IPsec Tunnel**
2. Complete the following for VPN Setup:
	- Name: **FortiGate_VPN_to_AWS**
	- Template Type: **Custom**
	
3. Next
4. Complete the following for Network:
	- IP Version: **IPv4**
	- Remote Gateway: **Static IP Address**
	- IP Address: **Enter the AWS WAN (Public) IP.**
	- Interface: **HOT (wan1)**
	- Local Gateway: **Disabled**
	- Mode Config: **Unchecked**
	- NAT Traversal: **Disabled**
	- Dead Peer Detection: **Disabled**

5. Complete the following for Authentication:
	- Method: **Pre-shared Key**
	- Pre-shared Key: **Enter the password from the Site-to-Site Connection configuration files, previously downloaded from AWS (Pre-shared key is saved under Phase 1).**
	- IKE Version: **2**

6. Complete the following for Phase 1 Proposal:
7. Enter the Phase 1 information according to the downloaded Site-to-Site connection file. 

>[!Note]
>The information for this section will be available after the Site-to-Site configuration and downloading of the configuration file.

- Encryption: N/A
- Authentication: N/A
- Diffie-Hellman Group: N/A
- Key Lifetime (Seconds): N/A

8. Complete the following for Phase 2 Sections:
	- Under Local Address: Enter the FortiGate LAN Subnet range: **192.168.1.0/24**
	- Under Remote Address: Enter the AWS LAN Subnet range: **172.31.22.0/24**

9.  Complete the following for New **Phase 2**:
10. Enter the Phase 2 information according to the downloaded Site-to-Site connection file.

>[!Note]
>The information for this section will be available after the Site-to-Site configuration and downloading of the configuration file.

- Name: **Leave Default.**
- Local Address: **Subnet – 192.168.1.0/24**
- Remote Address: **Subnet – 172.31.22.0/24**

###### Under Advanced settings:
- Encryption: **N/A**
- Authentication: **N/A**
- Diffie-Hellman Group: **N/A**
- Key Liftime: **Seconds**
- Seconds: b

11. Click OK.

##### Static Routes:

1. To create a static route between FortiGate VPN connection and  AWS LAN Subnet, navigate to **Network > Static Routes > Create New**
2. Complete the following for New Static Route:

	- Destination: Enter AWS Subnet: 172.31.22.0/24
	- Interface: Select the newly created IPsec Tunnel
	- Status: Enable

3.  Click **OK**

4. **Create Policy**

5. Navigate to **Policy & Objects > IPv4 Policy > Create New**

6. Complete the following for New LAN-to-AWS Policy:

	- Name: LAN-to-AWS
	- Incoming Interface: lan
	- Outgoing Interface: Select the VPN tunnel.
	- Source: FortiGate_LAN
	- Destination: AWS_LAN
	- Service: ALL
	- Action: ACCEPT
	- Enable tis policy: ON

7. Click **OK**
8. Complete the following for New AWS-TO-LAN Policy:

	- Name: AWS-to-LAN
	- Incoming Interface: Select the VPN tunnel.
	- Outgoing Interface: lan
	- Source: AWS_LAN
	- Destination: FortiGate_LAN
	- Service: ALL
	- Action: ACCEPT
	- Enable tis policy: ON

9. Click **OK**



>[!Sources]
>[AWS Documentation](https://docs.aws.amazon.com/vpn/latest/s2svpn/SetUpVPNConnections.html#vpn-configure-customer-gateway-device)
>
>[Fortinet Documentation](https://docs.fortinet.com/document/fortigate-public-cloud/6.2.0/aws-administration-guide/506140/connecting-a-local-fortigate-to-an-aws-vpc-vpn#FGT)
>
>[techbast.com Tutorial](https://techbast.com/2016/07/sophos-xg-configuring-site-site-ipsec-vpn-failover-group.html)
>
>[[Solving Licensing Issues with Dedicated Hosts]]


