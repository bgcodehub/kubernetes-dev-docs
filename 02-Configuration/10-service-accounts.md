# Service Accounts in Kubernetes

## Overview

Service accounts in Kubernetes are a critical component for secure and efficient communication between applications and the Kubernetes API. They are primarily used by applications to authenticate and interact with the cluster programmatically. Unlike user accounts, which are meant for human users, service accounts are designed for use by applications, scripts, or automation tools.

This document explains the concept of service accounts, their creation and usage, changes introduced in recent Kubernetes versions, and best practices for managing them.

---

## Key Concepts

1. **User Accounts vs. Service Accounts**:
   - **User Accounts**: Used by administrators and developers for cluster management tasks.
   - **Service Accounts**: Used by applications to authenticate and interact with the Kubernetes API.

2. **Examples of Service Account Usage**:
   - **Monitoring Tools**: Tools like Prometheus use service accounts to collect metrics.
   - **CI/CD Pipelines**: Tools like Jenkins deploy applications using service accounts.

---

## Creating a Service Account

To create a service account, use the `kubectl create serviceaccount` command followed by the desired name. For example:

```bash
kubectl create serviceaccount dashboard-sa
```

### Viewing the Created Service Account

To list all service accounts in a namespace, use:
```bash
kubectl get serviceaccounts
```

When a service account is created, Kubernetes automatically generates a token and stores it as a secret object associated with the service account.

---

## Accessing the Service Account Token

To retrieve the token associated with a service account:
1. Use `kubectl describe serviceaccount` to view the linked secret.
2. View the token secret using:
   ```bash
   kubectl describe secret <secret-name>
   ```
3. The token can then be extracted from the secret and used for authentication.

For example:
```bash
kubectl get secret dashboard-sa-token-xxxx -o yaml
```
This token can be added as an **Authorization Header** when making API calls to the Kubernetes cluster.

---

## Automating Token Mounting for Pods

Kubernetes simplifies the use of service accounts by automatically mounting the associated token as a volume inside the Pods. This eliminates the need to manually extract and configure the token.

### Example Pod Definition with Default Service Account

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dashboard-pod
spec:
  containers:
  - name: dashboard
    image: custom-dashboard:latest
```

When this Pod is created, Kubernetes:
- Automatically mounts the token secret at `/var/run/secrets/kubernetes.io/serviceaccount`.
- Makes the token accessible to the application running in the Pod.

---

## Updates in Kubernetes 1.22 and 1.24

### Token Request API

Starting in Kubernetes 1.22, the **Token Request API** was introduced to address security and scalability issues with service account tokens. Key changes include:
1. **Time-Bound Tokens**: Tokens now have a defined expiration time, unlike previous versions where tokens were long-lived.
2. **Audience-Bound Tokens**: Tokens are scoped to specific audiences, improving security.

### Changes in Kubernetes 1.24

From Kubernetes 1.24:
1. Tokens are no longer created automatically as secret objects when a service account is created.
2. To generate a token for a service account, use:
   ```bash
   kubectl create token <serviceaccount-name>
   ```
3. The generated token will have a default expiration time (e.g., 1 hour), which can be adjusted using additional options.

---

## Non-Expiring Tokens

For scenarios where non-expiring tokens are still required:
1. Create a secret object manually:
   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: non-expiring-token
     annotations:
       kubernetes.io/service-account.name: dashboard-sa
   type: kubernetes.io/service-account-token
   ```
2. Ensure the service account exists before creating the secret.

---

## Best Practices for Managing Service Accounts

1. **Minimize Privileges**:
   - Use Role-Based Access Control (RBAC) to assign the least privileges necessary for the service account.

2. **Automate Token Management**:
   - Leverage Kubernetes' automatic token mounting to simplify application configuration.

3. **Use Time-Bound Tokens**:
   - Always prefer tokens generated via the Token Request API for enhanced security.

4. **Monitor Token Usage**:
   - Regularly audit token usage and revoke unused or compromised tokens.

---

## Summary

Service accounts play a vital role in Kubernetes for enabling applications to securely interact with the cluster. Recent updates, such as the introduction of the Token Request API, enhance the security and scalability of service account tokens. By following best practices and leveraging these features, you can ensure secure and efficient application deployments in Kubernetes.

---
