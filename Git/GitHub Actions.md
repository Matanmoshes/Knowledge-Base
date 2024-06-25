GitHub Actions allow you to automate tasks within your GitHub repository. This guide will cover the basics of creating a workflow, common use cases, and best practices.

### What is GitHub Actions?

GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. You can write individual tasks, called actions, and combine them to create a custom workflow.

### Basic Concepts

- **Workflow**: A configurable automated process made up of one or more jobs.
- **Job**: A set of steps that execute on the same runner.
- **Step**: A single task within a job. It can run a script or an action.
- **Action**: A reusable unit of code that can perform a specific task.
- **Runner**: A server that runs your workflows when they're triggered.

### Setting Up a Workflow

1. **Create the Workflow File**: Workflows are defined in YAML files stored in the `.github/workflows` directory of your repository.

   ```bash
   mkdir -p .github/workflows
   ```

2. **Define the Workflow**: Create a YAML file (e.g., `ci.yml`) in the `.github/workflows` directory.

   ```yaml
   name: CI

   on:
     push:
       branches: [ main ]
     pull_request:
       branches: [ main ]

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

### Workflow Triggers

Workflows can be triggered by various events:

- **push**: Triggered when code is pushed to the repository.
- **pull_request**: Triggered when a pull request is created or updated.
- **schedule**: Triggered on a scheduled basis using cron syntax.
- **workflow_dispatch**: Triggered manually via the GitHub UI.

Example for manual trigger:

```yaml
on:
  workflow_dispatch:
```

### Common Use Cases

1. **Continuous Integration (CI)**: Automatically build and test your code.

   ```yaml
   name: CI

   on: [push, pull_request]

   jobs:
     build:
       runs-on: ubuntu-latest

       steps:
       - uses: actions/checkout@v2
       - name: Set up Node.js
         uses: actions/setup-node@v2
         with:
           node-version: '14'
       - run: npm install
       - run: npm test
   ```

2. **Continuous Deployment (CD)**: Deploy your application after successful builds.

   ```yaml
   name: CD

   on:
     push:
       branches: [ main ]

   jobs:
     deploy:
       runs-on: ubuntu-latest

       steps:
       - uses: actions/checkout@v2
       - name: Deploy to Server
         run: |
           ssh user@server 'cd /path/to/app && git pull && npm install && pm2 restart all'
   ```

3. **Scheduled Jobs**: Run periodic tasks such as backups or cleanup scripts.

   ```yaml
   name: Scheduled Backup

   on:
     schedule:
       - cron: '0 0 * * *' # Runs every day at midnight

   jobs:
     backup:
       runs-on: ubuntu-latest

       steps:
       - name: Run backup script
         run: ./scripts/backup.sh
   ```

### Best Practices

1. **Use Secrets for Sensitive Information**: Store sensitive data like API keys in GitHub Secrets.

   ```yaml
   - name: Deploy
     run: echo "Deploying to ${{ secrets.PRODUCTION_SERVER }}"
   ```

2. **Use Cache for Dependencies**: Speed up your workflows by caching dependencies.

   ```yaml
   - name: Cache dependencies
     uses: actions/cache@v2
     with:
       path: ~/.npm
       key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
       restore-keys: |
         ${{ runner.os }}-node-
   ```

3. **Modularize Your Workflows**: Break down complex workflows into smaller, reusable actions.

   ```yaml
   - name: Reusable CI Workflow
     uses: ./.github/workflows/ci.yml
   ```

4. **Test Locally**: Use tools like `act` to run your workflows locally before pushing to GitHub.

   ```bash
   act -j build
   ```

### Advanced Features

1. **Matrix Builds**: Run multiple jobs in parallel with different configurations.

   ```yaml
   jobs:
     test:
       runs-on: ubuntu-latest
       strategy:
         matrix:
           node: [10, 12, 14]
           os: [ubuntu-latest, windows-latest, macos-latest]

       steps:
       - uses: actions/checkout@v2
       - name: Setup Node.js
         uses: actions/setup-node@v2
         with:
           node-version: ${{ matrix.node }}
       - run: npm install
       - run: npm test
   ```

2. **Conditional Execution**: Run steps or jobs based on conditions.

   ```yaml
   jobs:
     example:
       runs-on: ubuntu-latest
       steps:
       - name: Conditional step
         run: echo "This runs only on main branch"
         if: github.ref == 'refs/heads/main'
   ```

### Conclusion

GitHub Actions is a powerful tool for automating your workflows. By defining workflows in YAML files, you can automate a wide range of tasks from CI/CD to scheduled jobs and more. Follow best practices to ensure your workflows are efficient and secure.

Feel free to customize and extend this guide based on your specific use case. Happy automating!