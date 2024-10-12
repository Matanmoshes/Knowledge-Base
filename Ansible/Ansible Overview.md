# Ansible Documentation Summary

Ansible is a powerful automation tool used for configuration management, application deployment, task automation, and orchestration. It is agentless, simple to use, and highly scalable, making it a preferred choice for IT professionals and DevOps engineers.

---

## Table of Contents

1. [Introduction to Ansible](#introduction-to-ansible)
2. [Ansible Architecture](#ansible-architecture)
3. [Installation](#installation)
4. [Configuration](#configuration)
   - [Ansible Configuration File](#ansible-configuration-file)
   - [Environment Variables](#environment-variables)
   - [Viewing Configuration](#viewing-configuration)
5. [Inventory Management](#inventory-management)
6. [Variables](#variables)
7. [Playbooks](#playbooks)
8. [Modules](#modules)
9. [Plugins](#plugins)
10. [Handlers](#handlers)
11. [Roles and Collections](#roles-and-collections)
12. [Templating with Jinja2](#templating-with-jinja2)
13. [Best Practices](#best-practices)
14. [Resources](#resources)

---

## Introduction to Ansible

- **What is Ansible?**
  - An open-source automation tool for configuration management, application deployment, and task automation.
  - Uses YAML for defining automation tasks in playbooks.
  - Agentless architecture communicates over SSH (for Linux) or WinRM (for Windows).

- **Why Use Ansible?**
  - **Simple and Human-Readable:** Uses YAML, which is easy to write and understand.
  - **Agentless:** No need to install agents on target machines.
  - **Powerful and Flexible:** Supports a wide range of modules for various tasks.
  - **Idempotent:** Ensures that operations result in the desired state without unintended changes.

---

## Ansible Architecture

- **Control Node:** The machine where Ansible is installed and from which commands are run.
- **Managed Nodes:** The target systems that Ansible manages.
- **Inventory:** A list of managed nodes.
- **Modules:** Reusable scripts that perform specific tasks.
- **Plugins:** Extend Ansible’s core functionality (e.g., connection, callback, filter).
- **Playbooks:** YAML files that define a series of tasks to be executed on managed nodes.

---

## Installation

### Prerequisites

- **Control Node:** Typically a Linux system.
- **Python:** Required on the control node and managed nodes.

### Installation Methods

1. **Using Package Managers:**
   - **RedHat/CentOS:**
     ```bash
     sudo yum install epel-release
     sudo yum install ansible
     ```
   - **Fedora:**
     ```bash
     sudo dnf install ansible
     ```
   - **Ubuntu/Debian:**
     ```bash
     sudo apt-get update
     sudo apt-get install ansible
     ```

2. **Using PIP:**
   ```bash
   sudo apt-get install python3-pip
   sudo pip3 install ansible
   sudo pip3 install --upgrade ansible
   ```

3. **From Source:**
   - Clone the repository and install manually (advanced users).

---

## Configuration

Ansible's behavior can be customized using configuration files and environment variables. Understanding these configurations ensures that Ansible operates as expected in different environments.

### Ansible Configuration File

- **Location:** `/etc/ansible/ansible.cfg` (default)
- **Sections:**
  - `[defaults]`: Default settings like inventory path, forks, timeout.
  - `[inventory]`: Inventory-related settings.
  - `[privilege_escalation]`: Sudo settings.
  - `[ssh_connection]`: SSH-specific settings.

### Example Configuration

```ini
[defaults]
inventory = /etc/ansible/hosts
forks = 10
timeout = 30
log_path = /var/log/ansible.log
gathering = implicit

[inventory]
enable_plugins = host_list, yaml, constructed

[privilege_escalation]
become = yes
become_method = sudo
become_user = root
```

### Environment Variables

Ansible allows the use of environment variables to override configuration settings dynamically. This is particularly useful for setting configurations specific to a particular run or environment without altering the main configuration file.

#### Key Environment Variables

1. **`ANSIBLE_CONFIG`**
   - **Purpose:** Specifies an alternate configuration file.
   - **Usage:**
     ```bash
     export ANSIBLE_CONFIG=/opt/ansible-web.cfg
     ```
     or
     ```bash
     $ANSIBLE_CONFIG=/opt/ansible-web.cfg ansible-playbook playbook.yml
     ```

2. **`ANSIBLE_GATHERING`**
   - **Purpose:** Controls the fact-gathering behavior.
   - **Values:**
     - `implicit` (default): Gather facts automatically before running tasks.
     - `explicit`: Only gather facts when explicitly specified in the playbook.
     - `smart`: Default behavior, similar to `implicit` but optimized.
     - `false`: Do not gather facts.
   - **Usage:**
     ```bash
     export ANSIBLE_GATHERING=explicit
     ```
     or
     ```bash
     ANSIBLE_GATHERING=explicit ansible-playbook playbook.yml
     ```

### Viewing Configuration

Ansible provides several commands to inspect and manage its configuration:

1. **Dump Configuration:**
   - **Command:**
     ```bash
     ansible-config dump | grep GATHERING
     ```
   - **Description:** Displays the current value of the `gathering` setting.

2. **View Current Configuration File:**
   - **Command:**
     ```bash
     ansible-config view
     ```
   - **Description:** Shows the contents of the current active configuration file.

3. **List All Configurations:**
   - **Command:**
     ```bash
     ansible-config list
     ```
   - **Description:** Lists all Ansible configuration settings and their current values.

4. **Show Current Settings:**
   - **Command:**
     ```bash
     ansible-config dump
     ```
   - **Description:** Outputs all current configuration settings, including defaults and overrides.

### Example Usage

```bash
# Set a custom configuration file
export ANSIBLE_CONFIG=/opt/ansible-web.cfg

# Set fact-gathering to explicit
export ANSIBLE_GATHERING=explicit

# Run a playbook with explicit fact-gathering
ansible-playbook playbook.yml

# View specific configuration
ansible-config dump | grep GATHERING

# View the active configuration file
ansible-config view

# List all configurations
ansible-config list

# Dump all current settings
ansible-config dump
```

**Sample Configuration Override:**

Given a custom configuration file at `/opt/ansible-web.cfg` with the following content:

```ini
[defaults]
gathering = explicit
```

Running the playbook with the custom configuration and explicit gathering:

```bash
$ANSIBLE_CONFIG=/opt/ansible-web.cfg ANSIBLE_GATHERING=explicit ansible-playbook playbook.yml
```

This ensures that Ansible uses the specified configuration file and adheres to the explicit fact-gathering behavior defined within it.

---

## Inventory Management

### What is Inventory?

- A list of managed nodes that Ansible interacts with.
- Can be static (INI or YAML files) or dynamic (scripts, cloud inventories).

### Inventory Formats

1. **INI Format:**

   ```ini
   [web]
   web1.company.com
   web2.company.com

   [db]
   db1.company.com
   db2.company.com

   [all:vars]
   ansible_user=admin
   ```

2. **YAML Format:**

   ```yaml
   all:
     children:
       webservers:
         hosts:
           web1.company.com:
             ansible_host: 192.168.1.10
           web2.company.com:
             ansible_host: 192.168.1.11
       dbservers:
         hosts:
           db1.company.com:
             ansible_host: 192.168.1.20
           db2.company.com:
             ansible_host: 192.168.1.21
     vars:
       ansible_user: admin
   ```

### Grouping and Parent-Child Relationships

- **Organize hosts into groups and subgroups for better management.**
- **Example:**

  ```ini
  [webservers:children]
  webservers_us
  webservers_eu

  [webservers_us]
  web1_us.company.com
  web2_us.company.com

  [webservers_eu]
  web1_eu.company.com
  web2_eu.company.com
  ```

---

## Variables

### Types of Variables

1. **String Variables:**
   ```yaml
   username: "admin"
   ```

2. **Number Variables:**
   ```yaml
   max_connections: 100
   ```

3. **Boolean Variables:**
   ```yaml
   debug_mode: true
   ```

4. **List Variables:**
   ```yaml
   packages:
     - nginx
     - postgresql
     - git
   ```

5. **Dictionary Variables:**
   ```yaml
   user:
     name: "admin"
     password: "secret"
   ```

### Variable Precedence

Ansible variables have a specific order of precedence. Higher precedence variables override lower ones.

1. **Extra Vars (`--extra-vars`)**
2. **Playbook Variables**
3. **Host Variables**
4. **Group Variables**
5. **Inventory Variables**
6. **Facts**
7. **Defaults**

### Using Variables

- **In Playbooks:**
  ```yaml
  - name: Add DNS server
    hosts: localhost
    tasks:
      - name: Update resolv.conf
        lineinfile:
          path: /etc/resolv.conf
          line: 'nameserver {{ dns_server }}'
    vars:
      dns_server: 10.1.250.10
  ```

- **Registering Variables:**
  ```yaml
  - name: Check /etc/hosts
    shell: cat /etc/hosts
    register: result

  - name: Display result
    debug:
      var: result.stdout
  ```

---

## Playbooks

### What is a Playbook?

- A YAML file containing one or more plays.
- Defines the desired state of systems by specifying tasks to be executed.

### Structure of a Playbook

```yaml
---
- name: Configure Web Server
  hosts: webservers
  become: yes
  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present

    - name: Start NGINX service
      service:
        name: nginx
        state: started
```

### Executing a Playbook

```bash
ansible-playbook playbook.yml
```

### Playbook Components

- **Play:** Maps a group of hosts to tasks.
- **Tasks:** Actions to be performed on hosts.
- **Modules:** Units of work (e.g., `apt`, `service`).
- **Variables:** Used to parameterize tasks.
- **Handlers:** Special tasks triggered by notifications.

---

## Modules

### Overview

- Modules are reusable, standalone scripts that perform specific tasks.
- Ansible includes hundreds of built-in modules covering various functionalities.

### Categories of Modules

- **System:** User, group, hostname management.
- **Commands:** Execute shell commands.
- **Files:** File and directory operations.
- **Database:** Manage databases like MySQL, PostgreSQL.
- **Cloud:** Interact with cloud providers (AWS, Azure, etc.).
- **Windows:** Manage Windows systems.

### Example Modules

1. **Command Module:**
   ```yaml
   - name: Execute date command
     command: date
   ```

2. **Copy Module:**
   ```yaml
   - name: Copy index.html
     copy:
       src: index.html
       dest: /var/www/nginx-default/index.html
   ```

3. **Service Module:**
   ```yaml
   - name: Start nginx service
     service:
       name: nginx
       state: started
   ```

---

## Plugins

### What are Plugins?

- Plugins extend Ansible’s core functionality.
- Types include Inventory, Callback, Filter, Connection, Action, etc.

### Common Plugin Types

1. **Inventory Plugins:**
   - Dynamically generate inventory from external sources (e.g., cloud providers).

2. **Filter Plugins:**
   - Transform data within templates and playbooks.
   - Example: `| upper` to convert strings to uppercase.

3. **Callback Plugins:**
   - Customize the output of Ansible runs.
   - Example: JSON output, custom logging.

4. **Connection Plugins:**
   - Define how Ansible connects to hosts (SSH, WinRM).

### Example Usage

- **Using a Custom Filter:**
  ```yaml
  msg: "The name is {{ my_name | upper }}"
  ```

---

## Handlers

### What are Handlers?

- Special tasks that run only when notified by other tasks.
- Typically used for actions like restarting services after configuration changes.

### Defining Handlers

```yaml
handlers:
  - name: Restart nginx
    service:
      name: nginx
      state: restarted
```

### Using Handlers

```yaml
tasks:
  - name: Update NGINX configuration
    copy:
      src: nginx.conf
      dest: /etc/nginx/nginx.conf
    notify: Restart nginx
```

---

## Roles and Collections

### Roles

- **Purpose:** Organize playbooks into reusable components.
- **Structure:**
  ```
  roles/
  └── role_name/
      ├── tasks/
      │   └── main.yml
      ├── handlers/
      ├── templates/
      ├── files/
      ├── vars/
      ├── defaults/
      ├── meta/
      └── README.md
  ```

- **Using Roles in Playbooks:**
  ```yaml
  - hosts: db-server
    roles:
      - mysql
  ```

### Collections

- **Purpose:** Package and distribute roles, modules, plugins, etc.
- **Installation:**
  ```bash
  ansible-galaxy collection install amazon.aws
  ```

- **Using Collections in Playbooks:**
  ```yaml
  ---
  - hosts: localhost
    collections:
      - amazon.aws
    tasks:
      - name: Create an S3 bucket
        aws_s3_bucket:
          name: my-bucket
          region: us-west-1
  ```

---

## Templating with Jinja2

### What is Jinja2?

- A templating engine used by Ansible for dynamic content generation.
- Allows embedding variables, control structures, and filters within templates.

### Using Templates

1. **Creating a Template File (`.j2`):**
   ```html
   <!DOCTYPE html>
   <html>
   <head>
     <title>{{ title }}</title>
   </head>
   <body>
     {{ message }}
   </body>
   </html>
   ```

2. **Applying the Template in a Playbook:**
   ```yaml
   - name: Deploy web page
     template:
       src: index.html.j2
       dest: /var/www/html/index.html
     vars:
       title: "Welcome Page"
       message: "Hello, World!"
   ```

### Jinja2 Filters

- **String Manipulation:**
  - `{{ my_name | upper }}` → Converts `my_name` to uppercase.
  - `{{ my_name | replace("old", "new") }}` → Replaces substrings.

- **List Operations:**
  - `{{ my_list | unique }}` → Removes duplicates.
  - `{{ my_list | sort }}` → Sorts the list.

- **Default Values:**
  - `{{ my_var | default("default_value") }}` → Uses a default if `my_var` is undefined.

### Control Structures

- **Loops:**
  ```jinja
  {% for user in users %}
    - name: Create user {{ user.name }}
      user:
        name: "{{ user.name }}"
        state: present
  {% endfor %}
  ```

- **Conditions:**
  ```jinja
  {% if user.admin %}
    - name: Grant sudo to {{ user.name }}
      user:
        name: "{{ user.name }}"
        sudo: "ALL=(ALL) NOPASSWD:ALL"
  {% endif %}
  ```

---

## Best Practices

1. **Use Version Control:**
   - Store playbooks, roles, and configurations in Git repositories.

2. **Organize Playbooks and Roles:**
   - Follow a consistent directory structure for scalability and maintainability.

3. **Leverage Idempotency:**
   - Ensure tasks are idempotent to prevent unintended changes.

4. **Use Variables and Templates:**
   - Parameterize configurations for flexibility across environments.

5. **Implement Handlers:**
   - Use handlers for actions that need to run only when changes occur.

6. **Test Playbooks:**
   - Verify playbook syntax and behavior using `--check`, `--diff`, and tools like `ansible-lint`.

7. **Secure Sensitive Data:**
   - Use Ansible Vault to encrypt sensitive information like passwords and keys.

---

## Resources

- **Official Documentation:** [Ansible Documentation](https://docs.ansible.com/)
- **Ansible Galaxy:** [Ansible Galaxy](https://galaxy.ansible.com/) – Repository for roles and collections.
- **Community Support:**
  - [Ansible GitHub](https://github.com/ansible/ansible)
  - [Ansible Forum](https://forum.ansible.com/)
- **Learning Courses:**
  - [Ansible for the Absolute Beginners](https://kode.wiki/3sufvat)

---

## Additional Configuration Details

To further enhance your Ansible setup, consider utilizing environment variables and advanced configuration viewing commands. These tools provide greater flexibility and control over how Ansible operates in different environments.

### Setting Environment Variables

1. **`ANSIBLE_CONFIG`:**
   - **Purpose:** Specifies an alternative Ansible configuration file.
   - **Usage:**
     ```bash
     export ANSIBLE_CONFIG=/opt/ansible-web.cfg
     ```
     This command sets the `ANSIBLE_CONFIG` environment variable to use `/opt/ansible-web.cfg` instead of the default `/etc/ansible/ansible.cfg`.

2. **`ANSIBLE_GATHERING`:**
   - **Purpose:** Controls the fact-gathering behavior.
   - **Values:**
     - `implicit`: Gather facts automatically (default behavior).
     - `explicit`: Only gather facts when explicitly specified in the playbook.
     - `false`: Do not gather facts.
   - **Usage:**
     ```bash
     export ANSIBLE_GATHERING=explicit
     ```
     or for a single playbook run:
     ```bash
     ANSIBLE_GATHERING=explicit ansible-playbook playbook.yml
     ```

### Viewing and Managing Configuration

Ansible provides several commands to inspect and manage its configuration effectively:

1. **Dump Specific Configuration Settings:**
   - **Command:**
     ```bash
     ansible-config dump | grep GATHERING
     ```
   - **Description:** Filters and displays the current setting for fact gathering.

2. **View the Active Configuration File:**
   - **Command:**
     ```bash
     ansible-config view
     ```
   - **Description:** Outputs the contents of the currently active Ansible configuration file.

3. **List All Configuration Options:**
   - **Command:**
     ```bash
     ansible-config list
     ```
   - **Description:** Lists all available Ansible configuration settings along with their current values.

4. **Dump All Current Configuration Settings:**
   - **Command:**
     ```bash
     ansible-config dump
     ```
   - **Description:** Shows all current Ansible configuration settings, including defaults and any overrides from environment variables or configuration files.

### Example Scenario

Suppose you have a custom configuration file located at `/opt/ansible-web.cfg` with the following content:

```ini
[defaults]
gathering = explicit
```

To use this configuration file and set fact gathering to `explicit` during a playbook run, execute:

```bash
ANSIBLE_CONFIG=/opt/ansible-web.cfg ANSIBLE_GATHERING=explicit ansible-playbook playbook.yml
```

This command ensures that:
- Ansible uses the `/opt/ansible-web.cfg` configuration file.
- Fact gathering is set to `explicit`, meaning Ansible will only gather facts when specified in the playbook.

### Viewing Configuration in Detail

To inspect the current configuration settings related to fact gathering:

```bash
export ANSIBLE_GATHERING=explicit
ansible-config dump | grep GATHERING
```

To view the entire active configuration file:

```bash
ansible-config view
```

To list all configuration options and their values:

```bash
ansible-config list
```

To dump all current settings for review:

```bash
ansible-config dump
```

These commands are invaluable for debugging and ensuring that Ansible operates under the desired configuration parameters.
