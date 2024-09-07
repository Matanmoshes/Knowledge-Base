# **Linux Net-Tools Guide**

Here’s a guide for **Linux net-tools**, which is a collection of utilities for networking in Linux systems. These tools help with managing and troubleshooting network configurations and connections.

---

## 1. **ifconfig**
The `ifconfig` command is used to configure network interfaces. It's one of the older networking commands, but it’s still available in many Linux distributions. It can be used to view or configure IP addresses, enable or disable interfaces, and more.

- **View network interfaces and IP configuration:**
  ```bash
  ifconfig
  ```

- **Assign an IP address to an interface:**
  ```bash
  sudo ifconfig eth0 192.168.1.100 netmask 255.255.255.0
  ```

- **Bring up or down a network interface:**
  ```bash
  sudo ifconfig eth0 up
  sudo ifconfig eth0 down
  ```

---

## 2. **route**
The `route` command is used to view or manipulate the routing table.

- **View the routing table:**
  ```bash
  route -n
  ```

- **Add a new default gateway:**
  ```bash
  sudo route add default gw 192.168.1.1 eth0
  ```

- **Delete a route:**
  ```bash
  sudo route del default gw 192.168.1.1
  ```

---

## 3. **netstat**
`netstat` is used to display network connections, routing tables, interface statistics, masquerade connections, and multicast memberships.

- **List all open ports:**
  ```bash
  netstat -tuln
  ```

- **Show network statistics:**
  ```bash
  netstat -s
  ```

- **Display routing table:**
  ```bash
  netstat -r
  ```

---

## 4. **arp**
The `arp` command is used to view and manipulate the ARP (Address Resolution Protocol) cache.

- **View the ARP table:**
  ```bash
  arp -a
  ```

- **Add a static ARP entry:**
  ```bash
  sudo arp -s 192.168.1.2 00:11:22:33:44:55
  ```

- **Delete an ARP entry:**
  ```bash
  sudo arp -d 192.168.1.2
  ```

---

## 5. **hostname**
`hostname` is used to display or set the system’s hostname.

- **Show the current hostname:**
  ```bash
  hostname
  ```

- **Set a new hostname:**
  ```bash
  sudo hostname new-hostname
  ```

---

## 6. **ping**
`ping` is used to check the network connectivity between your system and a remote host by sending ICMP echo request packets.

- **Check connectivity to a host:**
  ```bash
  ping google.com
  ```

- **Limit the number of pings:**
  ```bash
  ping -c 4 google.com
  ```

---

## 7. **nslookup**
`nslookup` is used to query DNS information, such as IP addresses associated with domain names.

- **Query DNS for a domain:**
  ```bash
  nslookup google.com
  ```

- **Query specific DNS server:**
  ```bash
  nslookup google.com 8.8.8.8
  ```

---

## 8. **dig**
`dig` (Domain Information Groper) is another tool for querying DNS information and is more feature-rich than `nslookup`.

- **Query DNS for a domain:**
  ```bash
  dig google.com
  ```

- **Query specific record types (e.g., MX for mail servers):**
  ```bash
  dig google.com MX
  ```

---

## 9. **traceroute**
`traceroute` is used to track the path packets take to a destination, helping diagnose routing problems.

- **Track route to a destination:**
  ```bash
  traceroute google.com
  ```

---
## 10. **mii-tool**
`mii-tool` is used to configure or check the status of Ethernet interfaces.

- **Check the status of an Ethernet interface:**
  ```bash
  sudo mii-tool eth0
  ```

- **Force the interface to renegotiate its link:**
  ```bash
  sudo mii-tool -r eth0
  ```

---

## 11. **ethtool**
`ethtool` is used to query or control network driver and hardware settings.

- **Show Ethernet device information:**
  ```bash
  sudo ethtool eth0
  ```

- **Change speed and duplex settings:**
  ```bash
  sudo ethtool -s eth0 speed 100 duplex full
  ```

---

## 12. **iwconfig**
`iwconfig` is used for configuring wireless network interfaces.

- **Show wireless network information:**
  ```bash
  iwconfig
  ```

- **Connect to a wireless network (basic):**
  ```bash
  sudo iwconfig wlan0 essid "your-network-name" key s:your-password
  ```

---

## 13. **ifup/ifdown**
`ifup` and `ifdown` are used to bring network interfaces up or down, often controlled via the `/etc/network/interfaces` configuration.

- **Bring up a network interface:**
  ```bash
  sudo ifup eth0
  ```

- **Bring down a network interface:**
  ```bash
  sudo ifdown eth0
  ```

---

## 14. **tcpdump**
`tcpdump` is a powerful network packet analyzer used to capture and analyze network traffic.

- **Capture packets on an interface:**
  ```bash
  sudo tcpdump -i eth0
  ```

- **Capture and save packets to a file:**
  ```bash
  sudo tcpdump -i eth0 -w capture.pcap
  ```

---
## 15. **nmcli**
`nmcli` is a command-line client for managing NetworkManager, which handles network interfaces and connections.

- **Show all available network connections:**
  ```bash
  nmcli connection show
  ```

- **Bring up a connection:**
  ```bash
  nmcli connection up <connection-name>
  ```

- **Add a new connection (Wired Ethernet example):**
  ```bash
  nmcli connection add type ethernet con-name "my-connection" ifname eth0
  ```

