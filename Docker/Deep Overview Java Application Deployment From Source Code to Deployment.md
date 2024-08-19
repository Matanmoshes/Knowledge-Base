
# Guide: Building, Running, and Pushing a Java Application with Docker

This guide will walk you through the entire process of building a Docker image for a simple Java application using Maven, running it in a Docker container, and pushing the image to Docker Hub.

To run the simple Java application (`HelloWorld.java` and `Greeter.java`) in a Docker container, you need to consider two main stages:

1. **Build Stage:** Compile the Java code and package it as a JAR file using Maven make the source code to a binary.
2. **Runtime Stage:** Deploy the JAR file to a Tomcat server to run the application.

---
## Step 1: Prepare Your Java Application

Ensure your project directory has the following structure:

```
├── Dockerfile
├── pom.xml
└── src
    └── main
        └── java
            └── hello
                ├── Greeter.java
                └── HelloWorld.java
```

- **`Dockerfile`**: The Dockerfile used to build the Docker image.
- **`pom.xml`**: The Maven configuration file.
- **`src/main/java/hello/`**: The directory containing your Java source code.
---
## Step 2: Create the Dockerfile

The Dockerfile defines the steps required to build and run your application in a Docker container.

```dockerfile
# Stage 1: Build the application with Maven
FROM maven:3.8.6-openjdk-11 AS build
WORKDIR /app

# Copy the pom.xml file and download dependencies
COPY pom.xml ./
RUN mvn dependency:go-offline

# Copy the source code and compile, test, and package it into a JAR file
COPY src ./src
RUN mvn compile
RUN mvn test
RUN mvn package

# List the contents of the target directory (for debugging)
RUN ls -l /app/target

# Stage 2: Deploy the application to Tomcat
FROM tomcat:10.1.28-jdk11-temurin AS runtime
WORKDIR /usr/local/tomcat/webapps/

# Copy the JAR file from the build stage to the Tomcat webapps directory
COPY --from=build /app/target/*.jar ./ROOT.war

# Expose the Tomcat port
EXPOSE 8080

# Start Tomcat server
CMD ["catalina.sh", "run"]
```

---
## Step 3: Build the Docker Image

Use the following command to build your Docker image:

```bash
docker build . -t java-app-hellow-world --no-cache --progress=plain
```

- **`--no-cache`**: Ensures a fresh build by not using any cached layers.
- **`--progress=plain`**: Provides detailed build output.

---
## Step 4: Run the Docker Container

Once the Docker image is built, you can run it using the following command:

```bash
docker run -p 8080:8080 java-app-hellow-world
```

- **`-p 8080:8080`**: Maps port 8080 of your local machine to port 8080 in the container, making the application accessible at `http://localhost:8080`.

---
## Step 5: Push the Docker Image to Docker Hub

To push the Docker image to Docker Hub, follow these steps:

#### 1. **Login to Docker Hub:**

```bash
docker login
```

- Enter your Docker Hub username and password when prompted.

#### 2. **Tag the Docker Image:**

You need to tag your Docker image with your Docker Hub repository name before pushing it.

```bash
docker tag java-app-hellow-world <your-dockerhub-username>/java-app-hellow-world:latest
```

Replace `<your-dockerhub-username>` with your actual Docker Hub username.

#### 3. **Push the Docker Image:**

```bash
docker push <your-dockerhub-username>/java-app-hellow-world:latest
```

This command uploads your image to Docker Hub, making it accessible for others to pull and run.

---
## Step 6: Pull and Run the Image from Docker Hub

To verify that the image was pushed successfully, you can pull it from Docker Hub and run it:

```bash
docker pull <your-dockerhub-username>/java-app-hellow-world:latest
docker run -p 8080:8080 <your-dockerhub-username>/java-app-hellow-world:latest
```

---
### Summary

This guide covers the entire process from building a Docker image for a Java application to running it locally and pushing it to Docker Hub. Below are the main steps:

