# Exercise: Deploy a Flask app on Minikube using kubectl and yaml

## Objective
Learn Kubernetes basics using Minikube to set up a single-node cluster and deploy Python applications. 
This exercise involves deploying a Flask app using minikube.

## Prerequisites
- The Illustrated **Children’s Guide to Kubernetes**
https://www.cncf.io/phippy/the-childrens-illustrated-guide-to-kubernetes/
- Video: https://www.youtube.com/watch?v=3I9PkvZ80BQ
- Install Minikube https://minikube.sigs.k8s.io/docs/
- Follow the Minikube installation guide for your operating system (Windows/Linux/MacOS).
- **Watch:** Origins of Kubernetes with Tim Hockin of Google https://www.youtube.com/watch?v=xSztxKexDXM
- **Watch:** Keynote: A Vision for Vision (in the era of AI) - Kubernetes in Its Second Decade - Tim Hockin https://www.youtube.com/watch?v=WqeShpaztZY

## Minikube cheat sheet - frequently used commands
```
# 1. Starting and Stopping Minikube
minikube start                            # Start Minikube with default settings
minikube start --kubernetes-version=v1.21.2  # Start with a specific Kubernetes version
minikube stop                             # Stop Minikube without deleting
minikube delete                           # Delete Minikube cluster

# 2. Checking Status and Information
minikube status                           # Check Minikube status
kubectl cluster-info                      # Display cluster information

# 3. Accessing Services
minikube service <service-name>           # Open specified service in browser
minikube service list                     # List URLs of all services
minikube tunnel                           # Create a network tunnel to access LoadBalancer services

# 4. Docker with Minikube
eval $(minikube docker-env)               # Use Minikube's Docker daemon
docker build -t my-image .                # Build image directly in Minikube
minikube image load my-image              # Load a local image into Minikube

# 5. Debugging and Logs
minikube logs                             # View Minikube logs
minikube dashboard                        # Open the Kubernetes dashboard
minikube ssh                              # SSH into the Minikube VM
```

## Step 1: Start minkube
```
minikube start
```

## Step 2: Create a Flask Application
File Name: app.py
```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Flask on Kubernetes!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

## Step 3: Create a Dockerfile for Flask Application:
File Name: Dockerfile
```
FROM python:3.8-slim
WORKDIR /app
COPY . /app
RUN pip install flask
EXPOSE 5000
CMD ["python", "app.py"]
```

## Step 4: Build the Docker Image with Minikube’s Docker Daemon:
Run the following comamnd to display what variable would be set. 
```
minikube docker-env
```
This set of variable export statements 
will configure your (operating systems's) Docker CLI to use Minikube’s Docker daemon. 
```
eval $(minikube docker-env)
docker build -t flask-app .
```

## Step 5: Create a Kubernetes Deployment YAML File:
File Name: flask-deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
      - name: flask-app
        image: flask-app
        ports:
        - containerPort: 5000
   ```

## Step 6: Deploy the application
Use kubectl to apply the YAML file and deploy the Flask application:

```
kubectl apply -f flask-deployment.yaml
```
## Step 7: Check Deployment Status
To verify that the deployment was created successfully and check the number of replicas:
```
kubectl get deployments
```
Output: You should see your flask-app deployment with its current status and number of replicas.
```
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
flask-app    1/1     1            1           5s
```
## Step 8: Verify Pods Created by the Deployment
To check if the deployment created the expected pods:
```
kubectl get pods -l app=flask-app
```
Output: The output will list the pods created by the flask-app deployment. Ensure the STATUS shows Running.

```
NAME                          READY   STATUS    RESTARTS   AGE
flask-app-3443a-213a          1/1     Running   0          5s
```
## Step 9: Describe the Deployment
This command provides detailed information about the deployment, 
including events, number of replicas, and any issues during deployment.
```
kubectl describe deployment flask-app
```
## Step 10: View Deployment Logs
To see the logs of the Flask application (useful to verify if the app is running as expected):

```
kubectl get pods -l app=flask-app
```
Use the following command to view logs:

```
kubectl logs flask-app-3443a-213a
```
The logs should show that Flask is running and listening on the specified port, 
indicating that the application is successfully deployed.

## Step 11: Check Services
This will show the flask-service if created, with details like the service type 
(NodePort), PORT, and any external IPs if applicable.
```
kubectl get services
```
# Q&A based on the exercise   
**Q1: What command is used to expose a service in Kubernetes?**   
**A1:** kubectl expose or kubectl apply -f <service.yaml> is used to expose a service in Kubernetes.

**Q2: How does Minikube help in local Kubernetes testing?**  
**A2:** Minikube runs a single-node Kubernetes cluster locally, making it easier to test deployments without needing a full Kubernetes environment.

**Q3: What is the role of kubectl in this setup?**
**A3:** kubectl is the Kubernetes CLI tool used to interact with the Kubernetes cluster. It allows you to deploy applications, scale them, and manage resources.

