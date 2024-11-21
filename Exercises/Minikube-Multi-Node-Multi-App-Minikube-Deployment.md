
# Multi-Node Kubernetes Cluster with Multiple Applications and Replicasets

## Storyboard

Let's consider developing an e-commerce platform with the product requirements:
1. **Product Catalog** (AppA) - Provides information about available products.
2. **Shopping Cart** (AppB) - Manages user shopping carts.

To ensure availability and fault tolerance, the Product Catalog will have 2 replicas and the Shopping Cart will have 3 replicas. 
Each replica of both services should be distributed across multiple nodes to handle traffic and provide redundancy.

## Use Case
An online store needs high availability for critical services like product browsing and cart management. 
To avoid service disruptions, we deploy each service with replicas distributed across multiple nodes. 
This setup ensures that even if one node fails, the services remain available for customers.

---

### Step 1: Set Up Minikube with Multiple Nodes

To create a multi-node environment in Minikube, use the following command:

```bash
minikube start --nodes 3 -p devops-multinode
```

This command sets up Minikube with three nodes in a profile named "devops-multinode."

---

### Step 2: Product Catalog (AppA) - Python Code

Create a simple Flask application for the **Product Catalog** service.

```python
# product_catalog.py
from flask import Flask, jsonify

app = Flask(__name__)

products = [
    {"id": 1, "name": "Laptop", "price": 1200},
    {"id": 2, "name": "Phone", "price": 800},
    {"id": 3, "name": "Headphones", "price": 150},
]

@app.route("/products", methods=["GET"])
def get_products():
    return jsonify(products)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=80)
```

Save this file as `product_catalog.py`. This code provides an endpoint to list available products.

---

### Step 3: Shopping Cart (AppB) - Python Code

Create a simple Flask application for the **Shopping Cart** service.

```python
# shopping_cart.py
from flask import Flask, jsonify, request

app = Flask(__name__)

cart = []

@app.route("/cart", methods=["GET"])
def get_cart():
    return jsonify(cart)

@app.route("/cart", methods=["POST"])
def add_to_cart():
    item = request.json
    cart.append(item)
    return jsonify(cart), 201

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=80)
```

Save this file as `shopping_cart.py`. This code provides endpoints to view and add items to the shopping cart.

---

### Step 4: Dockerize Applications

Create Dockerfiles for both applications.

**Product Catalog Dockerfile**

```dockerfile
# Dockerfile for Product Catalog
FROM python:3.9-slim
WORKDIR /app
COPY product_catalog.py /app/
RUN pip install flask
CMD ["python", "product_catalog.py"]
```

**Shopping Cart Dockerfile**

```dockerfile
# Dockerfile for Shopping Cart
FROM python:3.9-slim
WORKDIR /app
COPY shopping_cart.py /app/
RUN pip install flask
CMD ["python", "shopping_cart.py"]
```

Build the Docker images:

```bash
docker build -t product-catalog:latest -f Dockerfile.product .
docker build -t shopping-cart:latest -f Dockerfile.shopping .
```

---

### Step 5: Deploy Applications on Minikube

Use Kubernetes manifests to deploy the Product Catalog and Shopping Cart services.

**Product Catalog Deployment (ReplicaSet of 2)**

```yaml
# product_catalog_deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-catalog
  namespace: devops-exercise
spec:
  replicas: 2
  selector:
    matchLabels:
      app: product-catalog
  template:
    metadata:
      labels:
        app: product-catalog
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchLabels:
                app: product-catalog
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: product-catalog-container
        image: product-catalog:latest
        ports:
        - containerPort: 80
```

**Shopping Cart Deployment (ReplicaSet of 3)**

```yaml
# shopping_cart_deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: shopping-cart
  namespace: devops-exercise
spec:
  replicas: 3
  selector:
    matchLabels:
      app: shopping-cart
  template:
    metadata:
      labels:
        app: shopping-cart
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchLabels:
                app: shopping-cart
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: shopping-cart-container
        image: shopping-cart:latest
        ports:
        - containerPort: 80
```

Apply the configurations:

```bash
kubectl apply -f product_catalog_deployment.yaml
kubectl apply -f shopping_cart_deployment.yaml
```

---

### Step 6: Expose the Services

Expose both applications to make them accessible.

```yaml
# product_catalog_service.yaml
apiVersion: v1
kind: Service
metadata:
  name: product-catalog-service
  namespace: devops-exercise
spec:
  selector:
    app: product-catalog
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

```yaml
# shopping_cart_service.yaml
apiVersion: v1
kind: Service
metadata:
  name: shopping-cart-service
  namespace: devops-exercise
spec:
  selector:
    app: shopping-cart
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

Apply the service configurations:

```bash
kubectl apply -f product_catalog_service.yaml
kubectl apply -f shopping_cart_service.yaml
```

---

### Step 7: Verify Deployment and Distribution

List the pods and check their node assignment to ensure they are distributed as expected:

```bash
kubectl get pods -o wide -n devops-exercise
```

Each pod for Product Catalog and Shopping Cart should be running on different nodes.

### Step 8: Access the Services

Use Minikube’s service forwarding to access the applications:

```bash
minikube service product-catalog-service -n devops-exercise
minikube service shopping-cart-service -n devops-exercise
```

These commands open the services in our default web browser or display URLs to access each service.

---

### Step 9: Testing with `curl`

After deploying the services, we can test their functionality using `curl` commands.

#### Test Product Catalog Service

1. **Retrieve Product List**: Test if the Product Catalog service returns the list of products.

   ```bash
   curl -X GET http://<minikube_ip>:<node_port>/products
   ```

   Expected response:
   ```json
   [
       {"id": 1, "name": "Laptop", "price": 1200},
       {"id": 2, "name": "Phone", "price": 800},
       {"id": 3, "name": "Headphones", "price": 150}
   ]
   ```

#### Test Shopping Cart Service

1. **Retrieve Cart**: Verify that the Shopping Cart service returns an empty cart initially.

   ```bash
   curl -X GET http://<minikube_ip>:<node_port>/cart
   ```

   Expected response:
   ```json
   []
   ```

2. **Add Item to Cart**: Test adding an item to the cart by sending a `POST` request with item data.

   ```bash
   curl -X POST http://<minikube_ip>:<node_port>/cart -H "Content-Type: application/json" -d '{"id": 1, "name": "Laptop", "quantity": 1}'
   ```

   Expected response:
   ```json
   [
       {"id": 1, "name": "Laptop", "quantity": 1}
   ]
   ```

Replace `<minikube_ip>` and `<node_port>` with the IP and port for each respective service.

---

## Summary
In this exercise, we deployed two Python applications (Product Catalog and Shopping Cart) on a multi-node Minikube cluster
with ReplicaSets, ensuring each pod is running on a different node. 
This setup maximizes availability and fault tolerance across the cluster.
