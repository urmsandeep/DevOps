# Jenkins Multi-Stage Pipeline Exercise: Deploying a Python Application

## Objective
In this exercise, we will create and execute a **Jenkins Pipeline** that performs multiple stages: **Build**, **Test**, and **Deploy** 
for a sample Python application. This will help us understand how to implement and manage multi-stage CI/CD pipelines in Jenkins.

---

## Scenario
You are tasked with automating the CI/CD pipeline for a Python Flask application. The pipeline should perform the following stages:
1. **Build**: Install dependencies.
2. **Test**: Run unit tests to ensure the application works as expected.
3. **Deploy**: Deploy the application by copying it to a mock deployment directory.

---

## Steps to Complete the Exercise

### Step 1: Set Up Jenkins
1. Install Jenkins and ensure it is running on your local machine or a server.
2. Install the following plugins:
   - **Pipeline Plugin** (if not already installed).

---

### Step 2: Create a Sample Python Application
1. Create a directory for the application:
   ```
   mkdir python-flask-app
   cd python-flask-app

2. Create the following files:
file: app.py
```
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, Jenkins Multi-Stage Pipeline!"

if __name__ == "__main__":
    app.run(debug=True)
```
file: requirements.txt:
```
flask==2.1.2
```
file: test_app.py
```
import unittest
from app import app

class TestApp(unittest.TestCase):
    def test_home(self):
        tester = app.test_client()
        response = tester.get("/")
        self.assertEqual(response.status_code, 200)
        self.assertEqual(response.data.decode("utf-8"), "Hello, Jenkins Multi-Stage Pipeline!")

if __name__ == "__main__":
    unittest.main()
```
### Step 3: Push the Application to a Git Repository
Push the repository to your GitHub.
```
git init
git add .
git commit -m "Initial commit"
```

### Step 4: Create a Multistage Jenkins Pipeline
Go to your Jenkins dashboard and click "New Item".
Enter a name (e.g., Python-MultiStage-Pipeline) and select Pipeline.
Click OK.

### Step 5: Define the Jenkinsfile
In the project directory, create a file named Jenkinsfile with the following content:

```
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Installing dependencies...'
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'python -m unittest discover -s .'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh '''
                mkdir -p /tmp/python-app-deploy
                cp app.py /tmp/python-app-deploy/
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
    }
}
```
Push the Jenkinsfile to your Git repository:

```
git add Jenkinsfile
git commit -m "Added Jenkinsfile"
git push
```

### Step 6: Configure the Jenkins Job
In Jenkins, go to the pipeline configuration:
Under Pipeline, select Pipeline script from SCM.
Choose Git as the SCM and enter your repository URL.
Add credentials if necessary.
Save the configuration.

### Step 7: Run the Pipeline
Click Build Now to start the pipeline.
Observe the stages (Build, Test, Deploy) as they execute.
Check the console output for logs and results.

### Expected Outcome
Build Stage:
- The pip install -r requirements.txt command installs Flask and other dependencies.
Test Stage:
- The unittest framework runs the test cases and ensures they pass.
Deploy Stage:
- The application file (app.py) is copied to /tmp/python-app-deploy.

### Additional Enhancements
Add a Code Quality Analysis stage using tools like flake8 or pylint.
```
stage('Code Quality') {
    steps {
        echo 'Running code quality checks...'
        sh 'flake8 .'
    }
}
```
Deploy the application to a Docker container as part of the pipeline.
Integrate notifications (e.g., email or Slack) for pipeline success or failure.


