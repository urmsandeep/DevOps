# Docker Security with AppArmor and Python

## Objective

The goal of this exercise is to understand how to secure Docker containers using AppArmor profiles with Python for enforcement. 
You will also learn how to apply AppArmor profiles using the Docker SDK for Python and test restricted actions within the container.

## Scenario

You are responsible for deploying a Python-based web application using Docker. 
The application is running inside a Docker container.  You need to secure it to restrict access to sensitive directories 
and prevent unauthorized actions such as executing binaries or accessing restricted files.

The Python Flask application is containerized, and Docker's SDK will be used to apply and verify 
the AppArmor profiles. Finally, you will test restricted actions in the container using Python scripts.

## Tasks


### Task 1: Write a Basic Python Flask Application
Create a simple Python Flask application (web-server.py) that will be containerized.

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, this is a secure Flask application running inside a Docker container!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### Task 2: Containerize the Flask Application Using a Dockerfile

Create a *Dockerfile* to containerize the Flask application.

```
# Use an official Python runtime as a parent image
FROM python:3.8-slim

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install Flask
RUN pip install flask

# Expose port 5000
EXPOSE 5000

# Run app.py when the container launches
CMD ["python", "app.py"]

```

### Task 3: Create and Apply AppArmor Profile

Create an AppArmor profile that restricts access to sensitive directories and prevents the execution of certain binaries.

```
#include <tunables/global>

/usr/bin/python3 {
    # Deny access to sensitive system files
    deny /etc/** r,
    deny /var/** rw,

    # Allow Flask app to bind to port 5000
    network inet stream,

    # Permissions to the application directory
    /app/** rwk,

    # Deny execution of any binaries in /bin or /usr/bin
    deny /bin/** rmix,
    deny /usr/bin/** rmix,

    # Capability restrictions
    capability net_bind_service,
    deny capability sys_admin,
}

```
Apply the AppArmor profile when starting the container:

```
docker run --security-opt="apparmor=my-apparmor-profile" -p 5000:5000 web-server
```

### Task 4: Use Docker SDK for Python to Apply AppArmor Profile
Write a Python script using the Docker SDK to apply the AppArmor profile to the Flask container.

Python Script (apply_apparmor.py):

```
import docker

# Create a Docker client
client = docker.from_env()

# Build the Docker image
client.images.build(path=".", tag="flask-apparmor")

# Run the container with the AppArmor profile
container = client.containers.run(
    "flask-apparmor",
    ports={'5000/tcp': 5000},
    security_opt=["apparmor=my-apparmor-profile"],
    detach=True
)

print(f"Container started: {container.short_id}")

# Verify AppArmor profile applied
container_info = client.api.inspect_container(container.id)
apparmor_profile = container_info['HostConfig']['SecurityOpt']

print(f"AppArmor profile applied: {apparmor_profile}")

# Stop the container
container.stop()
```

### Task 5: Test Restricted Actions

Write a Python script to test restricted actions such as accessing /etc/passwd or executing /bin/bash in the container.

Python Script (test_restricted_actions.py):

```
import docker

# Create a Docker client
client = docker.from_env()

# Run the container with the AppArmor profile
container = client.containers.run(
    "flask-apparmor",
    ports={'5000/tcp': 5000},
    security_opt=["apparmor=my-apparmor-profile"],
    detach=True
)

# Test restricted actions
exit_code, output = container.exec_run("cat /etc/passwd")
print(f"Attempt to read /etc/passwd: Exit Code {exit_code}, Output: {output.decode()}")

exit_code, output = container.exec_run("/bin/bash")
print(f"Attempt to execute /bin/bash: Exit Code {exit_code}, Output: {output.decode()}")

# Stop the container
container.stop()
```

### Questions:

1. What is the purpose of using AppArmor with Docker containers?
2. How do AppArmor profiles help secure a Docker container?
3. Why is it important to restrict access to sensitive directories such as /etc/ and /var/?
4. What other capabilities can you restrict using AppArmor profiles?
5. How can you verify if an AppArmor profile is successfully applied to a Docker container?

### Answers:

1. The --net flag specifies the network to connect the container to.
2. Containers communicate using their container names or IP addresses.
3. A bridge network provides isolation between containers, while a host network shares the host's network stack. Think of it like:
- Bridge Network: Containers are in a private room, talking to each other through a door.
- Host Network: Containers are in the same room as the host, talking directly to everyone.
4. Use the -p flag to expose a container's port (e.g., `-p 500")
