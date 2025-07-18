# Postman CLI & Jenkins CI/CD Integration Guide

This document outlines the steps to integrate Postman CLI with Jenkins for automated API testing.

## 1. Postman CLI Setup

### 1.1 Install Postman CLI

   - Follow the official installation guide: [https://learning.postman.com/docs/postman-cli/postman-cli-installation/#windows-installation](https://learning.postman.com/docs/postman-cli/postman-cli-installation/#windows-installation)

### 1.2 Generate Postman API Key

   - Generate a Postman API key as described here: [https://learning.postman.com/docs/developer/postman-api/authentication/#generate-a-postman-api-key](https://learning.postman.com/docs/developer/postman-api/authentication/#generate-a-postman-api-key)

### 1.3 Run Postman Collection using Postman CLI

   1.  **Login to Postman:**
       ```bash
       postman login --with-api-key {Place Your API-Key Here}
       ```
   2.  **Run the Collection:**
        ```bash
        postman collection run {Place Your Collection ID Here} -d {Place Your Data File Here} -g {Place Your Global Variables Here (If Any)} -r html
       ```

## 2. Jenkins CI/CD Pipeline Configuration

### 2.1 Run Collection From Postman

   1. Select "Automate runs via CLI" within your Postman collection.
   2. Click "Configure command" under "Run on CI/CD".

### 2.2 Generate Postman CLI Configuration

   1. **Collection:** Select the collection you need to run.
   2. **CI/CD Provider:** Select "Jenkins".
   3. **Operating System for CI/CD:** Select "your operating system".

### 2.3 Copy the Postman CLI Command

   - After configuration, copy the generated Postman CLI command.

### 2.4 Run Jenkins on your Local Machine

   1. Open the command prompt/terminal in the directory where your `jenkins.war` file is located.
   2. Execute the following command:
      ```bash
      java -jar jenkins.war --enable-future-java
      ```
      *Note: Keep this command prompt/terminal open during Jenkins usage.*
   3. Open your web browser and access Jenkins using this URL:
      [http://localhost:8080/](http://localhost:8080/)

### 2.5 Create a New Job

   1. In the Jenkins dashboard, select "New Item".
   2. Select "Pipeline".

### 2.6 Adding Local NodeJs Installation to Jenkins

   1. Go to Jenkins "Dashboard" -> "Manage Jenkins" -> "Tools".
   2. Scroll down to the "NodeJS installations" section.
   3. Add a new NodeJs installation.
   4. Uncheck "Install automatically".
   5. Provide a descriptive name (This name will be used in your Jenkins script).
   6. Specify the local Node.js installation directory, e.g.,
      ```
       C:\Program Files\nodejs
      ```
       *Note: If you don't have Node.js installed, you will need to install it first or select the "Install automatically" option to let Jenkins handle installation.*

### 2.7 Paste the Command into the Pipeline Script

   1.  In your pipeline configuration, go to the "Pipeline" section and add a script.
   2.  Paste the Postman CLI command you copied in step 2.3 into the script.
   3.  Apply and Save the pipeline.
   4.  **Modify the Script**:
        -   Replace the nodeJs variable with the name of the NodeJs installation you added in Jenkins (step 2.6).
        -   Replace the placeholder API Key with your actual Postman API key in the "Postman CLI Login" stage.
        -   Adjust the script in the "Running Collection" stage as necessary, for example to add environment variables.
    5.  Apply and Save.

   **Example Jenkins Pipeline Script (Adjust as needed):**
   ```groovy
pipeline {
  agent any

  tools {nodejs "myNodeJS"}

  stages {
    stage('Install Postman CLI') {
      steps {
        bat 'powershell.exe -NoProfile -InputFormat None -ExecutionPolicy AllSigned -Command "[System.Net.ServicePointManager]::SecurityProtocol = 3072; iex ((New-Object System.Net.WebClient).DownloadString(\'https://dl-cli.pstmn.io/install/win64.ps1\'))"'
      }
    }

    stage('Postman CLI Login') {
      steps {
        bat 'postman login --with-api-key {Place Your API-Key Here}'
      }
    }

    stage('Running collection') {
      steps {
        bat 'postman collection run {Place Your Collection ID Here} -d {Place Your Data File Here} -g {Place Your Global Variables Here (If Any)} -r html '
      }
    }
  }
}