1. **Prepare your Java application and Dockerfile.**
2. **Build the Docker image:**
   ```bash
   docker build . -t java-app-hellow-world --no-cache --progress=plain
   ```
3. **Run the Docker container:**
   ```bash
   docker run -p 8080:8080 java-app-hellow-world
   ```
4. **Push the image to Docker Hub:**
   ```bash
   docker tag java-app-hellow-world <your-dockerhub-username>/java-app-hellow-world:latest
   docker push <your-dockerhub-username>/java-app-hellow-world:latest
   ```
5. **Pull and run the image from Docker Hub:**
   ```bash
   docker pull <your-dockerhub-username>/java-app-hellow-world:latest
   docker run -p 8080:8080 <your-dockerhub-username>/java-app-hellow-world:latest
   ```



---


# Understanding Java Application Deployment: From Source Code to Containerized Deployment with Docker

When you develop a Java application, the process of deploying it involves several critical steps: compiling the source code, packaging it into a JAR file, and then deploying this JAR file to a server (like Tomcat) for execution. This guide walks you through these steps using a Dockerfile that automates the build and deployment process. Additionally, we’ll explore how the `catalina.sh` script plays a role in running the Tomcat server.

---
## 1. **Understanding the Java Source Code**

Your Java application consists of two main classes:

- **HelloWorld.java**: This is the entry point of the application, containing the `main` method, which is the starting point for any Java application.

```java
package hello;

import org.joda.time.LocalTime;

public class HelloWorld {
    public static void main(String[] args) {
        LocalTime currentTime = new LocalTime();
        System.out.println("The current local time is: " + currentTime);

        Greeter greeter = new Greeter();
        System.out.println(greeter.sayHello());
    }
}
```

- **Greeter.java**: This class defines a simple method `sayHello`, which returns a greeting message.

```java
package hello;

public class Greeter {
    public String sayHello() {
        return "DevOps Project for AchiStar Technologies";
    }
}
```

---
## 2. **Compiling Java Source Code to Bytecode**

Java source code (`.java` files) needs to be compiled into bytecode (`.class` files) that the Java Virtual Machine (JVM) can execute. This compilation is done using the Java Compiler (`javac`).

For instance:

- `HelloWorld.java` -> `HelloWorld.class`
- `Greeter.java` -> `Greeter.class`

---
## 3. **Packaging the Compiled Classes into a JAR File**

Once the `.class` files are generated, they are typically packaged into a JAR (Java ARchive) file for distribution and deployment. A JAR file is essentially a compressed file containing all the `.class` files and other resources (like configuration files) needed to run the application.

Maven, a build automation tool for Java, handles this process. When you run the Maven `package` command, it does the following:

- **Compiles the Source Code**: Converts `.java` files into `.class` files.
- **Packages the Bytecode**: Bundles the compiled `.class` files and other resources into a JAR file.

The output is typically a `your-app-name.jar` file located in the `target` directory.

---

## 4. **Deploying the JAR File to a Web Server (Tomcat)**

Once you have the JAR file, it needs to be deployed to a web server like Tomcat to make the application accessible. Tomcat is a web server that runs Java Servlets and JSPs, but it can also serve regular Java applications packaged as WAR files (Web ARchive) or even as simple JAR files.

---
## 5. **The Dockerfile Explained: Integrating Maven and Tomcat**

Let's dive into the Dockerfile that automates the build and deployment process:

```dockerfile
# Stage 1: Build the application with Maven
FROM maven:3.8.6-openjdk-11 AS build
WORKDIR /app

# Copy the pom.xml file and download dependencies
COPY pom.xml ./
RUN mvn dependency:go-offline

# Copy the source code and compile, test, and package it into a JAR file
COPY src ./src
RUN mvn compile
RUN mvn test
RUN mvn package

# List the contents of the target directory (for debugging)
RUN ls -l /app/target
```

