### **Guide for Running the Jenkins Pipeline**

This guide will walk you through the steps to set up and run the Jenkins pipeline that pulls code from a GitHub repository, builds a Docker image, and pushes it to Docker Hub. It also includes the troubleshooting steps that helped resolve permission issues with Docker.

---

### **Step 1: Prepare Jenkins and Docker Environment**

#### **1.1 Configure Docker Permissions**

On your build agent (`docker-slave`), you need to ensure that the user Jenkins is running under (e.g., `ubuntu`) has permission to access Docker. This is done by modifying the Docker socket's permissions.

1. **Set Docker Socket Permissions**:
   On the build agent, run the following command to allow the user `ubuntu` to interact with Docker:

   ```bash
   sudo chmod 666 /var/run/docker.sock
   ```

   This grants read and write access to the Docker socket, which allows Jenkins to build and manage Docker containers.

2. **Restart the Docker Service**:
   After changing the permissions, restart the Docker service:

   ```bash
   sudo systemctl restart docker
   ```

3. **Verify Docker Access**:
   Check if Docker commands are working properly by running:

   ```bash
   docker ps
   ```

   This should list running containers without any permission errors.

---

### **Step 2: Set Up Jenkins Pipeline**

#### **2.1 Create a New Pipeline Job**

1. **Go to Jenkins**: Open your Jenkins dashboard.
2. **Create New Item**: Click on **New Item**, select **Pipeline**, and give it a name (e.g., `docker-pipeline`).
3. **Configure the Pipeline**: In the pipeline configuration, scroll down to the **Pipeline** section, and select **Pipeline script**.

#### **2.2 Update the Groovy Pipeline Code**

Below is the updated Groovy code you will use for the Jenkins pipeline. This code includes steps for checking out code from GitHub, building a Docker image, and pushing it to Docker Hub.

```groovy
pipeline {
    agent {
        label 'docker-slave'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Use GitHub credentials to clone the repository
                checkout scmGit(
                    branches: [[name: '*/master']], 
                    userRemoteConfigs: [[
                        credentialsId: 'github-creds',  // GitHub credentials ID
                        url: 'https://github.com/lidorg-dev/python-flask-docker.git'
                    ]]
                )
            }
        }
        stage('Build Docker Image') {
            steps {
                // Build Docker image using Dockerfile in repository
                sh "docker build -t python-flask-docker:${env.BUILD_NUMBER} ."
            }
        }
        stage('Test Docker Image') {
            steps {
                echo "Testing Docker Image..."
            }
        }
        stage('Push Docker Image') {
            steps {
                // Use DockerHub credentials to log in and push the Docker image
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker tag python-flask-docker:${env.BUILD_NUMBER} matanm66/python-flask-docker:${env.BUILD_NUMBER}"
                    sh "docker push matanm66/python-flask-docker:${env.BUILD_NUMBER}"
                }
            }
        }
    }
}
```

#### **2.3 Set Credentials in Jenkins**

1. **GitHub Credentials**: Make sure you have GitHub credentials (`github-creds`) set up in Jenkins:
   - Go to **Manage Jenkins** -> **Manage Credentials**.
   - Add **Username with password** and use your GitHub username and personal access token.

2. **DockerHub Credentials**: Ensure that you have DockerHub credentials (`dockerhub-creds`) set up:
   - Add another **Username with password** credential, with your DockerHub username and password.

---

### **Step 3: Run the Pipeline**

#### **3.1 Execute the Pipeline**

1. After saving the pipeline script, go to your pipeline job.
2. Click on **Build Now** to start the job.
3. Monitor the pipeline’s progress in the **Console Output** section to ensure that the stages complete successfully.

---

### **Step 4: Pipeline Stages Breakdown**

1. **Checkout Code**:
   - The pipeline checks out the code from the specified GitHub repository using the `github-creds` credential.

2. **Build Docker Image**:
   - The Docker image is built using the `Dockerfile` in the repository, and the image is tagged with the build number (e.g., `python-flask-docker:6`).

3. **Test Docker Image**:
   - A basic test is performed by printing "Testing Docker Image..." (You can extend this to include more detailed tests).

4. **Push Docker Image**:
   - The pipeline logs into DockerHub using the `dockerhub-creds` and pushes the image to the `matanm66` DockerHub repository.

---

### **Step 5: Verify the Docker Image on DockerHub**

Once the pipeline completes successfully, go to Docker Hub and verify that the image has been pushed to the `matanm66/python-flask-docker` repository.

You can now pull and run this Docker image on any machine using the following command:

```bash
docker pull matanm66/python-flask-docker:6
```

---

# **Grovy code breakdown:**

Here's a detailed breakdown of the Groovy pipeline code for Jenkins, which builds, tests, and pushes a Docker image to Docker Hub:

---

```groovy
pipeline {
    agent {
        label 'docker-slave'
    }
```

