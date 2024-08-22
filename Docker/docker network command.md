# `docker network`:

Docker provides a suite of commands to manage and interact with networks. Here’s a list of commonly used Docker network commands and what they do:

### 1. **List Networks**
```bash
docker network ls
```
This command lists all the networks available in Docker. It shows the network name, ID, driver type, and scope.

### 2. **Inspect a Network**
```bash
docker network inspect <network_name>
```
This command provides detailed information about a specific network, including its configuration, connected containers, and more.

### 3. **Create a Network**
```bash
docker network create --driver <driver_name> <network_name>
```
This command creates a new network. You can specify the network driver (e.g., `bridge`, `overlay`, `host`, `none`, `macvlan`).

**Example:**
```bash
docker network create --driver bridge my_bridge_network
```

### 4. **Connect a Container to a Network**
```bash
docker network connect <network_name> <container_name>
```
This command connects an existing container to a specified network.

**Example:**
```bash
docker network connect my_bridge_network my_container
```

### 5. **Disconnect a Container from a Network**
```bash
docker network disconnect <network_name> <container_name>
```
This command disconnects a container from a specified network.

**Example:**
```bash
docker network disconnect my_bridge_network my_container
```

### 6. **Remove a Network**
```bash
docker network rm <network_name>
```
This command removes a specified network. The network must not have any active containers attached to it.

**Example:**
```bash
docker network rm my_bridge_network
```

### 7. **Prune Unused Networks**
```bash
docker network prune
```
This command removes all unused networks, which can help in cleaning up your Docker environment.

### 8. **Create a Network with Specific Subnet**
```bash
docker network create --subnet <subnet> <network_name>
```
This command allows you to create a network with a specific subnet.

**Example:**
```bash
docker network create --subnet=192.168.10.0/24 my_custom_network
```

### 9. **Run a Container with a Specific Network**
```bash
docker run --network <network_name> <image_name>
```
This command starts a container and immediately connects it to a specified network.

**Example:**
```bash
docker run --network my_bridge_network nginx
```

### 10. **View Network Stats (Docker 20.10 and later)**
```bash
docker network events
```
This command shows the events related to the Docker network, such as when a container connects to or disconnects from a network.

---
## Example Use Case


1. **Create a Network**:
   ```bash
   docker network create --driver bridge my_app_network
   ```

2. **Run a Database Container**:
   ```bash
   docker run -d --name db --network my_app_network -e MYSQL_ROOT_PASSWORD=my-secret-pw mysql:5.7
   ```

3. **Run a Web Server Container and Connect to the Same Network**:
   ```bash
   docker run -d --name web --network my_app_network nginx
   ```

4. **Inspect the Network**:
   ```bash
   docker network inspect my_app_network
   ```

5. **Disconnect the Web Server from the Network**:
   ```bash
   docker network disconnect my_app_network web
   ```

6. **Remove the Network**:
   ```bash
   docker network rm my_app_network
   ```

