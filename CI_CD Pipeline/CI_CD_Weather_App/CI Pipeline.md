## **Code:** `ci-pipline.yml`

```YAML
name: CI Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  lint-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.12'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run flake8 (Linting)
        run: flake8 app/

      - name: Run tests
        env:
            PYTHONPATH: ./app 
            OPENWEATHER_API_KEY: ${{ secrets.OPENWEATHER_API_KEY }}
        run: pytest tests/
  build:
    runs-on: ubuntu-latest
    needs: lint-test

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: ${{ secrets.DOCKER_USERNAME }}/weather-app:latest
          build-args: |
            OPENWEATHER_API_KEY=${{ secrets.OPENWEATHER_API_KEY }}
```

---

## **CI Pipeline Configuration**
#### **name: CI Pipeline**

- **Purpose:** Defines the name of the workflow. This is what will appear in the GitHub Actions interface when the workflow runs.

---

#### **on:**
- **Purpose:** Specifies the events that trigger the workflow.

  ```yaml
  on:
    push:
      branches:
        - main
    pull_request:
      branches:
        - main
  ```

- **`push:`** 
  - Triggers the workflow when code is pushed to the `main` branch.

- **`pull_request:`**
  - Triggers the workflow when a pull request is made against the `main` branch.

---

### **Jobs Section**

- **Purpose:** Defines the individual jobs that will run as part of the workflow. Each job runs in a fresh environment.

---

#### **`jobs:`**
- **`lint-test:`**
  - **Purpose:** A job to run linting and testing on the codebase.

  ```yaml
  lint-test:
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

- **Purpose:** This step checks out the code from the repository so that the workflow has access to the files.

- **`uses: actions/checkout@v3`**
  - Utilizes a predefined GitHub Action to checkout the repository code at the start of the workflow.

---

#### **Step 2: Set Up Python**

```yaml
- name: Set up Python
  uses: actions/setup-python@v2
  with:
    python-version: '3.12'
```

- **Purpose:** This step sets up the Python environment required for the project.

- **`uses: actions/setup-python@v2`**
  - Uses a predefined GitHub Action to install and configure Python 3.12.

---

#### **Step 3: Install Dependencies**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```

- **Purpose:** This step installs the necessary Python dependencies defined in the `requirements.txt` file.

- **`python -m pip install --upgrade pip`**
  - Upgrades `pip`, the Python package installer, to the latest version.

- **`pip install -r requirements.txt`**
  - Installs all dependencies listed in the `requirements.txt` file.

---

#### **Step 4: Run flake8 (Linting)**

```yaml
- name: Run flake8 (Linting)
  run: flake8 app/
```

- **Purpose:** This step runs `flake8`, a Python linting tool, to check for coding style violations.

- **`run: flake8 app/`**
  - Executes `flake8` on the `app/` directory to ensure the code follows Python style guidelines.

---

#### **Step 5: Run Tests**

```yaml
- name: Run tests
  env:
      PYTHONPATH: ./app 
      OPENWEATHER_API_KEY: ${{ secrets.OPENWEATHER_API_KEY }}
  run: pytest tests/
```

- **Purpose:** This step runs the tests using `pytest`.

- **`env:`**
  - **`PYTHONPATH: ./app`**
    - Adds the `app/` directory to the Python path so that the tests can find and import the `app` module.
  - **`OPENWEATHER_API_KEY: ${{ secrets.OPENWEATHER_API_KEY }}`**
    - Provides the `OPENWEATHER_API_KEY` as an environment variable during testing, which is required by the Flask application to make API requests.

- **`run: pytest tests/`**
  - Runs the tests located in the `tests/` directory using `pytest`.

---

### **Build Job**

#### **`build:`**
- **Purpose:** A job to build and push the Docker image after successful linting and testing.

```yaml
build:
  runs-on: ubuntu-latest
  needs: lint-test
```

- **`runs-on: ubuntu-latest`**
  - Specifies that the job will run in an Ubuntu Linux environment.

- **`needs: lint-test`**
  - Ensures that this job will only run if the `lint-test` job completes successfully.

---

### **Build Job Steps**

#### **Step 1: Checkout Code (Again)**

```yaml
- name: Checkout code
  uses: actions/checkout@v3
```

- **Purpose:** This step checks out the code again for the build process.

---

#### **Step 2: Set Up Docker Buildx**

```yaml
- name: Set up Docker Buildx
  uses: docker/setup-buildx-action@v2
```

- **Purpose:** Sets up Docker Buildx, a builder that allows building multi-platform images.

---

#### **Step 3: Login to DockerHub**

```yaml
- name: Login to DockerHub
  uses: docker/login-action@v3
  with:
    username: ${{ secrets.DOCKER_USERNAME }}
    password: ${{ secrets.DOCKER_PASSWORD }}
```

- **Purpose:** Logs in to DockerHub to push the built Docker image.

- **`with:`**
  - **`username: ${{ secrets.DOCKER_USERNAME }}`**
    - Uses the DockerHub username stored in GitHub secrets.
  - **`password: ${{ secrets.DOCKER_PASSWORD }}`**
    - Uses the DockerHub password stored in GitHub secrets.

---

#### **Step 4: Build and Push Docker Image**

```yaml
- name: Build and push Docker image
  uses: docker/build-push-action@v4
  with:
    context: .
    push: true
    tags: ${{ secrets.DOCKER_USERNAME }}/weather-app:latest
    build-args: |
      OPENWEATHER_API_KEY=${{ secrets.OPENWEATHER_API_KEY }}
```

- **Purpose:** Builds the Docker image for the Flask app and pushes it to DockerHub.

- **`context: .`**
  - Specifies the build context, which includes all files in the repository.

- **`push: true`**
  - Pushes the built image to DockerHub.

- **`tags: ${{ secrets.DOCKER_USERNAME }}/weather-app:latest`**
  - Tags the Docker image with the latest version and your DockerHub username.

- **`build-args:`**
  - **`OPENWEATHER_API_KEY=${{ secrets.OPENWEATHER_API_KEY }}`**
    - Passes the `OPENWEATHER_API_KEY` as a build argument to the Dockerfile, allowing the key to be available during the Docker build process.

