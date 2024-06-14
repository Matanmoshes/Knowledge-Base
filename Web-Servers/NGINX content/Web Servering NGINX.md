Nginx (pronounced "engine-x") is a high-performance HTTP server and reverse proxy, as well as an IMAP/POP3 proxy server. It is known for its stability, rich feature set, simple configuration, and low resource consumption. Below, I'll provide a detailed explanation of setting up a web server using Nginx.

### 1. **Installation of Nginx**

#### On Ubuntu/Debian:
```sh
sudo apt update
sudo apt install nginx
```

#### On CentOS/RHEL:
```sh
sudo yum install epel-release
sudo yum install nginx
```

### 2. **Starting and Enabling Nginx**

To start Nginx:
```sh
sudo systemctl start nginx
```

To enable Nginx to start on boot:
```sh
sudo systemctl enable nginx
```

### 3. **Basic Nginx Configuration**

Nginx's main configuration file is located at `/etc/nginx/nginx.conf`. By default, the main configuration file includes other configuration files found in the `/etc/nginx/conf.d/` and `/etc/nginx/sites-enabled/` directories.

#### Key Directives in `nginx.conf`:

- **worker_processes**: Defines the number of worker processes.
  ```nginx
  worker_processes auto;
  ```

- **events**: Configuration related to how Nginx handles connections.
  ```nginx
  events {
      worker_connections 1024;
  }
  ```

- **http**: This block includes settings for HTTP traffic.
  ```nginx
  http {
      include       mime.types;
      default_type  application/octet-stream;

      sendfile        on;
      keepalive_timeout  65;

      include /etc/nginx/conf.d/*.conf;
  }
  ```

### 4. **Serving Static Content**

To serve static content, you can create a simple configuration file in the `/etc/nginx/conf.d/` directory.

#### Example Configuration:
Create a file named `example.com.conf`:
```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        root /var/www/html;
        index index.html index.htm;
    }
}
```

In this example, Nginx will serve files from the `/var/www/html` directory when a request is made to `example.com`.

### 5. **Reverse Proxy Configuration**

Nginx is often used as a reverse proxy to forward client requests to another server.

#### Example Configuration:
```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 6. **Load Balancing**

Nginx can also distribute client requests to multiple servers.

#### Example Configuration:
```nginx
upstream backend {
    server backend1.example.com weight=5;
    server backend2.example.com;
    server backend3.example.com;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend;
    }
}
```

### 7. **Security Enhancements**

#### Basic Security Headers:
Add these headers in your server block to enhance security.
```nginx
add_header X-Frame-Options "SAMEORIGIN";
add_header X-Content-Type-Options "nosniff";
add_header X-XSS-Protection "1; mode=block";
```

#### Enabling SSL:
To secure your server with SSL, you'll need an SSL certificate.

```nginx
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/ssl/certs/your_cert.crt;
    ssl_certificate_key /etc/ssl/private/your_key.key;

    location / {
        root /var/www/html;
        index index.html index.htm;
    }
}

server {
    listen 80;
    server_name example.com;
    return 301 https://$server_name$request_uri;
}
```

### 8. **Logging**

Nginx logs requests and errors. The log configuration can be found in `nginx.conf` or within server blocks.

```nginx
log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';

access_log /var/log/nginx/access.log main;
error_log /var/log/nginx/error.log warn;
```

### 9. **Performance Tuning**

- **gzip Compression**: Enable gzip to compress responses.
  ```nginx
  http {
      gzip on;
      gzip_types text/plain application/json;
      gzip_min_length 1000;
  }
  ```

- **Caching**: Configure caching to improve performance.
  ```nginx
  http {
      proxy_cache_path /data/nginx/cache levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m use_temp_path=off;

      server {
          location / {
              proxy_cache my_cache;
              proxy_pass http://backend;
              proxy_cache_bypass $http_cache_control;
              proxy_no_cache $http_cache_control;
              proxy_cache_valid 200 302 10m;
              proxy_cache_valid 404 1m;
          }
      }
  }
  ```

### 10. **Managing Nginx**

- **Reloading Configuration**: When you make changes to the configuration files, reload Nginx to apply the changes without stopping the service.
  ```sh
  sudo systemctl reload nginx
  ```

- **Checking Configuration**: Before reloading, check the configuration for syntax errors.
  ```sh
  sudo nginx -t
  ```

### Conclusion

Nginx is a powerful and flexible web server that can handle a variety of tasks from serving static content to acting as a reverse proxy and load balancer. By understanding its configuration and capabilities, you can optimize your web server setup to achieve high performance, security, and reliability.