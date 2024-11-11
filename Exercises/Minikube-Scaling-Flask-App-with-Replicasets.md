# Exercise: Scaling Flask App on Single Node using Replicasets

## Objective:
- Understand ReplicaSets and Pods
- Scale Flask App deployment
- Observe pod distribution

## Step 1: Clean up previous minikube does

If you already have a running cluster, you'll need to stop and delete it before starting a new one

```
minikube stop
minikube delete
```
## Step 2: Start minikube with a single node

```
minikube start --nodes=1
```

## Step 3: Create a new file replicaset.yaml with the following content:

File Name: replicaset.yaml 
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: flask-app-rs
spec:
  replicas: 3
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
        image: flask-app:latest
        imagePullPolicy: Never
        ports:
        - containerPort: 15000
```

## Step 4: Apply the ReplicaSet configuration:

```
kubectl apply -f replicaset.yaml
```

## Step 5: Verify the ReplicaSet:

```
kubectl get rs
```

## Step 6: Verify the pods:

```
kubectl get pods
```

## Step 7: Scale the ReplicaSet to 5 replicas:

```
kubectl scale rs flask-app-rs --replicas=5
```

## Step 8: Verify the updated ReplicaSet:

```
kubectl get rs
```

## Step 9: Verify the updated pods:

```
kubectl get pods
```

## Step 10: Delete one pod:

```
kubectl delete pod <pod-name>
```

## Step 11: Verify the pods:

```
kubectl get pods
```



Additional Challenges:

- Update the replicaset.yaml file to use a different image.
- Create a Deployment instead of a ReplicaSet.
- Use kubectl describe to inspect the ReplicaSet and pods.

Tips and Variations:

- Use kubectl get pods -o wide to see pod distribution across nodes.
- Use kubectl logs to view pod logs.
- Use kubectl exec to access a pod's container.
