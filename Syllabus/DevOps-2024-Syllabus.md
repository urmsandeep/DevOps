### LAB CYCLE – AY2024-2025 [ODD]  

**COURSE:** DevOps  
**SECTION:** A, B, C & D  
**COURSE CODE:** 21IS7PEDVR  
**TOTAL CREDITS:** 4  

---

### 1. Docker Basics and Container Management with Python  
Create a Python application, containerize it using Docker, and manage the containers with Python scripts using the Docker SDK for Python.  

- **Objective:** Learn to use Docker and Python for container management.  
- **Exercises:**  
  1. Write a Python application (e.g., Flask app) and create a Dockerfile to containerize it.  
  2. Use the docker-py library to build and run the container programmatically.  
  3. Create a Python script to list, start, and stop Docker containers.  
  4. Automate container health checks with a Python script.  

### 2. Docker Networking with Python  
Set up Docker networking for a multi-container Python application. Use Docker SDK for Python to manage networks.  

- **Objective:** Learn Docker networking using Python for multi-container setups.  
- **Exercises:**  
  1. Develop a Python web application and a database (e.g., SQLite) container.  
  2. Use the docker-py library to create a custom bridge network and connect the containers.  
  3. Write a Python script to inspect and manage Docker networks.  
  4. Implement container communication verification using Python.  

### 3. Docker Security with AppArmor and Python  
Secure your Python application container using AppArmor profiles.  

- **Objective:** Understand how to secure containers with AppArmor using Python for enforcement.  
- **Exercises:**  
  1. Write a basic Python Flask application, containerize it, and secure it using an AppArmor profile.  
  2. Use the Docker SDK for Python to apply and verify AppArmor profiles.  
  3. Test restricted actions in the container with Python scripts.  

### 4. Minikube and Kubernetes with Python  
Set up a Kubernetes single-node cluster using Minikube and deploy Python-based applications.  

- **Objective:** Learn Kubernetes basics using Python applications.  
- **Exercises:**  
  1. Use `kubectl` and a YAML file to deploy a Python Flask app on Minikube.  
  2. Write a Python script to scale the application to multiple pods using subprocess and Kubernetes commands.  
  3. Create and expose a service for the application, programmatically manage the pods using Python.  
  4. Monitor pod status using a Python script and Kubernetes API.  

### 5. Kubernetes Single-Node Setup with Python  
Deploy Python applications in a Kubernetes single-node cluster, using Kubernetes API for programmatic deployment.  

- **Objective:** Automate Kubernetes management with Python.  
- **Exercises:**  
  1. Write a Python script to automate the creation of a single-node Kubernetes cluster using Minikube.  
  2. Deploy a Python app in the cluster using Python’s Kubernetes client.  
  3. Write a Python script to manage deployments, services, and scaling.  
  4. Create an alerting system in Python to monitor pod status and notify on failures.  

### 6. Continuous Integration with Jenkins and Python  
Automate Python application deployment with Jenkins pipelines.  

- **Objective:** Learn how to automate Python app builds and deployments using Jenkins.  
- **Exercises:**  
  1. Set up Jenkins and create a pipeline to build and deploy a Python Flask app.  
  2. Write a Jenkinsfile to automate Docker container creation for the Python app.  
  3. Integrate GitHub for code versioning and automate the pipeline trigger upon commits using Python-based GitHub Actions.  

### 7. Monitoring with Grafana, Prometheus, and Python  
Set up monitoring for a Python-based application running in Kubernetes.  

- **Objective:** Implement monitoring and alerting using Grafana and Prometheus for Python applications.  
- **Exercises:**  
  1. Write a Python app and deploy it on Minikube, set up Prometheus to collect metrics.  
  2. Create custom Prometheus metrics in your Python app using the `prometheus_client` library.  
  3. Install Grafana and set up a dashboard to visualize Python app metrics.  
  4. Set up Python-based alerts for critical metrics and integrate with Grafana.  

### 8. Version Control with GitHub and Python  
Manage Python application code with GitHub for version control.  

- **Objective:** Learn how to manage and collaborate on Python projects using GitHub.  
- **Exercises:**  
  1. Create a Python project and push it to a GitHub repository.  
  2. Automate GitHub repository management using Python’s `PyGithub` library.  
  3. Use GitHub Actions to run automated tests and build your Python app.  
  4. Collaborate with others by creating branches, pull requests, and reviews programmatically using Python.  

### 9. Kafka and Zookeeper Setup with Python  
Set up a Python-based application using Kafka and Zookeeper for message brokering.  

- **Objective:** Learn message brokering and distributed systems using Python, Kafka, and Zookeeper.  
- **Exercises:**  
  1. Install Kafka and Zookeeper in Docker and write a Python producer and consumer application using the `kafka-python` library.  
  2. Write a Python script to manage Kafka topics and partitions programmatically.  
  3. Use Zookeeper for cluster management and implement a simple Python app that connects to multiple Kafka brokers.  
  4. Monitor Kafka performance and manage consumer groups using Python.  

### 10. Docker Volume Attachment for Persistent Storage  
- **Objective:** Learn how to manage persistent storage using Docker volumes.  
- **Exercises:**  
  1. Create a Python Flask application that writes data to a SQLite database.  
  2. Create a Docker volume and attach it to the container to persist the SQLite database.  
  3. Verify that data persists after stopping and restarting the container.  

### 11. Docker Networking with Custom Subnets  
- **Objective:** Learn how to configure Docker networking with custom subnets and demonstrate inter-container communication across different subnets.  
- **Exercises:**  
  1. Create two Python applications: one for a web service and another for a backend service.  
  2. Configure two custom Docker bridge networks with different subnets using the command:  
     ```bash  
     docker network create --subnet=192.168.1.0/24 custom-network-1  
     docker network create --subnet=192.168.2.0/24 custom-network-2  
     ```  
  3. Attach the web service to `custom-network-1` and the backend service to `custom-network-2`.  
  4. Use `docker network connect` to allow inter-container communication and verify using Python scripts.  

### 12. Kubernetes Application Scaling (Login Service Example)  
- **Objective:** Learn how to scale up and scale down applications in Kubernetes.  
- **Exercises:**  
  1. Deploy a Python-based login service using Kubernetes and expose it as a service.  
  2. Scale the service to handle increased traffic using the `kubectl scale` command:  
     ```bash  
     kubectl scale deployment login-service --replicas=5  
     ```  
  3. Use the Kubernetes Python client to automate scaling based on resource utilization (e.g., CPU load).  
  4. Demonstrate automatic scaling with a Horizontal Pod Autoscaler (HPA).  
