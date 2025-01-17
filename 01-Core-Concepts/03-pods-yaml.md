# Creating a Pod Using a YAML Configuration File

## Overview

Kubernetes uses YAML configuration files to define and manage objects such as Pods, replicas, and services. These files follow a structured format with four required top-level fields: `apiVersion`, `kind`, `metadata`, and `spec`. This document explains the structure of these fields and provides examples for creating Pods using YAML.

---

## YAML Structure for Kubernetes Objects

### 1. `apiVersion`

- Specifies the API version of Kubernetes being used to create the object.
- Example values:
    - `apps/v1` (for most workloads)
    - `extensions/v1beta1` (deprecated, but used for older objects)

For creating a Pod, use:

```yaml
apiVersion: v1

```

### 2. `kind`

- Defines the type of Kubernetes object being created.
- Example values:
    - `Pod`
    - `ReplicaSet`
    - `Deployment`

For creating a Pod, use:

```yaml
kind: Pod

```

### 3. `metadata`

- Contains metadata about the object, such as:
    - `name`: A unique name for the object.
    - `labels`: Key-value pairs to categorize and identify objects.

Example:

```yaml
metadata:
  name: my-app-pod
  labels:
    app: my-app
    environment: production

```

### 4. `spec`

- Provides the specifications for the object, such as:
    - Containers to run.
    - Image to use.
    - Ports to expose.

Example:

```yaml
spec:
  containers:
  - name: my-app-container
    image: nginx
    ports:
    - containerPort: 80

```

---

## Complete Example: Pod YAML Definition

Here is an example YAML file for creating a simple Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    environment: production
spec:
  containers:
  - name: my-app-container
    image: nginx
    ports:
    - containerPort: 80

```

### Explanation:

1. **`apiVersion`**: Specifies the Kubernetes API version.
2. **`kind`**: Indicates the object type (Pod).
3. **`metadata`**:
    - `name`: Assigns the Pod a unique name (`my-app-pod`).
    - `labels`: Helps group and identify objects (e.g., `app: my-app`).
4. **`spec`**:
    - Defines the container configuration.
    - Specifies the image (`nginx`) and the exposed port (`80`).

---

## Creating a Pod Using `kubectl`

Once the YAML file is created, use the `kubectl` command to deploy the Pod:

1. Save the file as `pod-definition.yaml`.
2. Apply the configuration:
    
    ```bash
    kubectl apply -f pod-definition.yaml
    
    ```
    
3. Verify the Pod status:

Output:
    
    ```bash
    kubectl get pods
    
    ```
    
    ```
    NAME          READY   STATUS    RESTARTS   AGE
    my-app-pod    1/1     Running   0          5s
    
    ```
    
4. View detailed information about the Pod:
    
    ```bash
    kubectl describe pod my-app-pod
    
    ```
    

---

## Summary

- Kubernetes YAML files provide a structured way to define and manage objects.
- A Pod configuration includes `apiVersion`, `kind`, `metadata`, and `spec` fields.
- Use `kubectl apply -f <filename>` to create objects from YAML files.