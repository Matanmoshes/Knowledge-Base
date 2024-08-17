### **Project Overview**

Weather application that uses Flask, Docker, Terraform, and CI/CD pipelines (GitHub Actions) to deploy the app on AWS. The app allows users to enter a city name and fetches the weather data for that city using the OpenWeather API.

---

### **Project Components**

1. **Flask Application (`app.py`):**
   - A simple web app where users can input a city name to get current weather information.
   - Uses the OpenWeather API, with the API key managed via environment variables.

2. **Docker (`Dockerfile`):**
   - Dockerizes the Flask application.
   - Defines the environment, installs dependencies, and exposes port `5000`.

3. **Terraform (`main.tf` and associated files):**
   - Manages the AWS infrastructure:
     - Sets up a VPC, subnets, an Internet Gateway, and a Route Table.
     - Creates an ECS cluster to run the Docker container.
     - Configures a Network Load Balancer (NLB) to handle incoming traffic.
     - Manages Security Groups for network access control.

4. **CI/CD Pipelines (`ci.yml` and `cd.yml`):**
   - **CI Pipeline (`ci.yml`):**
     - Triggers on code changes (push or pull request to `main`).
     - Runs linting with Flake8 and tests with Pytest.
     - Builds and pushes the Docker image to DockerHub after successful tests.
   - **CD Pipeline (`cd.yml`):**
     - Triggers after the CI pipeline completes.
     - Deploys the application to AWS using Terraform.
     - Outputs the Elastic IP (EIP) for accessing the application.

---

### **How It Works (Step-by-Step)**

1. **Code Changes:**
   - Code is pushed to the `main` branch or a pull request is made.

2. **CI Pipeline:**
   - **Linting and Testing:**
     - Runs Flake8 for linting and Pytest for unit tests.
   - **Docker Build and Push:**
     - Builds the Docker image and pushes it to DockerHub if tests pass.

3. **CD Pipeline:**
   - **Terraform Deployment:**
     - Applies Terraform to set up the AWS infrastructure and deploy the app.
   - **Application Access:**
     - Outputs the NLB’s Elastic IP for accessing the app on port `80`.

---

### **Application Workflow**

1. **User Interaction:**
   - User visits the app via the NLB’s IP address and inputs a city name.

2. **Backend Process:**
   - The Flask app fetches weather data from the OpenWeather API.
   - Calculates the local time for the city and displays the weather info.

3. **Output:**
   - The user sees the weather data and local time for the city they searched for.

---

### **Project Github Repo**

This setup ensures a fully automated and scalable deployment of your Flask application on AWS. The CI/CD pipelines enforce code quality and streamline the deployment process.

https://github.com/Matanmoshes/WeatherApp-CICD-Deployment