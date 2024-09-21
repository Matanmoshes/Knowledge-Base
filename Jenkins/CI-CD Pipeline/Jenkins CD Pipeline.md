### Jenkins CD Pipeline Guide

In this guide, we’ll break down a continuous deployment (CD) pipeline that is triggered after the successful completion of the CI pipeline. This pipeline uses **Terraform** to manage infrastructure on AWS and deploys an application. Below is the full `Jenkinsfile` for the CD pipeline and its detailed explanation.

---
### Necessary Plugins:
- **Git Plugin**: To interact with Git repositories.
- **Credentials Binding Plugin**: To handle AWS credentials and other sensitive information securely.
- **Pipeline: AWS Steps Plugin**: To interact with AWS in the Jenkins pipeline.
- **Terraform Plugin**: For better Terraform integration with Jenkins.

---

### Here’s the full `Jenkinsfile`:

```groovy
pipeline {
    agent {
        node {
            label 'agent-node-cd' // Define a CD agent node
        }
    }

    triggers {
        upstream(
            threshold: hudson.model.Result.SUCCESS,
            upstreamProjects: "CI Pipeline"  // Trigger CD after the CI pipeline is successfully completed
        )
    }

    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID') // Use Jenkins credentials for AWS access key
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY') // Use Jenkins credentials for AWS secret key
        OPENWEATHER_API_KEY = credentials('OPENWEATHER_API_KEY') // API key for the application
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                git branch: 'main', url: 'https://github.com/your-repo/your-project.git'
            }
        }

        stage('Setup Terraform') {
            steps {
                echo 'Setting up Terraform...'
                sh '''
                terraform -version
                terraform init
                '''
            }
        }

        stage('Apply Terraform') {
            steps {
                echo 'Applying Terraform configurations...'
                sh '''
                terraform apply -auto-approve
                '''
            }
        }

        stage('Post Apply - Output ALB DNS Name') {
            steps {
                echo 'Fetching ALB DNS name...'
                script {
                    def alb_dns_name = sh(script: 'terraform output -raw alb_dns_name', returnStdout: true).trim()
                    env.ALB_DNS_NAME = alb_dns_name
                }
                echo "ALB DNS Name: ${env.ALB_DNS_NAME}"
            }
        }

        stage('Display Application URL') {
            steps {
                echo "Application is deployed at http://${env.ALB_DNS_NAME}"
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs() // Clean up the workspace after the pipeline runs
        }
    }
}
```

---

### Breakdown of the Jenkins CD Pipeline

#### 1. **Pipeline Block**

This is the main block that defines the CD pipeline structure. It includes all stages and configuration details.

```groovy
pipeline {
    // Pipeline contents go here...
}
```

---

#### 2. **Agent Section**

The `agent` block defines which node (agent) the pipeline should run on. Here, the CD process is running on an agent labeled `'agent-node-cd'`.

```groovy
agent {
    node {
        label 'agent-node-cd'
    }
}
```

---

#### 3. **Triggers Section**

The `triggers` block sets up an automatic trigger that activates the CD pipeline after the CI pipeline is successfully completed.

```groovy
triggers {
    upstream(
        threshold: hudson.model.Result.SUCCESS,
        upstreamProjects: "CI Pipeline"  // This triggers when the "CI Pipeline" finishes successfully
    )
}
```

- **upstream**: This trigger sets up a dependency on the completion of the "CI Pipeline". The CD pipeline only runs when the CI pipeline finishes successfully.

---

#### 4. **Environment Section**

In this section, sensitive credentials like AWS access keys and the OpenWeather API key are securely handled using Jenkins’ built-in credentials management.

```groovy
environment {
    AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
    AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    OPENWEATHER_API_KEY = credentials('OPENWEATHER_API_KEY')
}
```

- **credentials**: Retrieves credentials securely stored in Jenkins for the AWS and API key.

---

#### 5. **Stages Section**

This section contains the different stages in the deployment process. Each stage defines a step in the deployment pipeline.

---

##### Stage: **Checkout**

This stage checks out the source code from the Git repository.

```groovy
stage('Checkout') {
    steps {
        echo 'Checking out code...'
        git branch: 'main', url: 'https://github.com/your-repo/your-project.git'
    }
}
```

---

##### Stage: **Setup Terraform**

This stage sets up Terraform to manage AWS infrastructure by initializing Terraform with the project configuration.

```groovy
stage('Setup Terraform') {
    steps {
        echo 'Setting up Terraform...'
        sh '''
        terraform -version
        terraform init
        '''
    }
}
```

- **terraform init**: Prepares the working directory and downloads any necessary plugins and modules.

---

##### Stage: **Apply Terraform**

This stage applies the Terraform configurations, which provision or update the required AWS resources.

```groovy
stage('Apply Terraform') {
    steps {
        echo 'Applying Terraform configurations...'
        sh '''
        terraform apply -auto-approve
        '''
    }
}
```

- **terraform apply**: Applies the changes defined in the Terraform configuration, automatically approving any prompts.

---

##### Stage: **Post Apply - Output ALB DNS Name**

This stage retrieves the output from Terraform, specifically the DNS name of the AWS Application Load Balancer (ALB), which is used to access the deployed application.

```groovy
stage('Post Apply - Output ALB DNS Name') {
    steps {
        echo 'Fetching ALB DNS name...'
        script {
            def alb_dns_name = sh(script: 'terraform output -raw alb_dns_name', returnStdout: true).trim()
            env.ALB_DNS_NAME = alb_dns_name
        }
        echo "ALB DNS Name: ${env.ALB_DNS_NAME}"
    }
}
```

- **terraform output -raw alb_dns_name**: This command retrieves the DNS name of the ALB from Terraform outputs.
- **env.ALB_DNS_NAME**: Stores the ALB DNS name in the environment variable for use in later stages.

---

##### Stage: **Display Application URL**

This stage displays the full URL of the deployed application using the ALB DNS name obtained from the previous stage.

```groovy
stage('Display Application URL') {
    steps {
        echo "Application is deployed at http://${env.ALB_DNS_NAME}"
    }
}
```

- **echo**: Outputs the final URL where the application can be accessed.

---

#### 6. **Post Section**

The `post` block is used to define actions that should always be executed after the pipeline finishes, such as cleaning up the workspace.

```groovy
post {
    always {
        echo 'Cleaning up workspace...'
        cleanWs()
    }
}
```

- **cleanWs**: Cleans up the workspace, removing any leftover files after the pipeline completes.

---

### Summary

This Jenkins CD pipeline deploys your infrastructure using Terraform and provides the URL for the deployed application via an AWS ALB. The key components include:

- **Agent Selection**: The pipeline runs on an agent labeled `'agent-node-cd'`.
- **Upstream Trigger**: The pipeline is triggered automatically after the successful completion of the CI pipeline.
- **Terraform Setup and Application**: Terraform is used to provision AWS resources.
- **Displaying the Application URL**: The DNS name of the AWS ALB is extracted and used to display the application’s URL.



