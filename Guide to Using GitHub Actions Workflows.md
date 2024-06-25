
## Guide to Using GitHub Actions Workflows

### Step 1: Understanding GitHub Actions and Workflows

**GitHub Actions** allows you to automate your software development workflows directly from your GitHub repository. Workflows are defined using YAML syntax and are stored in your repository in the `.github/workflows` directory.

### Step 2: Setting Up Your Repository

1. **Create a GitHub Repository**:
   - Go to [GitHub](https://github.com/).
   - Click on the `+` sign in the top right corner and select `New repository`.
   - Fill in the repository details and click `Create repository`.

2. **Clone Your Repository** (if you haven't already):
   ```bash
   git clone https://github.com/your-username/your-repository.git
   cd your-repository
   ```

### Step 3: Creating a Workflow File

1. **Create a Workflow Directory**:
   ```bash
   mkdir -p .github/workflows
   ```

2. **Create a Workflow YAML File**:
   ```bash
   touch .github/workflows/main.yml
   ```

### Step 4: Writing Your First Workflow

Open `main.yml` in your preferred text editor and add the following content:

```yaml
name: CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14'

    - name: Install dependencies
      run: npm install

    - name: Run tests
      run: npm test
```

### Step 5: Understanding the Workflow File

- **name**: The name of the workflow (e.g., `CI`).
- **on**: Specifies the events that trigger the workflow. This example triggers on pushes and pull requests to the `main` branch.
- **jobs**: Defines the jobs to run. This example includes a single job named `build`.
- **runs-on**: Specifies the type of machine to run the job on (e.g., `ubuntu-latest`).
- **steps**: Lists the steps to execute in the job:
  - **Checkout code**: Uses the `actions/checkout` action to check out the repository.
  - **Set up Node.js**: Uses the `actions/setup-node` action to set up Node.js version 14.
  - **Install dependencies**: Runs `npm install` to install dependencies.
  - **Run tests**: Runs `npm test` to execute tests.

### Step 6: Committing and Pushing Your Workflow

1. **Add, Commit, and Push**:
   ```bash
   git add .github/workflows/main.yml
   git commit -m "Add CI workflow"
   git push origin main
   ```

2. **Check the Workflow**:
   - Go to your repository on GitHub.
   - Click on the `Actions` tab.
   - You should see your workflow running under the `CI` workflow.

### Step 7: Adding More Jobs and Steps

You can add more jobs and steps to your workflow. For example, to add a deployment job:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v2
    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14'
    - name: Install dependencies
      run: npm install
    - name: Run tests
      run: npm test

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
    - name: Checkout code
      uses: actions/checkout@v2
    - name: Deploy to Azure
      uses: azure/webapps-deploy@v2
      with:
        app-name: 'your-app-name'
        slot-name: 'production'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

### Step 8: Using Secrets

**Secrets** are used to store sensitive information like API keys. To add a secret:

1. Go to your repository on GitHub.
2. Click on `Settings`.
3. Click on `Secrets and variables` -> `Actions`.
4. Click `New repository secret`.
5. Add your secret with a name (e.g., `AZURE_WEBAPP_PUBLISH_PROFILE`).

### Step 9: Advanced Workflows

#### Matrix Builds

Matrix builds allow you to run multiple configurations:

```yaml
strategy:
  matrix:
    node-version: [12.x, 14.x, 16.x]
```

#### Caching Dependencies

Caching can speed up your workflows:

```yaml
- name: Cache dependencies
  uses: actions/cache@v2
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

### Step 10: Monitoring and Debugging

- **View Logs**: In the `Actions` tab, click on a workflow run to see detailed logs.
- **Re-run Jobs**: You can re-run failed jobs directly from the GitHub interface.

### Conclusion

GitHub Actions provides a powerful way to automate your workflows. By following this guide, you can set up, customize, and extend your workflows to fit your project's needs. If you need more advanced features or run into issues, the [GitHub Actions documentation](https://docs.github.com/en/actions) is a great resource.