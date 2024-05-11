Here's an updated guide, including how to inspect the configuration of a service using `systemctl cat`.

### Guide to Manage Startup Process and Services with `systemctl`

1. **Listing and Controlling Services with `systemd`**:
   - `systemctl` is the primary tool for managing services in systems using `systemd`.

   **Commands:**
   ```bash
   # List all unit files and their status
   sudo systemctl list-unit-files --type=service
   
   # Check the status of a specific service (e.g., sshd)
   sudo systemctl status ssh.service
   
   # Start the sshd service manually
   sudo systemctl start ssh.service
   
   # Stop the sshd service
   sudo systemctl stop ssh.service
   
   # Restart the sshd service
   sudo systemctl restart ssh.service
   
   # Enable the sshd service to start at boot
   sudo systemctl enable ssh.service
   
   # Disable the sshd service from starting at boot
   sudo systemctl disable ssh.service
   
   # List all currently active services
   sudo systemctl list-units --type=service --state=active
   ```

2. **Viewing Service Unit File Configuration**:
   - Inspecting a service's unit file can provide insight into its configuration.
   - This is useful if you want to modify or understand the service’s behavior.

   **Command:**
   ```bash
   # View the configuration of the ssh service
   sudo systemctl cat ssh.service
   ```

   - The output will display the service’s unit file, including `[Unit]`, `[Service]`, and `[Install]` sections.
   - Modifying the unit file requires creating a drop-in file or editing the primary file (not recommended unless fully understood).

3. **Customizing Service Unit Files**:
   - If modifications are needed, create a drop-in configuration directory under `/etc/systemd/system/<service_name>.service.d/`.

   **Example:**
   ```bash
   # Create a custom override directory for ssh.service
   sudo mkdir -p /etc/systemd/system/ssh.service.d/
   
   # Create a custom override file
   sudo nano /etc/systemd/system/ssh.service.d/override.conf
   
   # Add or modify specific directives as needed, e.g.:
   [Service]
   Restart=always
   
   # Save and reload systemd
   sudo systemctl daemon-reload
   ```

4. **Legacy Service Management Tools**:
   - Some older distributions still rely on legacy tools.

   **Red Hat-Based (chkconfig):**
   ```bash
   # List services and their runlevel configurations
   sudo chkconfig --list
   
   # Enable a service to start at boot
   sudo chkconfig httpd on
   
   # Disable a service from starting at boot
   sudo chkconfig httpd off
   ```

   **Debian-Based (update-rc.d):**
   ```bash
   # Enable a service to start at boot (e.g., apache2)
   sudo update-rc.d apache2 defaults
   
   # Disable a service from starting at boot
   sudo update-rc.d apache2 disable
   ```

5. **Startup Applications in Desktop Environments**:
   - Graphical environments have tools to manage startup applications at a user level.

   **GNOME:**
   ```bash
   # Open the GNOME Startup Applications Preferences GUI
   gnome-session-properties
   ```
   - This provides a graphical interface for adding or removing user-specific startup applications.

### Final Tips:
- Reviewing service unit files periodically ensures services run with optimal settings.
- Keep unnecessary services off for security and efficiency reasons.
- Utilize `journalctl` for service logs to help with troubleshooting.