### Explanation:
- **pipeline**: The top-level block that defines the entire pipeline.
- **agent**: Defines where the pipeline or stage will run. The label `docker-slave` means that this pipeline will run on a Jenkins agent (node) labeled `docker-slave`. You must ensure that you have a Jenkins agent with Docker installed and labeled accordingly.

---

```groovy
    stages {
        stage('Checkout Code') {
            steps {
                // Use GitHub credentials to clone the repository
                checkout scmGit(
                    branches: [[name: '*/master']], 
                    userRemoteConfigs: [[
                        credentialsId: 'github-creds',  // GitHub credentials ID
                        url: 'https://github.com/lidorg-dev/python-flask-docker.git'
                    ]]
                )
            }
        }
```

### Explanation:
- **stages**: A collection of stages that make up the pipeline. Each stage contains steps to be executed.
- **stage('Checkout Code')**: This is the first stage in the pipeline, named 'Checkout Code'.
- **steps**: This block contains the actual actions (steps) Jenkins will perform.
- **checkout scmGit**: Jenkins checks out the source code from the specified GitHub repository. It uses the `github-creds` credential, which should be set up in Jenkins as a **GitHub personal access token** or username/password. The `url` specifies the GitHub repository, and `branches` tells Jenkins to clone the `master` branch.

---

```groovy
        stage('Build Docker Image') {
            steps {
                // Build Docker image using Dockerfile in repository
                sh "docker build -t python-flask-docker:${env.BUILD_NUMBER} ."
            }
        }
```

### Explanation:
- **stage('Build Docker Image')**: This stage builds the Docker image.
- **sh**: Runs shell commands inside the pipeline. 
- **docker build -t python-flask-docker:${env.BUILD_NUMBER} .**: This command builds the Docker image. 
    - **-t**: Tags the image with `python-flask-docker:${env.BUILD_NUMBER}`, where `${env.BUILD_NUMBER}` is a Jenkins environment variable that contains the current build number, allowing each image to have a unique tag.
    - The `.` specifies the current directory, where the Dockerfile and other files needed to build the image are located.

---

```groovy
        stage('Test Docker Image') {
            steps {
                echo "Testing Docker Image..."
            }
        }
```

### Explanation:
- **stage('Test Docker Image')**: This is a placeholder stage for testing the Docker image. It can be extended to include actual tests (such as running the image or running unit tests).
- **echo "Testing Docker Image..."**: Prints the message to the Jenkins console output. This is a placeholder for actual test steps.

---

```groovy
        stage('Push Docker Image') {
            steps {
                // Use DockerHub credentials to log in and push the Docker image
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker tag python-flask-docker:${env.BUILD_NUMBER} matanm66/python-flask-docker:${env.BUILD_NUMBER}"
                    sh "docker push matanm66/python-flask-docker:${env.BUILD_NUMBER}"
                }
            }
        }
    }
}
```

### Explanation:
- **stage('Push Docker Image')**: This stage logs in to Docker Hub and pushes the built Docker image to your Docker Hub repository.
- **withCredentials**: This block ensures that the sensitive Docker credentials (`dockerhub-creds`) are securely passed into the pipeline. The credentials are fetched using the `credentialsId` and stored in two environment variables, `DOCKER_USER` and `DOCKER_PASS`, which are used later in the commands.
    - `usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')`: Retrieves the DockerHub credentials (username and password) stored in Jenkins under the ID `dockerhub-creds`.
  
- **sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"**:
    - This command logs in to Docker Hub using the credentials stored in `DOCKER_USER` and `DOCKER_PASS`. The `--password-stdin` option securely passes the password to Docker via standard input, avoiding insecure usage like `docker login -p`.

- **docker tag python-flask-docker:${env.BUILD_NUMBER} matanm66/python-flask-docker:${env.BUILD_NUMBER}**:
    - This command tags the built image `python-flask-docker:${env.BUILD_NUMBER}` with a new tag, `matanm66/python-flask-docker:${env.BUILD_NUMBER}`, which corresponds to the Docker Hub repository `matanm66`.

- **docker push matanm66/python-flask-docker:${env.BUILD_NUMBER}**:
    - This command pushes the tagged Docker image to the Docker Hub repository `matanm66/python-flask-docker`, using the unique build number as the tag.

---

### Summary of the Pipeline

1. **Agent Declaration**: The pipeline runs on a Jenkins agent labeled `docker-slave`.
2. **Checkout Stage**: Jenkins pulls the source code from the specified GitHub repository using stored credentials.
3. **Build Docker Image Stage**: Jenkins builds a Docker image based on the repository’s Dockerfile and tags it with the Jenkins build number.
4. **Test Docker Image Stage**: Placeholder stage to run any tests on the Docker image (optional).
5. **Push Docker Image Stage**: Jenkins logs in to Docker Hub using stored credentials, tags the image appropriately, and pushes it to the specified Docker Hub repository.
