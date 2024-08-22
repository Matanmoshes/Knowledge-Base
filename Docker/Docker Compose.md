# Docker Compose Overview

Docker Compose is a tool that simplifies the process of defining and running multi-container Docker applications. With Docker Compose, you can define a multi-container application in a single YAML file, which makes it easier to manage and orchestrate services.

### Key Concepts of Docker Compose

- **Services**: The containers that are part of your application. Each service runs one image and defines the behavior of the container (e.g., which ports to expose, volumes to mount).
- **Networks**: Networks allow you to define how containers communicate with each other.
- **Volumes**: Persistent data storage. Volumes are used to store data that should persist even if the container is deleted.

---
# Basic Structure of a `docker-compose.yml` File

A Docker Compose file (`docker-compose.yml`) is typically structured as follows:

```yaml
version: '3'
services:
  service_name:
    image: image_name:tag
    build: ./path_to_dockerfile
    ports:
      - "host_port:container_port"
    environment:
      - ENV_VAR=value
    volumes:
      - host_path:container_path
    networks:
      - network_name

networks:
  network_name:

volumes:
  volume_name:
```

### Key Sections Explained

- **version**: Specifies the version of the Docker Compose file format. Common versions are '2', '2.1', '3', and '3.8'.
- **services**: Defines the containers (services) that make up your application. Each service has its own configuration.
- **networks**: Defines custom networks that the services can use to communicate with each other.
- **volumes**: Defines shared volumes that persist data across container restarts.

---

# Example: A Simple Multi-Container Application

Let's create a simple application using Docker Compose. This application will consist of two services: a web server (`nginx`) and a database (`mysql`).

#### `docker-compose.yml` Example

```yaml
version: '3.8'

services:
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: example
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - app-network

  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    networks:
      - app-network
    depends_on:
      - db

networks:
  app-network:

volumes:
  db_data:
```

### Explanation of the Example

- **db Service**:
  - Uses the `mysql:5.7` image.
  - Sets an environment variable `MYSQL_ROOT_PASSWORD` to set the root password for MySQL.
  - Mounts a volume named `db_data` to `/var/lib/mysql` to persist the database data.
  - Connects to a custom network `app-network`.

- **web Service**:
  - Uses the `nginx:latest` image.
  - Maps port `8080` on the host to port `80` in the container, making the web server accessible at `http://localhost:8080`.
  - Mounts a local `html` directory to `/usr/share/nginx/html` inside the container, where Nginx serves static files.
  - Connects to the same custom network `app-network` as the database, allowing the web service to communicate with the `db` service.
  - The `depends_on` key ensures that the `db` service is started before the `web` service.

- **networks**:
  - Defines a custom network `app-network` to allow the `db` and `web` services to communicate with each other.

- **volumes**:
  - Defines a persistent volume `db_data` to store the database files.

---
# Running the Example

1. **Create a Directory Structure**:
   ```bash
   mkdir my_app
   cd my_app
   mkdir html
   echo "<h1>Hello, Docker Compose!</h1>" > html/index.html
   ```

2. **Create the `docker-compose.yml` File**:
   In the `my_app` directory, create a `docker-compose.yml` file with the content provided above.

3. **Run Docker Compose**:
   In the `my_app` directory, run:
   ```bash
   docker-compose up -d
   ```

   This command will build and start the containers in the background.

4. **Access the Application**:
   Open a browser and go to `http://localhost:8080`. You should see the message "Hello, Docker Compose!".

5. **Stopping and Removing Containers**:
   To stop the containers, run:
   ```bash
   docker-compose down
   ```

   This command stops and removes the containers, networks, and volumes defined in the `docker-compose.yml` file.

---

## Summary

- **Docker Compose** simplifies managing multi-container Docker applications by defining them in a single YAML file.
- You can define services, networks, and volumes in the `docker-compose.yml` file.
- Compose allows you to easily start, stop, and manage the lifecycle of your application.

