### Adding Triggers and SCM Polling to Jenkinsfile

Triggers in Jenkins allow you to automate when the pipeline runs. One of the most common ways to trigger a pipeline is using `pollSCM`, which polls the source control repository (e.g., GitHub) for changes at regular intervals. If changes are detected, the pipeline runs.

The `pollSCM` trigger is defined using cron-like syntax, which specifies the schedule for polling the source control repository.

### Example with SCM Polling

In this example, we add a **trigger** to poll the SCM (Source Control Management) every 5 minutes using `pollSCM '*/5 * * * *'`. This ensures the pipeline checks for new changes in the Git repository every 5 minutes and runs the pipeline if any new changes are detected.

```groovy
pipeline {
    agent { 
        node {
            label 'docker-agent-python'  // The Jenkins agent where the pipeline will run
        }
    }
    triggers {
        // Poll the source control repository every 5 minutes
        pollSCM '*/5 * * * *'
    }
    stages {
        stage('Build') {
            steps {
                echo "Building.."
                // Navigating into the app directory and installing dependencies
                sh '''
                cd myapp
                pip install -r requirements.txt
                '''
            }
        }
        stage('Test') {
            steps {
                echo "Testing.."
                // Running the Python script with optional argument
                sh '''
                cd myapp
                python3 hello.py
                python3 hello.py --name=Brad
                '''
            }
        }
        stage('Deliver') {
            steps {
                echo 'Deliver....'
                // Placeholder for delivery or deployment steps
                sh '''
                echo "doing delivery stuff.."
                '''
            }
        }
    }
}
```

#### Breakdown of Triggers and SCM Polling:

- **pollSCM '*/5 * * * *'**: This line tells Jenkins to poll the SCM (e.g., GitHub) every 5 minutes. The syntax follows the cron format.
  - `*/5` → Poll every 5 minutes
  - `*` → For every hour
  - `*` → For every day of the month
  - `*` → For every month
  - `*` → For every day of the week
  
  This trigger checks the repository for changes at the specified interval. If there are any changes, Jenkins will automatically start the pipeline. If no changes are detected, the pipeline will not run.

#### Example Explanation:
- **Agent**: The pipeline runs on a node labeled `docker-agent-python`, which is a Docker-based Jenkins agent configured with Python.
- **Build Stage**: Installs the necessary dependencies using `pip install` from the `requirements.txt` file.
- **Test Stage**: Executes the Python scripts (`hello.py`) with optional arguments to test the application.
- **Deliver Stage**: Represents a placeholder for deployment or delivery tasks after the build and tests are completed.

### Setting Up Triggers in Jenkins:

1. **Edit the Jenkinsfile**: Add the `triggers` block with the `pollSCM` trigger inside your Jenkinsfile, as shown in the example.
2. **Commit and Push**: Push the updated Jenkinsfile to your repository.
3. **Polling Configuration**: Jenkins will now poll the repository based on the schedule defined in the `pollSCM` trigger and run the pipeline when changes are detected.

---

### Benefits of Using Triggers and SCM Polling:

- **Automation**: Automates the pipeline run when new changes are pushed to the repository.
- **Efficiency**: You don't need to manually trigger a build every time there’s a change in the repository.
- **Customizable Scheduling**: You can set the polling schedule based on your needs (e.g., every 5 minutes, hourly, or daily).

### Best Practices:
- **Optimize Polling Interval**: For busy projects, polling too frequently can put unnecessary load on both Jenkins and the repository. Adjust the interval based on the activity in your repository.
- **Webhooks**: In some cases, using webhooks might be more efficient than polling, as they trigger builds immediately when a push happens, without the need for constant polling.

By using SCM polling and triggers, Jenkins can automatically detect changes in your source control system and ensure that builds, tests, and deployments are done consistently.