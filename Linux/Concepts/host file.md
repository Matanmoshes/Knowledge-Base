The `/etc/hosts` file is a system file that contains static mappings of IP addresses to hostnames. It is used by the operating system to resolve hostnames to IP addresses without querying a DNS server. This file is essential for network communication and can help in quick and local hostname resolution.

### Structure of `/etc/hosts`

The `/etc/hosts` file typically contains lines with the following structure:
```plaintext
IP_address  hostname  [aliases...]
```
- **IP_address**: The IP address of the host.
- **hostname**: The primary name associated with the IP address.
- **aliases** (optional): Additional names that can also be used to refer to the same IP address.

### Example of `/etc/hosts`

Here is an example of a typical `/etc/hosts` file:
```plaintext
127.0.0.1       localhost
127.0.1.1       myhostname

# The following lines are desirable for IPv6 capable hosts
::1             ip6-localhost ip6-loopback
fe00::0         ip6-localnet
ff00::0         ip6-mcastprefix
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
```

### Common Entries

- **`127.0.0.1 localhost`**: This entry maps the loopback address (`127.0.0.1`) to the hostname `localhost`. This is used for network services running on the local machine.
- **`127.0.1.1 myhostname`**: This entry maps an IP address to the actual hostname of the machine (`myhostname`). This is useful for systems that are not assigned a static IP address or for network configurations that rely on a dynamic IP address.

### Purpose and Usage

1. **Local Name Resolution**: Before querying DNS servers, the system checks the `/etc/hosts` file to resolve hostnames. If a match is found, the corresponding IP address is used.
2. **Local Development**: Developers often use the `/etc/hosts` file to set up local development environments by mapping project-specific hostnames to `localhost` or other IP addresses.
3. **Blocking Unwanted Content**: By mapping unwanted or malicious domains to `127.0.0.1`, users can effectively block access to those domains.

### Managing the `/etc/hosts` File

To modify the `/etc/hosts` file, you typically need administrative (root) privileges. Here’s how you can edit it:

1. **Open the file with a text editor** (e.g., `nano`):
   ```sh
   sudo nano /etc/hosts
   ```

2. **Add or modify entries** as needed. For example:
   ```plaintext
   127.0.0.1       localhost
   127.0.1.1       myhostname
   192.168.1.100   webserver
   ```

3. **Save and exit** the text editor:
   - For `nano`, press `Ctrl+O` to save and `Ctrl+X` to exit.

### Ensuring Correct Configuration

For the hostname resolution issue you encountered, ensure that there is an entry in `/etc/hosts` for your hostname:

```plaintext
127.0.0.1       localhost
127.0.1.1       ip-10-0-163-180
```

This ensures that the system can resolve `ip-10-0-163-180` to an IP address, which prevents the `sudo: unable to resolve host` error.