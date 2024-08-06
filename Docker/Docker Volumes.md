### Docker Volumes Explained

#### What are Docker Volumes?
Docker volumes are a mechanism for persisting data generated or used by Docker containers. They enable data to be stored outside the container's filesystem, which is beneficial for various reasons:
- **Persistence**: Data remains intact even if the container is stopped, removed, or recreated.
- **Sharing**: Volumes can be shared among multiple containers.
- **Isolation**: Volumes help isolate the data from the container environment, providing a more flexible and manageable way to handle data.

#### Types of Volumes
1. **Named Volumes**: Managed by Docker and stored in a Docker-specific location on the host filesystem.
2. **Bind Mounts**: Map a directory on the host machine to a directory in the container.

### Using Volumes

#### Example 1: Volume Mapping with `-v`
To map a host directory to a directory in the container, you use the `-v` flag with the `docker run` command.

#### Command to Map Host Directory to Container Directory
```bash
docker run -v /mnt/:/var/www <image_name>
```
- **`-v /mnt/:/var/www`**: This flag maps the `/mnt/` directory on the Docker host to the `/var/www` directory inside the container.
  - **`/mnt/`**: Host directory path.
  - **`/var/www`**: Container directory path.

#### Explanation
- **`/mnt/`**: This is the directory on the host machine where the data will be stored.
- **`/var/www`**: This is the directory inside the container where the host directory will be mounted. Any files created or modified in this directory will be reflected in the `/mnt/` directory on the host and vice versa.

### Practical Example
Let's say you want to run an Apache web server container and map your host's `/mnt/` directory to the container's `/var/www` directory, which is the default document root for Apache.

#### Step-by-Step Command
1. Pull the Apache image from Docker Hub (if not already done):
    ```bash
    docker pull httpd:latest
    ```

2. Run the Apache container with the volume mapping:
    ```bash
docker run --name matan-nginx -p 8080:80 -v ~/Documents/DevOps/docker-practice:/usr/share/nginx/html:ro -d nginx
    ```
    - **`-d`**: Run the container in detached mode.
    - **`-p 8080:80`**: Map port 8080 on the host to port 80 in the container.
    - **`-v ~/Documents/DevOps/docker-practice:/usr/share/nginx/html`**: Map the host's `/usr/` directory to the container's `/share/nginx/html` directory.
    - **`--name matan-nginx`**: Name the container `matan-nginx`.
    - **`nginx:latest`**: Use the latest Nginx image.

### Benefits of Using Volumes
1. **Data Persistence**: Ensures data is not lost when containers are removed or recreated.
2. **Separation of Concerns**: Keeps data separate from the container's lifecycle, allowing for easier management and backups.
3. **Efficiency**: Volumes are designed to be efficient and provide better performance than bind mounts in many cases.


#### Running mysql image with volume mapping:

```Shell
docker run --name mysql-matan-db -v /Users/matanmoshe/Documents/DevOps/db-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 -d mysql
```