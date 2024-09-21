# Jenkinsfile Guide

A **Jenkinsfile** is a script that defines a Jenkins pipeline. This file is used to automate building, testing, and deploying projects using Jenkins. The Jenkinsfile can be written in **Declarative** or **Scripted** syntax. This guide focuses on the key components of a Jenkinsfile and explains how to create and manage pipelines.

#### Key Benefits of Using a Jenkinsfile:
- **Version Control**: Jenkinsfiles are stored in source control (e.g., GitHub), which allows tracking changes and collaboration.
- **Pipeline as Code**: The Jenkinsfile defines the entire process from build to deployment, making the pipeline reproducible and easy to manage.
- **Automation**: With Jenkinsfile, you can automate the entire CI/CD pipeline.

### Types of Pipelines:
1. **Declarative Pipeline**: Easier to read and write, and more structured.
2. **Scripted Pipeline**: More flexible, but requires a deeper understanding of Groovy syntax.

---

### 1. **Declarative Pipeline Example**

This is a simple example of a Jenkinsfile using Declarative syntax:

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'make build'  // Replace with your build command
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'make test'  // Replace with your test command
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                sh 'make deploy'  // Replace with your deploy command
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
```

#### Explanation:
- **pipeline**: This defines the pipeline block.
- **agent any**: Specifies where the pipeline should run. "any" means it can run on any available Jenkins agent.
- **stages**: Defines a sequence of stages (e.g., Build, Test, Deploy).
- **steps**: Contains the commands to execute in each stage.
- **post**: Actions to take after the pipeline completes, such as notifications on success or failure.

### 2. **Scripted Pipeline Example**

This is an example using the scripted pipeline syntax:

```groovy
node {
    try {
        stage('Build') {
            echo 'Building...'
            sh 'make build'  // Replace with your build command
        }
        
        stage('Test') {
            echo 'Testing...'
            sh 'make test'  // Replace with your test command
        }
        
        stage('Deploy') {
            echo 'Deploying...'
            sh 'make deploy'  // Replace with your deploy command
        }
    } catch (Exception e) {
        echo "Pipeline failed with error: ${e}"
        currentBuild.result = 'FAILURE'
    } finally {
        echo "Cleaning up"
        deleteDir()
    }
}
```

#### Explanation:
- **node**: The scripted pipeline runs inside a `node` block, which allocates a Jenkins agent.
- **try-catch-finally**: Handles exceptions and allows for error handling.
- **deleteDir()**: Cleans up the workspace at the end of the pipeline.

---

### 3. **Key Jenkinsfile Components**

#### 3.1 **Agent**
Defines where the pipeline will run (which machine or node):

- `agent any`: Runs on any available agent.
- `agent none`: No default agent is assigned, and agents must be specified for each stage.

Example:
```groovy
pipeline {
    agent {
        label 'linux'  // Runs only on agents with the 'linux' label
    }
    ...
}
```

#### 3.2 **Stages**
Defines the sequence of tasks that the pipeline will execute. Each stage has steps that run commands or scripts.

Example:
```groovy
stages {
    stage('Build') {
        steps {
            sh 'make build'
        }
    }
    
    stage('Test') {
        steps {
            sh 'make test'
        }
    }
}
```

#### 3.3 **Steps**
The commands that are executed within each stage. Common steps include:

- **sh**: Runs a shell script (for Linux).
- **bat**: Runs a batch script (for Windows).
- **echo**: Prints messages to the console.

#### 3.4 **Post Actions**
Defines actions to take after the pipeline execution:

Example:
```groovy
post {
    success {
        echo 'Pipeline completed successfully'
    }
    failure {
        echo 'Pipeline failed'
    }
}
```

#### 3.5 **Environment Variables**
You can define environment variables for use within the pipeline:

```groovy
pipeline {
    environment {
        PATH = "/usr/local/bin:$PATH"
        NODE_ENV = "production"
    }
    ...
}
```

---

### 4. **Using Jenkinsfile in Your Project**

1. **Create a Jenkinsfile**: Add a file named `Jenkinsfile` in the root of your project repository.
2. **Add the Pipeline Code**: Add the pipeline code (either Declarative or Scripted) inside the Jenkinsfile.
3. **Commit and Push**: Commit the Jenkinsfile to your repository.
4. **Configure Jenkins**: Set up a Jenkins job to use the pipeline from the Jenkinsfile in your repository.

---

### 5. **Advanced Features**

#### 5.1 **Parallel Stages**
Run stages in parallel to speed up the pipeline.

Example:
```groovy
stages {
    stage('Parallel Execution') {
        parallel {
            stage('Test A') {
                steps {
                    echo 'Running Test A...'
                }
            }
            stage('Test B') {
                steps {
                    echo 'Running Test B...'
                }
            }
        }
    }
}
```

#### 5.2 **Parameters**
You can pass parameters to the pipeline to make it dynamic:

Example:
```groovy
pipeline {
    parameters {
        string(name: 'ENVIRONMENT', defaultValue: 'production', description: 'Deployment environment')
    }
    stages {
        stage('Deploy') {
            steps {
                sh "echo Deploying to ${params.ENVIRONMENT}"
            }
        }
    }
}
```

---

### 6. **Best Practices**

- **Use Declarative Pipelines**: Unless you need extra flexibility, Declarative pipelines are easier to manage and read.
- **Store Jenkinsfiles in Source Control**: Always store your Jenkinsfile in version control so changes can be tracked.
- **Keep Pipelines Simple**: Avoid making your pipeline too complex. Break it into smaller pipelines if necessary.
- **Use Stages Logically**: Separate your pipeline into clear, logical stages like Build, Test, and Deploy.
