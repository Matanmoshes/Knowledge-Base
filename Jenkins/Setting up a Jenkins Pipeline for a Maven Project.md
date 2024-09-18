### Detailed Guide: Setting up a Jenkins Pipeline for a Maven Project

In this guide, we will go through the steps involved in setting up and running a Jenkins pipeline to build, test, and package a simple Java web application (WAR file) using Maven. The pipeline automates the process of checking out code from a GitHub repository, compiling the code, running tests, and packaging the application as a deployable WAR file. We'll also cover optional steps like archiving the WAR file in Jenkins.

#### Table of Contents:
1. **Pipeline Overview**
2. **Pipeline Setup in Jenkins**
3. **Detailed Breakdown of Each Stage**
4. **Optional: Archiving Artifacts**
5. **Final Notes**

---

### 1. Pipeline Overview

The pipeline consists of the following stages:
- **Checkout Code**: Clones the project from a GitHub repository.
- **Compile and Build**: Cleans and compiles the Maven project.
- **Run Unit Tests**: Executes unit tests using Maven’s test phase.
- **Package**: Packages the Java web application into a WAR file.
- **Archive WAR (Optional)**: Archives the generated WAR file in Jenkins for future use.

---

### 2. Pipeline Setup in Jenkins

#### Step 1: Creating the Jenkins Pipeline
1. **Login to Jenkins**.
2. Navigate to the **Dashboard** and click **New Item**.
3. Enter a name for the pipeline (e.g., `Maven-War-Build`), select **Pipeline**, and click **OK**.
4. In the pipeline configuration page, scroll down to the **Pipeline** section.
5. Choose **Pipeline script** and paste the following script:

   ```groovy
   node {
       // Stage 1: Checkout the code
       stage('Checkout Code') {
           echo 'Checking out the code...'
           checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Matanmoshes/hello-world-war.git']])
       }

       // Stage 2: Compile and Build
       stage('Compile-Build') {
           echo 'Compiling and building the project...'
           sh 'mvn clean compile'
       }

       // Stage 3: Run Unit Tests
       stage('Run Unit Test') {
           echo 'Running unit tests...'
           sh 'mvn test'
       }

       // Stage 4: Package the WAR
       stage('Package') {
           echo 'Packaging the project into a WAR file...'
           sh 'mvn package'
       }

       // Stage 5 (Optional): Archive the WAR file
       stage('Archive WAR') {
           echo 'Archiving the WAR file...'
           archiveArtifacts artifacts: '**/target/*.war', allowEmptyArchive: true
       }
   }
   ```

6. Click **Save** to save the pipeline configuration.

#### Step 2: Installing Required Plugins (if not already installed)
- Jenkins needs to have **Git** and **Maven** installed. You can manage plugins under **Manage Jenkins** > **Manage Plugins**.

#### Step 3: Running the Pipeline
1. Once saved, click **Build Now** from the project page.
2. You will see the build progress with logs available under **Console Output**.

---

### 3. Detailed Breakdown of Each Stage

#### Stage 1: Checkout Code

```groovy
stage('Checkout Code') {
    echo 'Checking out the code...'
    checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Matanmoshes/hello-world-war.git']])
}
```

- **Purpose**: This stage checks out the code from the GitHub repository.
- **Explanation**:
  - `checkout scmGit(...)`: This is used to pull the code from the `master` branch of the specified GitHub repository.
  - It fetches the latest code to the Jenkins workspace where the build will occur.

#### Stage 2: Compile and Build

```groovy
stage('Compile-Build') {
    echo 'Compiling and building the project...'
    sh 'mvn clean compile'
}
```

- **Purpose**: Cleans the workspace and compiles the Java project using Maven.
- **Explanation**:
  - `mvn clean compile`: Maven first cleans the target directory (`clean`) and then compiles the source code (`compile`). It does not package the code yet; it simply ensures that the source files are properly compiled.

#### Stage 3: Run Unit Tests

```groovy
stage('Run Unit Test') {
    echo 'Running unit tests...'
    sh 'mvn test'
}
```

- **Purpose**: Runs unit tests defined in the project.
- **Explanation**:
  - `mvn test`: This runs the unit tests in the `src/test/java` folder using Maven's testing phase. In this case, no tests were defined, so it returned `No tests to run.`. If there were tests, this stage would ensure that they pass before proceeding.

#### Stage 4: Package the WAR

```groovy
stage('Package') {
    echo 'Packaging the project into a WAR file...'
    sh 'mvn package'
}
```

- **Purpose**: Packages the compiled code into a deployable `.war` file.
- **Explanation**:
  - `mvn package`: Maven packages the compiled Java files and web resources (like `index.jsp` and `WEB-INF`) into a WAR file (`Web Application Archive`) that can be deployed to an application server like Tomcat. The WAR file is created in the `target` directory.

#### Stage 5: Archive WAR (Optional)

```groovy
stage('Archive WAR') {
    echo 'Archiving the WAR file...'
    archiveArtifacts artifacts: '**/target/*.war', allowEmptyArchive: true
}
```

- **Purpose**: Archives the generated WAR file in Jenkins so that it can be accessed later.
- **Explanation**:
  - `archiveArtifacts`: Jenkins stores the WAR file as a build artifact, which can be downloaded from the Jenkins UI or used in further deployment stages.
  - The `allowEmptyArchive` option allows the stage to succeed even if no WAR file was generated (useful for cases where the build might fail or produce no artifacts).

---

### 4. Optional: Archiving Artifacts

If you want to make the WAR file available for download after the build:
- Ensure that the `Archive WAR` stage is included in the pipeline script.
- After the pipeline completes, you can download the WAR file by navigating to the build's page and looking for the **Artifacts** section.

---

### 5. Final Notes

- **Maven Configuration**: Ensure that Maven is installed on your Jenkins node and properly configured. You can check this in **Manage Jenkins** > **Global Tool Configuration**.
- **Testing**: If you want to add unit tests, you can place test files in the `src/test/java` directory. These will automatically be run during the `Run Unit Test` stage.
- **Deployment**: This pipeline focuses on building the project. You can extend it to include deployment stages (e.g., deploying the WAR to a Tomcat server).

---

### Conclusion

This Jenkins pipeline automates the process of building a Java web application, from checking out the code to packaging it into a WAR file. It's a simple setup but serves as a good foundation for more complex workflows, such as adding testing, code quality checks, or deployment steps. The optional steps, like archiving artifacts, can help manage build artifacts in Jenkins for future use. 

Feel free to customize this guide based on your specific requirements.