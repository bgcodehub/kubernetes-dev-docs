# Kubernetes Secrets: Managing Sensitive Information

## Introduction

This guide explores Kubernetes Secrets, a tool for securely storing sensitive information such as passwords, API keys, and tokens. Secrets ensure safe access to sensitive data by encoding it, providing enhanced security in Kubernetes environments. Here, we will discuss how to create, manage, and use Kubernetes Secrets effectively.

---

## What Are Kubernetes Secrets?

- Kubernetes Secrets securely store sensitive data.
- Unlike ConfigMaps, Secrets encode the data to reduce exposure risks.
- Two methods to create Secrets:
  1. **Imperative**: Directly creating Secrets via commands.
  2. **Declarative**: Defining Secrets using YAML files.

---

## Creating Secrets Imperatively

To create a Secret imperatively, use the `kubectl create secret` command.

### Example:
s
Creating a Secret with key-value pairs:

```bash
kubectl create secret generic app-secret \
--from-literal=db_user=admin \
--from-literal=db_password=securepassword
```

This creates a Secret named `app-secret` containing two key-value pairs.

### Viewing Secrets:

To list all Secrets:

```bash
kubectl get secrets
```

To describe a specific Secret:

```bash
kubectl describe secret app-secret
```

### Important Note:

Secrets created imperatively are encoded in plain text unless additional encryption is enabled.

---

## Creating Secrets Declaratively

Creating Secrets declaratively involves defining them in a YAML file.

### YAML Example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
  namespace: default
data:
  db_user: YWRtaW4=  # Base64 encoded
  db_password: c2VjdXJlcGFzc3dvcmQ=  # Base64 encoded
```

To apply the YAML configuration:

```bash
kubectl apply -f secret-definition.yaml
```

---

## Encoding Data for Secrets

Secrets require data to be Base64 encoded. To encode data, use the following command:

```bash
echo -n 'your-data' | base64
```

Example:

```bash
echo -n 'admin' | base64
# Output: YWRtaW4=
```

To decode Base64:

```bash
echo -n 'YWRtaW4=' | base64 --decode
# Output: admin
```

---

## Injecting Secrets into Pods

Secrets can be injected into Pods as environment variables using the `envFrom` or `env` property.

### Example:

Injecting Secrets as environment variables:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app-container
    image: nginx
    envFrom:
    - secretRef:
        name: app-secret
```

The `envFrom` property ensures all keys in the Secret are available as environment variables.

---

## Mounting Secrets as Volumes

Secrets can also be mounted as volumes, making each key in the Secret available as a file.

### Example:

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
      mountPath: /etc/secret
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: app-secret
```

This configuration mounts the Secret as files under `/etc/secret`.

---

## Viewing and Managing Secrets

- To view encoded data in a Secret:

```bash
kubectl get secret app-secret -o yaml
```

- To decode the data:

```bash
echo -n 'base64-encoded-string' | base64 --decode
```

---

## Encrypting Secrets at Rest

By default, Secrets are stored in `etcd` in an unencrypted format. To secure them, enable encryption at rest.

### Steps:

1. **Create an Encryption Configuration File:**

```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
- resources:
  - secrets
  providers:
  - aescbc:
      keys:
      - name: key1
        secret: c2VjdXJlLXNlY3JldC1rZXk=
  - identity: {}
```

2. **Update the API Server Configuration:**
   Edit the Kubernetes API server manifest file to reference the encryption configuration file.

3. **Restart the API Server:**
   Restart the API server to apply encryption settings.

---

## Summary

- Kubernetes Secrets enable secure storage of sensitive data.
- Data in Secrets must be Base64 encoded.
- Secrets can be injected into Pods as environment variables or mounted as volumes.
- Encrypt Secrets at rest to enhance security.
