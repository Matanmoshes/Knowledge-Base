
### Linux/Unix Commands

#### 1. `ifconfig`
Displays or configures network interfaces.

**Example**:
```sh
ifconfig
```

#### 2. `ip`
Displays or configures IP addresses, routing, and devices.

**Example**:
```sh
ip addr show
ip link show
ip route show
```

#### 3. `netstat`
Displays network connections, routing tables, interface statistics, masquerade connections, and multicast memberships.

**Example**:
```sh
netstat -tuln
netstat -i
```

#### 4. `ss`
Displays socket statistics, similar to `netstat`.

**Example**:
```sh
ss -tuln
ss -s
```

#### 5. `ping`
Sends ICMP ECHO_REQUEST packets to network hosts.

**Example**:
```sh
ping google.com
ping -c 4 8.8.8.8
```

#### 6. `traceroute`
Prints the route that packets take to the network host.

**Example**:
```sh
traceroute google.com
```

#### 7. `dig`
Queries DNS servers for information about domains.

**Example**:
```sh
dig example.com
dig +short google.com
```

#### 8. `nslookup`
Queries DNS to obtain domain name or IP address mapping.

**Example**:
```sh
nslookup google.com
```

#### 9. `host`
Performs DNS lookups.

**Example**:
```sh
host google.com
```

#### 10. `route`
Shows or manipulates the IP routing table.

**Example**:
```sh
route -n
route add default gw 192.168.1.1
```

#### 11. `iptables`
Configures IP packet filter rules (firewall).

**Example**:
```sh
iptables -L
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

#### 12. `curl`
Transfers data from or to a server using various protocols.

**Example**:
```sh
curl http://example.com
curl -I http://example.com
```

#### 13. `wget`
Non-interactive network downloader.

**Example**:
```sh
wget http://example.com
```

#### 14. `nmap`
Network exploration tool and security/port scanner.

**Example**:
```sh
nmap -sP 192.168.1.0/24
nmap -sV -p 1-65535 example.com
```


---

### Windows Commands

#### 1. `ipconfig`
Displays all current TCP/IP network configuration values.

**Example**:
```sh
ipconfig
ipconfig /all
```

#### 2. `netstat`
Displays network connections, routing tables, interface statistics, masquerade connections, and multicast memberships.

**Example**:
```sh
netstat -an
netstat -r
```

#### 3. `ping`
Sends ICMP ECHO_REQUEST packets to network hosts.

**Example**:
```sh
ping google.com
ping -n 4 8.8.8.8
```

#### 4. `tracert`
Prints the route that packets take to the network host.

**Example**:
```sh
tracert google.com
```

#### 5. `nslookup`
Queries DNS to obtain domain name or IP address mapping.

**Example**:
```sh
nslookup google.com
```

#### 6. `route`
Shows or manipulates the IP routing table.

**Example**:
```sh
route print
route add 192.168.1.0 mask 255.255.255.0 192.168.0.1
```

#### 7. `netsh`
Displays or modifies the network configuration of a running system.

**Example**:
```sh
netsh interface ip show config
netsh wlan show profiles
```

#### 8. `arp`
Displays or modifies the ARP cache.

**Example**:
```sh
arp -a
```

#### 9. `pathping`
Combines the functionality of `ping` and `tracert` to find network latency and packet loss.

**Example**:
```sh
pathping google.com
```

#### 10. `telnet`
Used for remote communication with another host using the TELNET protocol.

**Example**:
```sh
telnet 192.168.1.1 23
```


---

### macOS Commands
(macOS shares many networking commands with Linux/Unix, here are a few macOS-specific commands)

#### 1. `ifconfig`
Displays or configures network interfaces.

**Example**:
```sh
ifconfig
```

#### 2. `ip`
Displays or configures IP addresses, routing, and devices.

**Example**:
```sh
ip addr show
ip link show
ip route show
```

#### 3. `ping`
Sends ICMP ECHO_REQUEST packets to network hosts.

**Example**:
```sh
ping google.com
ping -c 4 8.8.8.8
```

#### 4. `traceroute`
Prints the route that packets take to the network host.

**Example**:
```sh
traceroute google.com
```

#### 5. `dig`
Queries DNS servers for information about domains.

**Example**:
```sh
dig example.com
dig +short google.com
```

#### 6. `nslookup`
Queries DNS to obtain domain name or IP address mapping.

**Example**:
```sh
nslookup google.com
```

#### 7. `host`
Performs DNS lookups.

**Example**:
```sh
host google.com
```

#### 8. `route`
Shows or manipulates the IP routing table.

**Example**:
```sh
route -n
route add default gw 192.168.1.1
```

#### 9. `curl`
Transfers data from or to a server using various protocols.

**Example**:
```sh
curl http://example.com
curl -I http://example.com
```

#### 10. `wget`
Non-interactive network downloader.

**Example**:
```sh
wget http://example.com
```

#### 11. `nmap`
Network exploration tool and security/port scanner.

**Example**:
```sh
nmap -sP 192.168.1.0/24
nmap -sV -p 1-65535 example.com
```

### Cross-Platform Commands

#### 1. `ssh`
Used for secure remote login from one computer to another.

**Example**:
```sh
ssh user@hostname
```

#### 2. `scp`
Used to copy files between hosts on a network.

**Example**:
```sh
scp file.txt user@hostname:/path/to/destination
```

#### 3. `ftp`
Used to transfer files between local and remote servers.

**Example**:
```sh
ftp hostname
```

#### 4. `sftp`
Secure file transfer program, similar to `ftp` but uses SSH for security.

**Example**:
```sh
sftp user@hostname
```

This list covers many of the common network commands used across different operating systems. These commands are fundamental tools for network configuration, diagnostics, and management.