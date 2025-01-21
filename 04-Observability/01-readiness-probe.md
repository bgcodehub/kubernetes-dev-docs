# Observability in Kubernetes: Readiness and Liveness Probes

## Overview
Observability is critical for ensuring applications in Kubernetes operate efficiently and reliably. This section focuses on **Readiness Probes**, **Liveness Probes**, and their role in monitoring and maintaining Pods in Kubernetes.

---

## Pod Lifecycle Recap
Understanding a Pod's lifecycle is essential for configuring probes effectively:
- **Pending**: The scheduler is attempting to assign a node.
- **Running**: The container is active, and the application is executing.
- **Succeeded/Failed**: The container has either completed successfully or terminated with errors.

**Command to Monitor Pod Status**:
```bash
kubectl get pods
```

### Key Conditions
- **Scheduled**: Indicates the Pod is assigned to a node.
- **Initialized**: All containers are ready.
- **Ready**: The application inside the container is prepared to handle requests.

**Troubleshooting Pending State**:
```bash
kubectl describe pod <pod_name>
```

---

## Readiness Probes
**Purpose**: Determine whether a container is ready to serve traffic. This avoids routing requests to unprepared applications, such as:
- A database service waiting to establish a connection.
- A web application server warming up.

**Example Use Case**:
- The application may take time to initialize (e.g., Jenkins requires 10-15 seconds before the web UI is accessible).

---

## Configuring Readiness Probes
Probes are defined within the **Pod specification**. Multiple configurations are available:

1. **HTTP GET Probe**:
   Checks if the application responds to an HTTP request.
   ```yaml
   readinessProbe:
     httpGet:
       path: /health
       port: 8080
   ```

2. **TCP Socket Probe**:
   Verifies if a TCP socket is listening.
   ```yaml
   readinessProbe:
     tcpSocket:
       port: 3306
   ```

3. **Exec Command Probe**:
   Executes a script or command inside the container.
   ```yaml
   readinessProbe:
     exec:
       command:
         - sh
         - '-c'
         - curl -f http://localhost:8080/health || exit 1
   ```

### Additional Options
- **`initialDelaySeconds`**: Time to wait before starting the probe.
- **`periodSeconds`**: Frequency of checks.
- **`failureThreshold`**: Number of failures before marking the container as "not ready."

---

## Benefits of Readiness Probes
- **Traffic Redirection**: Ensures traffic is only routed to ready containers.
- **Prevents Service Disruption**: Avoids sending requests to unprepared Pods in a multi-Pod setup.

---

## Multi-Pod Scenario: Importance of Readiness Probes
Imagine a scenario with three Pods:
1. Two Pods are already serving traffic.
2. A new Pod requires initialization.

Without a readiness probe:
- Traffic is routed to the new Pod prematurely, causing potential service disruptions.

With a readiness probe:
- Traffic continues to the old Pods until the new one is ready.

---

## Liveness Probes
**Purpose**: Detect if a container is in a failed state and requires a restart. Liveness probes help:
- Restart stuck applications.
- Recover from deadlocks or memory leaks.

---

## Configuring Liveness Probes
The configuration is similar to readiness probes, with options for HTTP, TCP, and Exec probes.

**Example**:
```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 10
```

---

## Monitoring and Debugging Probes
1. **Check Logs**:
   ```bash
   kubectl logs <pod_name>
   ```
2. **Describe Pod for Probe Status**:
   ```bash
   kubectl describe pod <pod_name>
   ```

---

## Summary
Readiness and Liveness Probes are essential for maintaining high availability and reliability in Kubernetes:
- **Readiness Probes** ensure traffic is routed only to prepared containers.
- **Liveness Probes** monitor and recover failing containers.

---