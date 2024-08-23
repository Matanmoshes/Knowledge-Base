# Multi-Container Application with Python flask, Redis, and Nginx

In this advanced example, we'll build a multi-container application using Docker Compose that includes a Python Flask application, a Redis database for caching, and an Nginx server for reverse proxying and load balancing.

---
## Application Overview

- **Python Flask Service**: Serves a simple web application and uses Redis to cache data.
- **Redis Service**: Acts as a cache for the Flask application.
- **Nginx Service**: Serves as a reverse proxy, distributing requests to multiple Flask instances (for load balancing).

### Directory Structure

```bash
my_advanced_app/
│
├── docker-compose.yml
├── nginx/
│   └── nginx.conf
└── app/
    ├── requirements.txt
    ├── app.py
    └── Dockerfile
```

---

## 1. **Python Flask Application**

Create a simple Flask application in the `app` directory.

#### `app/requirements.txt`

```txt
Flask
Werkzeug
redis
```

#### `app/app.py`

```python
from flask import Flask

import redis

app = Flask(__name__)

cache = redis.Redis(host='redis', port=6379)


@app.route('/')

def hello():

visits = cache.get('visits')

if visits is None:

visits = 0

else:

visits = int(visits)  

visits += 1

cache.set('visits', visits)

return f'Hello from Flask! Number of visits: {visits}'

  

if __name__ == "__main__":

app.run(host='0.0.0.0', port=5000)
```

#### `app/Dockerfile`

```Dockerfile
# Dockerfile

FROM python:3.9-slim

WORKDIR /usr/src/app

COPY requirements.txt ./

RUN pip install --no-cache-dir -r requirements.txt

# Install curl

RUN apt-get update && apt-get install -y curl

COPY . .

EXPOSE 5000

CMD ["flask", "run", "--host=0.0.0.0"]
```


---

## 2. **Nginx Configuration**

Create an Nginx configuration file in the `nginx` directory.

#### `nginx/nginx.conf`

```nginx
events {}

http {
    upstream flask_servers {
        server app1:5000;
        server app2:5000;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://flask_servers;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

### 3. **Docker Compose File**

The Docker Compose file orchestrates the Flask, Redis, and Nginx services.

#### `docker-compose.yml`

```yaml
version: '3.8'

services:
  redis:
    image: redis:alpine
    networks:
      - app-network

  app1:
    build: ./app
    networks:
      - app-network
    healthcheck:
      test: ["CMD", "curl", "-f","http://localhost:5000"]
      interval: 30s
      timeout: 10s
      retries: 5
      

  app2:
    build: ./app
    networks:
      - app-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000"]
      interval: 30s
      timeout: 10s
      retries: 5 

  nginx:
    image: nginx:alpine
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
    ports:
      - "8080:80"
    depends_on:
      app1:
        condition: service_healthy
      app2:
        condition: service_healthy
    networks:
      - app-network

networks:
  app-network:
```


---

# Explanation of the Example

- **Redis Service**:
  - Uses the official `redis:alpine` image.
  - Provides a caching layer for the Flask application.
  - Connected to the custom network `app-network`.

- **App1 and App2 Services**:
  - Both services are identical instances of the Flask application, built using the Dockerfile in the `app` directory.
  - These services are connected to the same `app-network` and are load-balanced by Nginx.

- **Nginx Service**:
  - Uses the `nginx:alpine` image.
  - The custom `nginx.conf` is mounted to the container to configure Nginx to load-balance between `app1` and `app2`.
  - Exposes port `8080` on the host, mapping it to port `80` on the container.
  - Depends on both `app1` and `app2`, ensuring they start before Nginx.

- **Networks**:
  - A custom network `app-network` is defined, allowing all services to communicate with each other.

---

## Running the Application

1. **Create the Directory Structure**:
   Create the necessary directories and files as outlined in the directory structure.

2. **Build and Run the Containers**:
   In the root of your project directory, run:
   ```bash
   docker-compose up -d
   ```

3. **Access the Application**:
   Open your browser and go to `http://localhost:8080`. Nginx will distribute the requests between the two Flask instances, and you should see the visit count increment each time you refresh the page.

4. **Scaling the Application**:
   You can scale the application by increasing the number of Flask instances with a simple command:
   ```bash
   docker-compose up -d --scale app1=3
   ```
   This command will start a third instance of the Flask application, and Nginx will automatically begin load-balancing among all three instances.

5. **Stopping and Removing Containers**:
   To stop and remove all containers, networks, and volumes, run:
   ```bash
   docker-compose down
   ```


---

## Summary

- This advanced Docker Compose example demonstrates how to create a multi-container application with Python (Flask), Redis, and Nginx.
- The application is set up to be highly available and scalable, with Nginx handling load balancing across multiple instances of the Flask application.
- Docker Compose makes it easy to manage and scale this complex setup with simple commands.

