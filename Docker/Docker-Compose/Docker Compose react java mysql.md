
# **Full-Stack Application with React, Spring Boot, and MySQL**

This project demonstrates how to build a full-stack application using React for the frontend, Spring Boot for the backend, and MySQL as the database. The application is containerized using Docker and orchestrated using Docker Compose.

## **Project Structure**

```plaintext
.
├── README.md
├── backend
│   ├── Dockerfile
│   ├── pom.xml
│   └── src
│       └── main
│           └── java
│               └── com
│                   └── company
│                       └── project
│                           └── Application.java
├── db
│   └── password.txt
├── frontend
│   ├── Dockerfile
│   ├── package-lock.json
│   ├── package.json
│   ├── public
│   │   └── index.html
│   └── src
│       ├── App.tsx
│       └── index.tsx
└── docker-compose.yml
```

### **Folder & File Descriptions**

1. **backend/**:
   - Contains the Spring Boot application.
   - **Dockerfile**: Builds the backend Spring Boot application.
   - **pom.xml**: Maven build file that defines dependencies and plugins.
   - **src/**: Contains the Java source code for the backend application.

2. **frontend/**:
   - Contains the React application.
   - **Dockerfile**: Builds the frontend React application.
   - **package.json**: Contains npm scripts and dependencies for the frontend.
   - **src/**: Contains the React source code.

3. **db/**:
   - Contains the MySQL database password in a file named `password.txt`.

4. **docker-compose.yml**:
   - Orchestrates the multi-container application using Docker Compose.

---
## **Backend Setup (Spring Boot)**

### **Dockerfile**

```Dockerfile
# syntax=docker/dockerfile:1.4

FROM --platform=$BUILDPLATFORM maven:3.8.5-eclipse-temurin-17 AS builder
WORKDIR /workdir/server
COPY pom.xml /workdir/server/pom.xml
RUN mvn dependency:go-offline

COPY src /workdir/server/src
RUN mvn install

FROM builder as prepare-production
RUN mkdir -p target/dependency
WORKDIR /workdir/server/target/dependency
RUN jar -xf ../*.jar

FROM eclipse-temurin:17-jre-focal

EXPOSE 8080
VOLUME /tmp
ARG DEPENDENCY=/workdir/server/target/dependency
COPY --from=prepare-production ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=prepare-production ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=prepare-production ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java","-cp","app:app/lib/*","com.company.project.Application"]
```

### **Explanation**:
- **Multi-stage build**: 
  - First, the application is built using Maven (`builder` stage).
  - The second stage (`prepare-production`) extracts the necessary dependencies.
  - The final stage runs the Spring Boot application with a minimal runtime environment (`eclipse-temurin:17-jre-focal`).


### **pom.xml**
- Defines project dependencies (like Spring Boot) and plugins (like Maven) required to build the application.

### **Application.java**

```java
package com.company.project;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### **Explanation**:
- This is the entry point for the Spring Boot application. It initializes and runs the application.

---
## **Frontend Setup (React)**

### **Dockerfile**

```Dockerfile
FROM node:lts AS development

WORKDIR /code
COPY package.json /code/package.json
COPY package-lock.json /code/package-lock.json

RUN npm ci

COPY . /code

ENV CI=true
ENV PORT=3000

ENTRYPOINT [ "npm", "start" ]
```

### **Explanation**:
- This Dockerfile sets up a development environment for the React application using Node.js.
- The application is started using `npm start`, and it will be served on port 3000.

### **package.json**
- This file includes all the dependencies and scripts needed to run the React application.

### **App.tsx & index.tsx**
- These files are the core of the React application. They define the main components and entry points.

---

## **Database Setup (MySQL)**

### **password.txt**
- Stores the MySQL root password. This file is used as a secret in Docker Compose.

---

## **Docker Compose Setup**

### **docker-compose.yml**

```yaml
version: '3.8'

services:
  backend:
    build:
      context: ./backend
    container_name: matan-backend
    environment:
      - SPRING_DATASOURCE_URL=jdbc:mysql://db:3306/mydatabase
      - SPRING_DATASOURCE_USERNAME=root
      - SPRING_DATASOURCE_PASSWORD_FILE=/run/secrets/db_password
    ports:
      - "8080:8080"
    depends_on:
      - db

  db:
    image: mysql:latest
    container_name: matan-db
    environment:
      MYSQL_ROOT_PASSWORD_FILE: /run/secrets/db_password
      MYSQL_DATABASE: mydatabase
    secrets:
      - db_password
    ports:
      - "3306:3306"
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5
  
  frontend:
    build:
      context: ./frontend
    container_name: matan-frontend
    environment:
      - PORT=3000
    ports:
      - "3000:3000"
    depends_on:
      - backend

secrets:
  db_password:
    file: ./db/password.txt
```

### **Explanation**:
- **backend**: Defines the backend Spring Boot service.
- **db**: Defines the MySQL service. A health check is added to ensure MySQL is up and running before other services depend on it.
- **frontend**: Defines the frontend React service.
- **secrets**: Defines the secret file (`password.txt`) used for MySQL's root password.

---

## **Running the Application**

1. **Build and Start Services**:
   Run the following command to build and start all services:
   ```bash
   docker-compose up -d --build
   ```

2. **Check Running Containers**:
   Use the following command to ensure all containers are running:
   ```bash
   docker-compose ps
   ```

3. **Access the Application**:
   - **Frontend**: Open a browser and go to `http://localhost:3000`.
   - **Backend**: The backend should be accessible at `http://localhost:8080`.

4. **Interact with MySQL**:
   - Connect to the MySQL container:
     ```bash
     docker exec -it matan-db mysql -u root -p
     ```
   - Enter the password from `password.txt` when prompted.
   - View databases, tables, and query data using standard MySQL commands.

---
## **Learning Points**

- **Docker Multi-Stage Builds**: Efficiently builds and optimizes container images by separating build stages.
- **Docker Compose**: Orchestrates multi-container Docker applications, managing dependencies between services.
- **Environment Variables & Secrets**: Securely manages sensitive information like database credentials.
- **Health Checks**: Ensures the database is ready before dependent services start.
- **Spring Boot & React**: Demonstrates how to build a full-stack application with backend and frontend services.

