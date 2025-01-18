# Secrets in Kubernetes

## Overview

Secrets in Kubernetes are used to store sensitive information, such as passwords, tokens, or keys, in an encoded format. This ensures sensitive data is not exposed in plain text within Pod definition files or other configuration resources. This document explains how to create, manage, and inject secrets in Kubernetes.

---

## Why Use Secrets?

Hardcoding sensitive information directly into application code or Pod definitions is not secure. Kubernetes Secrets offer a secure mechanism to store and manage such data, ensuring:

- **Data Security**: Sensitive information is stored in an encoded format.
- **Centralized Management**: Secrets can be reused across multiple Pods and workloads.
- **Granular Access Control**: RBAC can control which components and users can access secrets.

---

## Creating Secrets

### Imperative Approach

To create a secret directly using the `kubectl` command:

### Using `-from-literal`

This option allows you to specify key-value pairs directly in the command line:

```bash
kubectl create secret generic app-secret \
  --from-literal=db_user=myuser \
  --from-literal=db_password=mypassword

```

### Using `-from-file`

This option allows you to create secrets from a file:

```bash
kubectl create secret generic app-secret \
  --from-file=./db_password.txt

```

The content of `db_password.txt` will be stored as the value for the `db_password` key.

### Declarative Approach

Define a secret in a YAML file. For example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
data:
  db_user: bXl1c2Vy  # Base64 encoded "myuser"
  db_password: bXlwYXNzd29yZA==  # Base64 encoded "mypassword"

```

To apply the YAML file, run:

```bash
kubectl apply -f secret-definition.yaml

```

### Encoding Data

When using the declarative approach, data values must be base64-encoded. Use the following command to encode a value:

```bash
echo -n "myvalue" | base64

```

To decode a base64-encoded value:

```bash
echo -n "bXl2YWx1ZQ==" | base64 --decode

```

---

## Viewing and Managing Secrets

### List Secrets

To list all secrets in the current namespace:

```bash
kubectl get secrets

```

### Describe a Secret

To view metadata and additional details about a secret:

```bash
kubectl describe secret app-secret

```

### View Secret Data

To view the encoded data:

```bash
kubectl get secret app-secret -o yaml

```

To decode the secret values:

```bash
kubectl get secret app-secret -o jsonpath='{.data.db_password}' | base64 --decode

```

---

## Injecting Secrets into Pods

Secrets can be injected into Pods as environment variables or mounted as files.

### Environment Variables

To inject secrets as environment variables, modify the Pod definition file:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app-container
    image: nginx
    env:
    - name: DB_USER
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: db_user
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: db_password

```

Apply the Pod definition:

```bash
kubectl apply -f pod-definition.yaml

```

### Mounted as Files

Secrets can also be mounted as files in a Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secrets"
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: app-secret

```

This will create files in `/etc/secrets`, each containing the respective secret value.

---

## Summary

- Kubernetes Secrets securely store sensitive data such as passwords and keys.
- Secrets can be created using imperative commands or declarative YAML files.
- Sensitive data should always be encoded in base64 when using the declarative approach.
- Secrets can be injected into Pods as environment variables or mounted as files.
