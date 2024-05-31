### IP Addressing

IP addresses are numerical labels assigned to each device connected to a computer network that uses the Internet Protocol for communication. They serve two main functions: identifying the host or network interface and providing the location of the host in the network.

#### Types of IP Addresses

1. **Private IP Addresses**:
   - **Purpose**: Used within a private network, not routable on the public internet.
   - **Ranges**:
     - **Class A**: 10.0.0.0 to 10.255.255.255
     - **Class B**: 172.16.0.0 to 172.31.255.255
     - **Class C**: 192.168.0.0 to 192.168.255.255
   - **Usage**: Commonly used in local area networks (LANs) for devices such as computers, printers, and smartphones.

2. **Public IP Addresses**:
   - **Purpose**: Routable on the public internet, unique across the entire internet.
   - **Assignment**: Assigned by Internet Service Providers (ISPs) and must be unique for each device on the global internet.
   - **Usage**: Used by devices directly connected to the internet, such as web servers and routers.

#### Subnet Mask

A subnet mask is used to divide an IP address into network and host parts. It helps in determining the network boundary. A subnet mask consists of 32 bits, like an IP address, and is typically written in the same dot-decimal notation (e.g., 255.255.255.0).

- **Purpose**: Defines which part of an IP address is the network portion and which part is the host portion.
- **Example**: For an IP address of 192.168.1.1 with a subnet mask of 255.255.255.0:
  - Network Portion: 192.168.1
  - Host Portion: 1

#### IP Address Classes

IPv4 addresses are categorized into five classes (A to E), primarily for the purpose of IP addressing and routing. Classes A, B, and C are used for general networking.

1. **Class A**:
   - **Range**: 0.0.0.0 to 127.255.255.255
   - **Default Subnet Mask**: 255.0.0.0
   - **Network Bits**: 8 bits
   - **Host Bits**: 24 bits
   - **Number of Networks**: 128 (but 0.0.0.0 and 127.0.0.0 are reserved)
   - **Number of Hosts per Network**: 16,777,214
   - **Usage**: Large networks with many devices, such as ISPs or large enterprises.

2. **Class B**:
   - **Range**: 128.0.0.0 to 191.255.255.255
   - **Default Subnet Mask**: 255.255.0.0
   - **Network Bits**: 16 bits
   - **Host Bits**: 16 bits
   - **Number of Networks**: 16,384
   - **Number of Hosts per Network**: 65,534
   - **Usage**: Medium to large-sized networks, such as universities and large businesses.

3. **Class C**:
   - **Range**: 192.0.0.0 to 223.255.255.255
   - **Default Subnet Mask**: 255.255.255.0
   - **Network Bits**: 24 bits
   - **Host Bits**: 8 bits
   - **Number of Networks**: 2,097,152
   - **Number of Hosts per Network**: 254
   - **Usage**: Small networks, such as small businesses or home networks.

#### Additional Classes

4. **Class D**:
   - **Range**: 224.0.0.0 to 239.255.255.255
   - **Purpose**: Reserved for multicast groups.
   - **Usage**: Used for one-to-many communication, such as streaming media.

5. **Class E**:
   - **Range**: 240.0.0.0 to 255.255.255.255
   - **Purpose**: Reserved for experimental purposes and future use.
   - **Usage**: Not used for standard public or private networking.

#### Important Points

- **Loopback Address**: 127.0.0.1 is used to refer to the localhost, or the device itself.
- **Broadcast Address**: Used to send data to all possible destinations in a network. For example, 192.168.1.255 in a Class C network.

#### CIDR (Classless Inter-Domain Routing)

CIDR allows for a more flexible allocation of IP addresses than the traditional class-based method. It uses a prefix length (e.g., /24) to denote the subnet mask.

- **Example**: 192.168.1.0/24
  - **Prefix Length**: /24 indicates a subnet mask of 255.255.255.0.
  - **Usage**: Efficiently allocates IP addresses and reduces waste.

- **Representation**: Subnet masks in CIDR are represented by the number of bits set to 1.
  - `/24` (255.255.255.0): 11111111.11111111.11111111.00000000
  - `/16` (255.255.0.0): 11111111.11111111.00000000.00000

Understanding IP addressing, including the distinction between private and public IP addresses, subnet masks, and the different IP address classes, is crucial for network design, configuration, and management.

