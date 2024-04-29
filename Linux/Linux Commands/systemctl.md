# `systemctl` Command
[systemctl]

To get a complete overview of all `systemctl` commands and options directly in the shell, you can use the `systemctl` command with the `--help` option. This will list all the commands, options, and a brief description of their usage. Here's how you can do it:

```bash
systemctl --help
```

This command will display a detailed help message covering all available `systemctl` commands, their syntax, and options. For a more structured and categorized overview, here’s how you can use common `systemctl` commands in the shell for various purposes:

### Starting, Stopping, and Managing Services
```bash
# Start a service
systemctl start <service_name>

# Stop a service
systemctl stop <service_name>

# Restart a service
systemctl restart <service_name>

# Reload service configuration
systemctl reload <service_name>

# Check the status of a service
systemctl status <service_name>
```

### Enabling and Disabling Services at Boot
```bash
# Enable a service to start at boot
systemctl enable <service_name>

# Disable a service from starting at boot
systemctl disable <service_name>
```

### Listing and Analyzing System Units
```bash
# List all active units
systemctl list-units

# List all installed unit files
systemctl list-unit-files

# Show a tree of the unit dependencies
systemctl list-dependencies <service_name>
```

### System Power Management
```bash
# Reboot the system
systemctl reboot

# Shut down and power off the system
systemctl poweroff

# Suspend the system
systemctl suspend

# Put the system into hibernation
systemctl hibernate
```

### Handling Failed Services
```bash
# Show services that have entered a failed state
systemctl --failed

# Reset the failed state of all units
systemctl reset-failed
```

### Advanced Commands
```bash
# Mask a service to prevent it from being started
systemctl mask <service_name>

# Unmask a service
systemctl unmask <service_name>
```



---

##  <span style="color:#00b050">Install, configure and troubleshoot bootloaders</span>

In Linux, you can boot or change your system into different operating modes, known as "runlevels" in traditional init systems, and "targets" in systems using `systemd`. The concept of targets in `systemd` is more flexible and powerful than traditional runlevels, allowing for better control over what services and processes start at various stages of the system's boot process or during its operation.

Here’s an overview of how to manage these targets with `systemd`, which is the initialization system used by many modern Linux distributions such as Fedora, Ubuntu (starting from 15.04), and CentOS/RHEL 7 and later.

### Common Systemd Targets
- **`poweroff.target`**: Shuts down and powers off the system.
- **`rescue.target`**: Launches a minimal environment for troubleshooting (similar to single-user mode).
- **`multi-user.target`**: Boots the system into a multi-user mode with networking but without a graphical interface, similar to runlevel 3 in older systems.
- **`graphical.target`**: Boots the system into a multi-user, graphical mode, similar to runlevel 5.
- **`reboot.target`**: Reboots the system.
- **`emergency.target`**: Provides a minimal environment and boots the system into emergency mode for maximum troubleshooting.

### Changing Targets
To change the current target and therefore the mode of the system, you use the `systemctl` command. Here’s how to manage these changes:

#### Check the Current Target
```bash
systemctl get-default
```

#### Set a Default Target
To permanently change the default boot target, use:
```bash
systemctl set-default <target_name>
```
For example, to set the system to boot into graphical mode by default:
```bash
systemctl set-default graphical.target
```

#### Switch to Another Target
To change the target temporarily (i.e., until the next reboot), use:
```bash
systemctl isolate <target_name>
```
For example, to switch to rescue mode immediately:
```bash
systemctl isolate rescue.target
```

### Example Commands
Here are some example commands to manage these targets:

- **Switch to graphical mode**:
  ```bash
  systemctl isolate graphical.target
  ```

- **Set multi-user mode as the default**:
  ```bash
  systemctl set-default multi-user.target
  ```

- **Reboot the system**:
  ```bash
  systemctl isolate reboot.target
  ```

- **Shut down and power off the system immediately**:
  ```bash
  systemctl isolate poweroff.target
  ```


---
### GRUB (GRand Unified Bootloader)

When dealing with a system booting through legacy BIOS mode and needing to make changes to the GRUB bootloader configuration before generating a new GRUB configuration file, the primary file you would edit is:

```plaintext
/etc/default/grub
```

This file contains the default settings used by the `grub-mkconfig` script to generate the `grub.cfg` file, which is located in `/boot/grub` or `/boot/grub2` depending on your distribution. The `/etc/default/grub` file includes various parameters such as:

- **GRUB_DEFAULT**: This sets the default menu entry that GRUB will select.
- **GRUB_TIMEOUT**: This defines the number of seconds before the default entry is automatically booted.
- **GRUB_CMDLINE_LINUX**: Used to set the Linux command line. Here, you can specify kernel parameters.
- **GRUB_CMDLINE_LINUX_DEFAULT**: Similar to the above but for default boot entries, often including quiet splash options for less verbose booting.

### Steps to Edit and Update GRUB Configuration

1. **Edit the Configuration**:
   Open the `/etc/default/grub` file with a text editor like nano or vim with root privileges.
   
   ```bash
   sudo vi /etc/default/grub
   ```

2. **Make Changes**:
   Adjust the settings according to your requirements. For instance, changing the timeout or default kernel boot option.

3. **Generate the New GRUB Configuration**:
   After saving your changes, you need to generate a new `grub.cfg` file using the following command:

   ```bash
   sudo update-grub
   ```

   Or, on some distributions like Fedora or CentOS, you might need to use:

   ```bash
   sudo grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

This process will ensure that any changes you make to the GRUB settings are applied the next time the system boots. 
