# Resource Requests and Limits in Kubernetes

## Overview
In Kubernetes, resource management is vital for maintaining application performance and stability. **Resource Requests** and **Limits** ensure fair allocation of CPU and memory across Pods, preventing resource starvation or overuse.

This document details how resource requests and limits work, their configuration, and best practices for applying them effectively in your cluster.

---

## Key Concepts

1. **Resource Requests**: The minimum amount of CPU or memory required for a container to run. Kubernetes uses this value to schedule Pods on nodes with sufficient resources.
2. **Resource Limits**: The maximum amount of CPU or memory a container can consume. This ensures that a container does not overuse resources, potentially affecting other workloads.

---

## How Kubernetes Handles Resource Requests

When a Pod is scheduled, Kubernetes uses the resource requests defined in the Pod specification to find a suitable node. If a node lacks the requested resources, the Pod will remain in the **Pending** state until sufficient resources are available.

### Example: Configuring Resource Requests
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: nginx
    resources:
      requests:
        memory: "4Gi"
        cpu: "2"
```

#### Explanation:
- **`memory: "4Gi"`**: The Pod requests 4 GiB of memory.
- **`cpu: "2"`**: The Pod requests 2 vCPUs.

The Kubernetes scheduler will only place this Pod on a node with at least 4 GiB of memory and 2 vCPUs available.

---

## Configuring Resource Limits

Resource limits define the upper boundary of CPU and memory a container can use. This ensures no single container dominates the node's resources, protecting other workloads.

### Example: Configuring Resource Limits
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: nginx
    resources:
      limits:
        memory: "6Gi"
        cpu: "3"
```

#### Explanation:
- **`memory: "6Gi"`**: The container cannot consume more than 6 GiB of memory.
- **`cpu: "3"`**: The container is limited to 3 vCPUs.

If a container exceeds its memory limit, it will be terminated with an **Out of Memory (OOM)** error. CPU usage exceeding the limit will be throttled but not terminated.

---

## Combining Requests and Limits

It is common to define both requests and limits to balance guaranteed resources with upper consumption boundaries.

### Example: Defining Both Requests and Limits
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: nginx
    resources:
      requests:
        memory: "2Gi"
        cpu: "1"
      limits:
        memory: "4Gi"
        cpu: "2"
```

#### Explanation:
- The container is guaranteed **2 GiB of memory** and **1 vCPU**.
- It can use up to **4 GiB of memory** and **2 vCPUs** if resources are available.

---

## Resource Units Explained

### CPU Units
- **1 CPU**: Equivalent to 1 vCPU in cloud environments or 1 core in physical machines.
- **m (MilliCPU)**: Fractional CPU units. For example, `500m` equals 0.5 vCPUs.

### Memory Units
- **Mi**: Mebibyte (1 MiB = 1024 KiB).
- **Gi**: Gibibyte (1 GiB = 1024 MiB).
- **M and G**: Metric units (e.g., 1G = 1000 MB).

---

## LimitRange: Setting Defaults and Constraints

Kubernetes allows the use of **LimitRange** objects to enforce default values and constraints for Pods and containers.

### Example: Defining a LimitRange
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-memory-limits
  namespace: example-namespace
spec:
  limits:
  - default:
      memory: "1Gi"
      cpu: "500m"
    defaultRequest:
      memory: "500Mi"
      cpu: "250m"
    max:
      memory: "4Gi"
      cpu: "2"
    min:
      memory: "256Mi"
      cpu: "100m"
    type: Container
```

#### Explanation:
- **`default`**: Default resource limits for containers in the namespace.
- **`defaultRequest`**: Default resource requests for containers.
- **`max` and `min`**: Constraints on the maximum and minimum resources a container can request or consume.

---

## Resource Quotas: Enforcing Namespace Limits

To restrict the total resource consumption within a namespace, Kubernetes provides **ResourceQuota** objects.

### Example: Defining a ResourceQuota
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: namespace-quota
  namespace: example-namespace
spec:
  hard:
    requests.cpu: "10"
    requests.memory: "20Gi"
    limits.cpu: "15"
    limits.memory: "30Gi"
```

#### Explanation:
- **`requests.cpu`**: Total CPU requests cannot exceed 10 vCPUs.
- **`limits.memory`**: Total memory usage cannot exceed 30 GiB.

---

## Best Practices

1. **Set Requests for All Pods**: Ensure all Pods have resource requests to guarantee fair scheduling.
2. **Define Limits Where Necessary**: Use limits to prevent resource hogging, especially in multi-tenant clusters.
3. **Use LimitRange**: Apply default values and constraints at the namespace level to enforce consistency.
4. **Monitor Resource Usage**: Use tools like Prometheus and Grafana to monitor and adjust resource configurations.

---

## Summary

- Resource requests and limits are essential for efficient resource allocation in Kubernetes.
- Requests ensure Pods are scheduled on suitable nodes, while limits prevent overuse of resources.
- Use LimitRange and ResourceQuota objects to define namespace-wide constraints and defaults.

--- 
