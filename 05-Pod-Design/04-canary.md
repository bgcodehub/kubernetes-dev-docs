# Canary Updates in Kubernetes

## Overview
Canary updates are a deployment strategy in Kubernetes where a new version of the application is deployed, but only a small percentage of traffic is routed to it initially. This allows for testing and validation of the new version before fully replacing the older version.

## Key Concepts
- **Incremental Traffic Routing**: A small percentage of traffic is directed to the new version while the majority continues to use the old version.
- **Validation**: Tests are run on the new version to ensure stability and performance.
- **Gradual Upgrade**: If the tests are successful, traffic to the new version is incrementally increased until it fully replaces the old version.

## Implementation Steps
1. **Deploy the Primary Application**:
   - Create a deployment for the existing application version (e.g., version 1).
   - Configure a service to route traffic to this deployment using a label selector.

2. **Deploy the Canary Version**:
   - Deploy the new application version as a separate deployment (e.g., version 2).
   - Initially, limit the number of replicas to one.

3. **Traffic Routing**:
   - Configure the service to use a common label for both the primary and canary deployments. This allows the service to distribute traffic to both versions.
   - By adjusting the number of replicas in the canary deployment, control the percentage of traffic routed to it.

4. **Validation and Rollout**:
   - Run tests on the canary version to verify its functionality.
   - Gradually increase traffic to the canary version by scaling up its replicas while reducing the primary deployment's replicas.
   - Once the canary version is validated, scale it up to fully replace the primary version.

## Example Configuration
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-primary
spec:
  replicas: 5
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
        version: v1
    spec:
      containers:
      - name: app-container
        image: myapp:v1
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-canary
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
        version: v2
    spec:
      containers:
      - name: app-container
        image: myapp:v2
---
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

## Traffic Distribution
- The service distributes traffic equally among all replicas of both deployments.
- For example:
  - If there are 5 replicas in the primary deployment and 1 in the canary deployment, approximately 83% of traffic will go to the primary deployment and 17% to the canary deployment.

## Limitations
- Traffic splitting precision depends on the number of replicas in the deployments. For instance:
  - To achieve a 1% traffic split, at least 100 pods are required across both deployments.
- Kubernetes primitives provide limited control over traffic distribution. Advanced tools like Istio can offer more precise control.

## Best Practices
- Use tools like Istio for finer-grained traffic control.
- Test the canary version thoroughly before gradually increasing traffic.
- Monitor the application’s performance and rollback if issues are detected.

## Summary
Canary updates in Kubernetes allow for safe and controlled deployment of new application versions by initially routing minimal traffic to the new version. This strategy minimizes risks and ensures a seamless upgrade process.