- **FROM maven:3.8.6-openjdk-11 AS build**: This uses a Docker image containing Maven and OpenJDK 11. Maven compiles and packages the Java application, while OpenJDK provides the Java Development Kit (JDK).

- **WORKDIR /app**: Sets the working directory inside the Docker container to `/app`.

- **COPY pom.xml ./**: Copies the Maven project configuration file (`pom.xml`) into the container. This file defines the project structure, dependencies, and build instructions.

- **RUN mvn dependency:go-offline**: Downloads all dependencies required by the project to ensure Maven has all the necessary libraries and tools to compile the code.

- **COPY src ./src**: Copies the source code (everything under the `src` directory) into the container.

- **RUN mvn compile && mvn test && mvn package**: These commands compile the Java source code, run tests, and package it into a JAR file. The resulting JAR file is placed in the `target` directory.

- **RUN ls -l /app/target**: Lists the contents of the `target` directory to confirm that the JAR file is generated correctly.

Next, the Dockerfile defines the runtime environment:

```dockerfile
# Stage 2: Deploy the application to Tomcat
FROM tomcat:10.1.28-jdk11-temurin AS runtime
WORKDIR /usr/local/tomcat/webapps/

# Copy the JAR file from the build stage to the Tomcat webapps directory
COPY --from=build /app/target/*.jar ./ROOT.war

# Expose the Tomcat port
EXPOSE 8080

# Start Tomcat server
CMD ["catalina.sh", "run"]
```

- **FROM tomcat:10.1.28-jdk11-temurin AS runtime**: This uses a Docker image that contains the Tomcat web server and JDK 11.

- **WORKDIR /usr/local/tomcat/webapps/**: Sets the working directory to Tomcat's `webapps` directory, where web applications are deployed.

- **COPY --from=build /app/target/*.jar ./ROOT.war**: Copies the JAR file generated in the build stage and renames it to `ROOT.war`. In Tomcat, a `ROOT.war` file is deployed as the default web application.

- **EXPOSE 8080**: Exposes port 8080, which is the default HTTP port for Tomcat.

- **CMD ["catalina.sh", "run"]**: This command starts the Tomcat server using the `catalina.sh` script.

---
## 6. **Understanding `catalina.sh`**

The `catalina.sh` script is a key component of Apache Tomcat. It is used to start, stop, and manage the Tomcat server. When you run `catalina.sh run`, it starts Tomcat in the foreground, meaning the server will continue to run and log messages directly to the console. This is useful in Docker environments where the container needs to stay alive and provide logs for monitoring.

- **Starting Tomcat (`run`)**: When you use `catalina.sh run`, it starts Tomcat in the foreground, making it suitable for Docker containers that need to keep running.

- **Other Commands**:
  - `start`: Starts Tomcat as a background process.
  - `stop`: Stops the Tomcat server.
  - `restart`: Restarts Tomcat.

---
## 7. **How the Java Application Runs**

Once the Docker container is started, Tomcat automatically deploys the `ROOT.war` file. Here’s how the process works:

1. **Tomcat Deployment**: Tomcat monitors its `webapps` directory for any WAR files. When it finds `ROOT.war`, it unpacks it and loads the application.
2. **Execution**: Tomcat then executes the Java application. When a user accesses the Tomcat server (e.g., via `http://localhost:8080`), Tomcat runs the `main` method in `HelloWorld.java`.
3. **Output**: The application prints the current local time and the greeting message to the console.

---
## Summary

- **Source Code**: Your `.java` files define the logic of your application.
- **Compilation**: Maven compiles these files into `.class` files (bytecode).
- **Packaging**: Maven then packages these `.class` files into a JAR file.
- **Deployment**: The Dockerfile builds this JAR file and deploys it to Tomcat, which serves the application.
- **Tomcat and `catalina.sh`**: The `catalina.sh` script starts the Tomcat server and keeps it running in the foreground within the Docker container.


