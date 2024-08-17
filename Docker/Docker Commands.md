### Essential Docker Commands Guide

#### Docker Installation and Version
- **Install Docker (on Ubuntu):**
  ```bash
  sudo apt-get update
  sudo apt-get install docker-ce docker-ce-cli containerd.io
  ```
- **Check Docker version:**
  ```bash
  docker --version
  ```

#### Working with Docker Images
- **Pull an image from Docker Hub:**
  ```bash
  docker pull <image_name>
  ```
  Example:
  ```bash
  docker pull nginx
  ```

- **List all images:**
  ```bash
  docker images
  ```

- **Remove an image:**
  ```bash
  docker rmi <image_id>
  ```

- **Build an image from a Dockerfile:**
  ```bash
  docker build -t <image_name>:<tag> <path_to_dockerfile>
  ```
  Example:
  ```bash
  docker build -t myapp:latest .
  ```

#### Working with Docker Containers
- **Run a container:**
  ```bash
  docker run --name webserver-nginx -d -p 8888:80 nginx:latest
  ```
  - `--name webserver-nginx`: Assigns a name to the container.
  - `-d`: Runs the container in detached mode (background).
  - `-p 8888:80`: Maps port 8888 on the host to port 80 on the container.
  - `nginx:latest`: Uses the latest nginx image.

- **List all running containers:**
  ```bash
  docker ps
  ```

- **List all containers (including stopped):**
  ```bash
  docker ps -a
  ```

- **Stop a container:**
  ```bash
  docker stop <container_id>
  ```

- **Start a container:**
  ```bash
  docker start <container_id>
  ```

- **Remove a container:**
  ```bash
  docker rm <container_id>
  ```

#### Docker Networks
- **List all networks:**
  ```bash
  docker network ls
  ```

- **Create a new network:**
  ```bash
  docker network create <network_name>
  ```

- **Connect a container to a network:**
  ```bash
  docker network connect <network_name> <container_id>
  ```

- **Disconnect a container from a network:**
  ```bash
  docker network disconnect <network_name> <container_id>
  ```

#### Docker Volumes
- **List all volumes:**
  ```bash
  docker volume ls
  ```

- **Create a new volume:**
  ```bash
  docker volume create <volume_name>
  ```

- **Inspect a volume:**
  ```bash
  docker volume inspect <volume_name>
  ```

- **Manipulate Inspect command to view specific field:**
```Shell
docker inspect --format='{{json .Mounts}}' <container_name>
```

- **Remove a volume:**
  ```bash
  docker volume rm <volume_name>
  ```

- **Create mysql container with volume mapping:**
```Shell
docker run --name mysql-matan-db -v /Users/matanmoshe/Documents/DevOps/db-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 -d mysql
```

#### Docker System Management
- **Show Docker system information:**
  ```bash
  docker info
  ```

- **Clean up unused data:**
  ```bash
  docker system prune
  ```


---

# Connect to mysql container

### Step-by-Step Guide

#### Start a `mysql` server instance:

```Shell
docker run --name mysql-db -d -e MYSQL_ROOT_PASSWORD=1234 -d mysql
```


#### 1. Connect to your container and open a bash shell
First, make sure your MySQL container is running. If not, start it with the appropriate command.

```bash
docker exec -it mysql-db bash
```
- `docker exec`: Executes a command in a running container.
- `-it`: Allows you to interact with the container via the terminal.
- `mysql-db`: The name of your MySQL container.
- `bash`: The command to open a bash shell.

#### 2. Log in to MySQL
Once inside the container, log in to MySQL using the root user (or any user you have set up).

```bash
mysql -u root -p
```
- `mysql`: The MySQL command-line client.
- `-u root`: Specifies the MySQL user (root in this case).
- `-p`: Prompts for the password.

When prompted, enter the password `1234`.

#### 3. Show databases
After logging in, show the existing databases.

```sql
SHOW DATABASES;
```

#### 4. Create a new database named `matan`
Create the new database with the following command:

```sql
CREATE DATABASE matan;
```

