
## **Code:** `cd-pipeline.yml`

```YAML
name: CD Pipeline

on:
    workflow_run:
        workflows: ["CI Pipeline"]
        types:
            - completed
jobs:
    deploy:
        runs-on: ubuntu-latest

        steps:
            - name: Checkout code
              uses: actions/checkout@v3

            - name: Setup Terraform
              uses: hashicorp/setup-terraform@v2

            - name: Terraform Init
              run: terraform init
              working-directory: terraform

            - name: Terraform Apply
              run: terraform apply -auto-approve
              working-directory: terraform
              env:
                AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
                AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                TF_VAR_openweather_api_key: ${{ secrets.OPENWEATHER_API_KEY }}

            - name: Post Apply - Output EIP
              run: terraform output -raw elastic_ip
              working-directory: terraform
              id: eip_output

            - name: Display the application URL
              run: echo "Application is deployed at http://$(terraform output -raw elastic_ip):5000"
```


---

## **CD Pipeline Configuration**

#### **name: CD Pipeline**

- **Purpose:** Defines the name of the workflow. This name will appear in the GitHub Actions interface when the workflow runs.

---

#### **on:**
- **Purpose:** Specifies the event that triggers the workflow.

  ```yaml
  on:
      workflow_run:
          workflows: ["CI Pipeline"]
          types:
              - completed
  ```

- **`workflow_run:`**
  - This key defines that the `CD Pipeline` will be triggered when another workflow, in this case, the "CI Pipeline," completes.

- **`workflows: ["CI Pipeline"]`**
  - Specifies that this pipeline depends on the successful completion of the "CI Pipeline" workflow.

- **`types: - completed`**
  - Triggers the CD pipeline when the "CI Pipeline" has finished running, regardless of whether it succeeded or failed.

---

### **Jobs Section**

- **Purpose:** Defines the individual jobs that will run as part of the workflow. Each job runs in a fresh environment.

---

#### **`jobs:`**
- **`deploy:`**
  - **Purpose:** A job to deploy the application infrastructure using Terraform.

  ```yaml
  deploy:
    runs-on: ubuntu-latest
  ```

- **`runs-on: ubuntu-latest`**
  - Specifies the environment in which this job will run. In this case, it’s an Ubuntu Linux environment.

---

### **Job Steps**

- **Purpose:** Defines a sequence of actions or commands that will be executed in the job.

---

#### **Step 1: Checkout Code**

```yaml
- name: Checkout code
  uses: actions/checkout@v3
```

- **Purpose:** This step checks out the code from the repository so that the workflow has access to the files necessary for deployment.

- **`uses: actions/checkout@v3`**
  - Utilizes a predefined GitHub Action to checkout the repository code at the start of the workflow.

---

#### **Step 2: Setup Terraform**

```yaml
- name: Setup Terraform
  uses: hashicorp/setup-terraform@v2
```

- **Purpose:** Sets up the Terraform environment required for managing your AWS infrastructure.

- **`uses: hashicorp/setup-terraform@v2`**
  - Utilizes a predefined GitHub Action to set up Terraform CLI, making Terraform commands available in the workflow.

---

#### **Step 3: Terraform Init**

```yaml
- name: Terraform Init
  run: terraform init
  working-directory: terraform
```

- **Purpose:** Initializes the Terraform configuration in the specified directory, preparing it for deployment.

- **`run: terraform init`**
  - Runs the Terraform `init` command, which initializes the Terraform working directory by downloading the necessary provider plugins.

- **`working-directory: terraform`**
  - Specifies that the Terraform commands should be executed in the `terraform` directory, where your Terraform configuration files are located.

---

#### **Step 4: Terraform Apply**

```yaml
- name: Terraform Apply
  run: terraform apply -auto-approve
  working-directory: terraform
  env:
    AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
    AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    TF_VAR_openweather_api_key: ${{ secrets.OPENWEATHER_API_KEY }}
```

- **Purpose:** Applies the Terraform configuration to deploy or update the infrastructure.

- **`run: terraform apply -auto-approve`**
  - Runs the Terraform `apply` command with the `-auto-approve` flag, which automatically applies the changes without waiting for manual approval.

- **`env:`**
  - **`AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}`**
    - Uses the AWS access key ID stored in GitHub secrets to authenticate with AWS.
  - **`AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}`**
    - Uses the AWS secret access key stored in GitHub secrets for authentication.
  - **`TF_VAR_openweather_api_key: ${{ secrets.OPENWEATHER_API_KEY }}`**
    - Passes the OpenWeather API key as a Terraform variable, which is necessary for the Flask application.

---

#### **Step 5: Post Apply - Output EIP**

```yaml
- name: Post Apply - Output EIP
  run: terraform output -raw elastic_ip
  working-directory: terraform
  id: eip_output
```

- **Purpose:** Retrieves the Elastic IP (EIP) output from the Terraform deployment to use it later in the workflow.

- **`run: terraform output -raw elastic_ip`**
  - Runs the Terraform `output` command to get the value of the `elastic_ip` output from the Terraform configuration.

- **`id: eip_output`**
  - Assigns an ID to this step’s output so it can be referenced in subsequent steps.

---

#### **Step 6: Display the Application URL**

```yaml
- name: Display the application URL
  run: echo "Application is deployed at http://$(terraform output -raw elastic_ip):5000"
```

- **Purpose:** Displays the URL of the deployed application using the Elastic IP obtained from the previous step.

- **`run: echo "Application is deployed at http://$(terraform output -raw elastic_ip):5000"`**
  - Outputs a message that shows the URL where the Flask application is accessible. The URL is composed of the Elastic IP and the port `5000`, where the application is running.

---

