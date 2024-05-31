DORA (Discover, Offer, Request, Acknowledge) is a process used in the Dynamic Host Configuration Protocol (DHCP) to assign IP addresses and other network configuration details to devices on a network. Here's a detailed explanation of each step in the DORA process:

### 1. Discover (DHCPDISCOVER)

**Purpose**: The client broadcasts a request to find available DHCP servers.

- **Initiation**: When a client device (like a computer or smartphone) joins a network and needs an IP address, it sends out a DHCPDISCOVER message. This message is a broadcast packet because the client doesn't yet know the IP addresses of the available DHCP servers.
- **Broadcast**: The DHCPDISCOVER message is sent to the IP address 255.255.255.255 (broadcast address), ensuring all devices on the local network segment can receive it.
- **Contents**: The DHCPDISCOVER packet contains the client's MAC address and potentially some other configuration parameters, but it does not yet have an IP address assigned.

### 2. Offer (DHCPOFFER)

**Purpose**: DHCP servers respond to the client's discovery request with an offer.

- **Server Response**: Upon receiving a DHCPDISCOVER message, one or more DHCP servers respond with a DHCPOFFER message. Each server that can provide an IP address and other configuration information sends this message.
- **Unicast/Broadcast**: The DHCPOFFER message is sent as a broadcast or unicast. The broadcast ensures it reaches the client even if the client does not yet have an IP address. If the server knows the client's MAC address, it can use unicast.
- **Contents**: The DHCPOFFER message includes an available IP address for the client, the subnet mask, lease duration, and other network configuration details like the default gateway and DNS servers.

![[Pasted image 20240530203350.png]]

### 3. Request (DHCPREQUEST)

**Purpose**: The client responds to the offer by requesting the offered IP address.

- **Client Selection**: The client selects one of the offers (if it receives multiple offers from different DHCP servers) and responds with a DHCPREQUEST message, indicating which IP address it has chosen.
- **Broadcast**: The DHCPREQUEST message is usually broadcasted, so all DHCP servers know that the client has chosen a specific server's offer and that it is declining other offers.
- **Contents**: The DHCPREQUEST includes the chosen IP address and the server identifier (the IP address of the DHCP server that made the offer).

### 4. Acknowledge (DHCPACK)

**Purpose**: The selected DHCP server acknowledges the request and finalizes the lease.

- **Finalization**: The DHCP server that provided the chosen offer responds with a DHCPACK message, confirming the IP address lease to the client.
- **Configuration Details**: The DHCPACK message contains all the necessary network configuration parameters, such as the assigned IP address, subnet mask, default gateway, DNS servers, and lease duration.
- **Lease Confirmation**: Upon receiving the DHCPACK, the client configures its network interface with the assigned IP address and other parameters, completing the DHCP configuration process.

### Additional Considerations

- **Lease Renewal**: The DHCP lease has a limited duration. The client must periodically renew the lease by sending a DHCPREQUEST message directly to the DHCP server before the lease expires. The server responds with a DHCPACK to renew the lease.
- **Rebinding**: If the lease renewal process fails (e.g., the server is unreachable), the client attempts to rebind by sending a broadcast DHCPREQUEST message to any available DHCP server.
- **Releasing an IP Address**: If a client no longer needs the IP address (e.g., when it disconnects from the network), it can send a DHCPRELEASE message to the server to release the IP address back to the pool of available addresses.

### Diagram of the DORA Process

```plaintext
Client                          DHCP Server
  |                                 |
  |--DHCPDISCOVER------------------>|
  |                                 |
  |<---------DHCPOFFER--------------|
  |                                 |
  |--DHCPREQUEST------------------->|
  |                                 |
  |<----------DHCPACK---------------|
  |                                 |
```

In summary, the DORA process ensures that devices on a network can dynamically obtain IP addresses and other necessary configuration details, allowing them to communicate effectively on the network without manual configuration.