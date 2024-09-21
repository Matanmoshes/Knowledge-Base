### Jenkins CI Pipeline Guide

In this guide, we’ll break down a complete Jenkins pipeline script, written in Groovy, to help you understand each section and how Jenkins CI can be configured to build, test, and deploy your project.

---

### Plugins Needed

- **Git Plugin**: For Git integration.
- **Pipeline Plugin**: Core pipeline functionality.
- **Docker Plugin**: For building and pushing Docker images.
- **Credentials Binding Plugin**: For securely managing environment variables like API keys.

---

### Here’s the full `Jenkinsfile`:

```groovy
pipeline {
    agent {
        node {
            label 'agent-node-ci' // Use an agent node with the label 'agent-node-ci'
        }
    }

    triggers {
        pollSCM('H/5 * * * *')  // Poll SCM every 5 minutes to check for changes (can be adjusted)
    }

    environment {
        OPENWEATHER_API_KEY = credentials('OPENWEATHER_API_KEY') // Use Jenkins credentials for the API key
        DOCKER_USERNAME = credentials('DOCKER_USERNAME')
        DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from repository...'
                git branch: 'main', url: 'https://github.com/your-repo/your-project.git'
            }
        }

        stage('Set Up Python') {
            steps {
                echo 'Setting up Python 3.12...'
                sh '''
                python3 -m pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Linting') {
            steps {
                echo 'Running flake8 for linting...'
                sh 'flake8 app/'
            }
        }

        stage('Testing') {
            steps {
                echo 'Running tests...'
                sh '''
                export PYTHONPATH=./app
                pytest tests/
                '''
            }
        }

        stage('Docker Build and Push') {
            when {
                branch 'main'  // Only run this on the main branch
            }
            steps {
                echo 'Building and pushing Docker image...'
                script {
                    docker.withRegistry('', "$DOCKER_USERNAME:$DOCKER_PASSWORD") {
                        sh '''
                        docker build -t ${DOCKER_USERNAME}/weather-app:latest .
                        docker push ${DOCKER_USERNAME}/weather-app:latest
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs() // Clean workspace after the pipeline runs
        }
    }
}
```

---

### Breakdown of the Jenkins Pipeline

#### 1. **Pipeline Block**

The `pipeline {}` block defines the structure of the Jenkins pipeline. It contains all stages and configuration for the job.

```groovy
pipeline {
    // Pipeline contents go here...
}
```

---

#### 2. **Agent Section**

The `agent` block defines where the pipeline runs. In this example, the pipeline is set to run on a node labeled `'agent-node-ci'`.

```groovy
agent {
    node {
        label 'agent-node-ci'
    }
}
```

- **node**: Refers to the Jenkins agent where the job will be executed. The label `'agent-node-ci'` specifies a particular agent.

---

#### 3. **Triggers Section**

The `triggers` block defines automatic triggers for the pipeline. Here, we’re using the `pollSCM` trigger, which checks the source control for changes every 5 minutes.

```groovy
triggers {
    pollSCM('H/5 * * * *')
}
```

- **pollSCM**: This schedules the pipeline to poll the source code management (SCM) system, such as GitHub, for changes. If changes are found, the pipeline is triggered.

---

#### 4. **Environment Section**

The `environment` block sets up environment variables. Here, we're pulling sensitive credentials such as the OpenWeather API key and DockerHub credentials from Jenkins' secure credentials storage.

```groovy
environment {
    OPENWEATHER_API_KEY = credentials('OPENWEATHER_API_KEY')
    DOCKER_USERNAME = credentials('DOCKER_USERNAME')
    DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
}
```

- **credentials**: Jenkins uses the credentials stored in its Credentials Manager to handle sensitive information securely.

---

#### 5. **Stages Section**

The `stages` block contains the various steps of the pipeline. Each stage represents a phase in the CI pipeline.

---

##### Stage: **Checkout**

The first stage checks out the source code from the repository.

```groovy
stage('Checkout') {
    steps {
        echo 'Checking out code from repository...'
        git branch: 'main', url: 'https://github.com/your-repo/your-project.git'
    }
}
```

- **git**: This command pulls the latest code from the `main` branch of the specified GitHub repository.

---

##### Stage: **Set Up Python**

This stage installs Python dependencies, setting up the environment for testing and building the application.

```groovy
stage('Set Up Python') {
    steps {
        echo 'Setting up Python 3.12...'
        sh '''
        python3 -m pip install --upgrade pip
        pip install -r requirements.txt
        '''
    }
}
```

- **sh**: Executes shell commands. It installs and upgrades `pip` and installs the project dependencies from `requirements.txt`.

---

##### Stage: **Linting**

This stage runs `flake8` to check for any code style or quality issues.

```groovy
stage('Linting') {
    steps {
        echo 'Running flake8 for linting...'
        sh 'flake8 app/'
    }
}
```

- **flake8**: A Python tool that checks the source code for PEP8 compliance (Python's style guide).

---

##### Stage: **Testing**

This stage runs the project's unit tests using `pytest`.

```groovy
stage('Testing') {
    steps {
        echo 'Running tests...'
        sh '''
        export PYTHONPATH=./app
        pytest tests/
        '''
    }
}
```

- **PYTHONPATH**: Sets the path for Python to find the app's modules.
- **pytest**: A testing framework for running the project’s unit tests.

---

##### Stage: **Docker Build and Push**

In this stage, a Docker image is built and pushed to DockerHub if the pipeline is running on the `main` branch.

```groovy
stage('Docker Build and Push') {
    when {
        branch 'main'
    }
    steps {
        echo 'Building and pushing Docker image...'
        script {
            docker.withRegistry('', "$DOCKER_USERNAME:$DOCKER_PASSWORD") {
                sh '''
                docker build -t ${DOCKER_USERNAME}/weather-app:latest .
                docker push ${DOCKER_USERNAME}/weather-app:latest
                '''
            }
        }
    }
}
```

- **docker.withRegistry**: Authenticates with DockerHub using the credentials `DOCKER_USERNAME` and `DOCKER_PASSWORD`.
- **docker build**: Builds the Docker image for the weather app.
- **docker push**: Pushes the image to DockerHub.

---

#### 6. **Post Section**

The `post` block defines actions to be taken after the pipeline finishes. In this example, it always cleans up the workspace, regardless of the build result.

```groovy
post {
    always {
        echo 'Cleaning up workspace...'
        cleanWs()
    }
}
```

- **cleanWs**: A Jenkins Pipeline Utility step to clean the workspace, removing all files created during the build.

---

### Summary

In this Jenkins CI pipeline:

- We defined a pipeline that runs on an agent labeled `'agent-node-ci'`.
- We set up automatic triggers to check for changes in the source control system every 5 minutes using `pollSCM`.
- We created multiple stages including code checkout, Python setup, linting, testing, and building/pushing a Docker image.
- The pipeline uses Jenkins' environment variables and securely handles credentials.
- After the pipeline finishes, it cleans up the workspace to ensure a clean state for future builds.