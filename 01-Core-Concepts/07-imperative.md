# Practice with Imperative Commands in Kubernetes

## Overview
Imperative commands in Kubernetes are used to create and manage resources directly from the command line. These commands are particularly helpful for quick tasks, exploratory testing, or preparation for certification exams. This guide provides examples of common imperative commands and their use cases.

---

## Creating Pods with Imperative Commands

### Example 1: Create a Pod with `nginx`
Command:
```bash
kubectl run nginx-pod --image=nginx:alpine
```
Explanation:
- **`kubectl run`**: Creates a new Pod.
- **`nginx-pod`**: Name of the Pod.
- **`--image=nginx:alpine`**: Specifies the container image.

### Example 2: Create a Redis Pod with Labels
Command:
```bash
kubectl run redis --image=redis:alpine --labels=tier=db
```
Explanation:
- **`--labels=tier=db`**: Assigns a label to the Pod for easier management and filtering.

---

## Creating Services with Imperative Commands

### Example 1: Create a ClusterIP Service for Redis
Command:
```bash
kubectl expose pod redis --type=ClusterIP --name=redis-service --port=6379
```
Explanation:
- **`kubectl expose`**: Exposes a resource (Pod, Deployment, etc.) as a service.
- **`--type=ClusterIP`**: Default service type for internal cluster communication.
- **`--name=redis-service`**: Specifies the name of the service.
- **`--port=6379`**: Port for the Redis application.

### Example 2: NodePort Service
To create a NodePort service, modify the YAML file created by `kubectl expose`, or use the `kubectl create service` command with additional options.

---

## Managing Deployments with Imperative Commands

### Example 1: Create a Deployment with `webapp`
Command:
```bash
kubectl create deployment webapp --image=nginx --replicas=3
```
Explanation:
- **`kubectl create deployment`**: Creates a new Deployment.
- **`--image=nginx`**: Specifies the container image.
- **`--replicas=3`**: Number of desired Pod replicas.

### Example 2: Expose the Deployment
Command:
```bash
kubectl expose deployment webapp --port=8080
```
Explanation:
- **`kubectl expose deployment`**: Creates a service for the Deployment.
- **`--port=8080`**: Specifies the port to expose.

---

## Creating Namespaces and Assigning Resources

### Example 1: Create a Namespace
Command:
```bash
kubectl create namespace dev-ns
```
Explanation:
- **`create namespace`**: Creates a new Namespace named `dev-ns`.

### Example 2: Assign Resources to a Namespace
Command:
```bash
kubectl create deployment redis-deploy --image=redis --replicas=2 --namespace=dev-ns
```
Explanation:
- **`--namespace=dev-ns`**: Assigns the resource to the specified Namespace.

---

## Combining Pod Creation and Exposure

### Example: Create a Pod and Expose It as a Service
Command:
```bash
kubectl run httpd --image=httpd:alpine --port=80 --expose
```
Explanation:
- **`--port=80`**: Specifies the container port.
- **`--expose`**: Automatically creates a ClusterIP service for the Pod.

---

## Common Commands Summary

1. **Creating Pods**:
   ```bash
   kubectl run <pod-name> --image=<image> [--labels=<key=value>]
   ```
2. **Creating Services**:
   ```bash
   kubectl expose <resource-type> <resource-name> --type=<service-type> --name=<service-name> --port=<port>
   ```
3. **Creating Deployments**:
   ```bash
   kubectl create deployment <deployment-name> --image=<image> --replicas=<number>
   ```
4. **Creating Namespaces**:
   ```bash
   kubectl create namespace <namespace-name>
   ```
5. **Switching Contexts**:
   ```bash
   kubectl config set-context --current --namespace=<namespace>
   ```

---

## Conclusion
Imperative commands are a powerful way to quickly create and manage Kubernetes resources. While they are ideal for testing and quick tasks, declarative YAML files are recommended for production environments to ensure consistency and reproducibility.

