# Creating "Hello World" Jenkins Job

## Pre-requisite: Ensure Jenkins is running
Refer to this page:

https://github.com/urmsandeep/DevOps/blob/main/Exercises/Jenkins-CI-Automation.md#getting-started-with-jenkins

Once you have Jenkins running, follow these steps to create and configure first Jenkins job in Jenkins.

---

## Step 1: Access Jenkins
1. Open your browser and navigate to the Jenkins URL (e.g., `http://localhost:8080`).
2. Log in using your credentials.

---

## Step 2: Create a New Job
1. On the Jenkins dashboard, click **"New Item"** in the left-hand menu.
2. Enter a name for your job (e.g., `HelloWorld`).
3. Select **Freestyle project**.
4. Click **OK** to proceed.

---

## Step 3: Configure the Job

### General Settings
1. Add a description for the job:
   ```text
   Hello World! Jenkins job.

### Source Code Management
Select Git under Source Code Management.
Enter the repository URL: https://github.com/<your-username>/repo.git

### Build Triggers
To schedule builds, enable "Build periodically" and use cron syntax. Example cron syntax:
H * * * *
This will trigger the job once every hour.

### Build Steps
Under Build, click Add build step → Execute shell.
Add the following shell script
```
echo "Hello, Jenkins!"
```

### Step 4: Save and Run the Job
Click Save to save your job configuration.
On the job page, click Build Now to run the job.

#### Step 5: View the Build Output
Navigate to the Build History section on the left-hand side of the job page.
Click the build number (e.g., #1).
Click Console Output to see the build logs.

```
Started by user Admin
Building in workspace /var/jenkins_home/workspace/HelloWorld
[HelloWorld] $ /bin/sh -xe /tmp/jenkins1281930102.sh
+ echo 'Hello, Jenkins!'
Hello, Jenkins!
Finished: SUCCESS
```
