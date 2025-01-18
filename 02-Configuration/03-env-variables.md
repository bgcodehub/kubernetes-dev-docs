# Setting Environment Variables in Kubernetes

## Overview
Environment variables are a convenient way to pass configuration data to containers running in Kubernetes Pods. They can be set directly in the Pod definition or dynamically using ConfigMaps and Secrets for better scalability and security.

This document covers the basics of setting environment variables in Kubernetes using the `env` property, as well as introducing advanced methods with `valueFrom`.

---

## Setting Environment Variables Using the `env` Property

### Key Concepts
- The `env` property in the Pod definition is an array.
- Each environment variable is defined as an object with `name` and `value` fields.

### Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: nginx
    env:
    - name: ENV_VAR_NAME
      value: "ENV_VAR_VALUE"
    - name: ANOTHER_VAR
      value: "ANOTHER_VALUE"
```
Explanation:
- **`name`**: The name of the environment variable.
- **`value`**: The value assigned to the variable.

When this Pod runs, the container will have access to `ENV_VAR_NAME` and `ANOTHER_VAR` as environment variables with their respective values.

---

## Using ConfigMaps and Secrets for Environment Variables

Environment variables can also be set dynamically using Kubernetes objects like ConfigMaps and Secrets. This approach separates configuration data from application code.

### Using `valueFrom` with ConfigMaps
Instead of hardcoding the values, you can reference a ConfigMap:

#### Example ConfigMap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-configmap
data:
  ENV_VAR_NAME: "configmap-value"
```

#### Pod Definition
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-using-configmap
spec:
  containers:
  - name: example-container
    image: nginx
    env:
    - name: ENV_VAR_NAME
      valueFrom:
        configMapKeyRef:
          name: example-configmap
          key: ENV_VAR_NAME
```
Explanation:
- **`valueFrom.configMapKeyRef`**: References the `example-configmap` ConfigMap.
- **`key`**: Specifies the key in the ConfigMap (`ENV_VAR_NAME`) whose value will be used.

When this Pod runs, the `ENV_VAR_NAME` variable will have the value `configmap-value`.

### Using Secrets for Sensitive Data
For sensitive data like passwords, use Kubernetes Secrets:

#### Example Secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: example-secret
data:
  PASSWORD: c2VjcmV0LXZhbHVl
```
> **Note**: Secret values must be base64-encoded.

#### Pod Definition
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-using-secret
spec:
  containers:
  - name: example-container
    image: nginx
    env:
    - name: PASSWORD
      valueFrom:
        secretKeyRef:
          name: example-secret
          key: PASSWORD
```
Explanation:
- **`valueFrom.secretKeyRef`**: References the `example-secret` Secret.
- **`key`**: Specifies the key in the Secret (`PASSWORD`) whose value will be used.

When this Pod runs, the `PASSWORD` environment variable will be set to the decoded value of the Secret.

---

## Summary
- Environment variables can be set directly in the Pod definition using the `env` property.
- For dynamic and secure configurations, use ConfigMaps and Secrets with `valueFrom`.
- ConfigMaps are suitable for general configuration data, while Secrets are designed for sensitive information.
