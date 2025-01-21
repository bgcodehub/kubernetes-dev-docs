# Updates and Rollbacks in Kubernetes Deployments

## Overview
Kubernetes provides robust mechanisms for updating and rolling back applications in a deployment. This ensures seamless upgrades and the ability to revert to a previous version if issues arise. This lecture focuses on deployment rollouts, versioning, and strategies for updating and rolling back applications.

---

## Rollouts and Versioning
- **Rollouts in Deployments:**
  - When a deployment is created or updated, Kubernetes triggers a **rollout**, resulting in a new deployment revision (e.g., Revision 1, Revision 2, etc.).
  - These revisions help track changes to deployments and enable rollbacks if necessary.

- **Commands for Rollouts:**
  - `kubectl rollout status`: View the status of the rollout.
  - `kubectl rollout history`: Check the revisions and history of a deployment.

---

## Deployment Strategies
Kubernetes supports multiple deployment strategies to ensure minimal downtime during application updates:

1. **Recreate Strategy:**
   - All existing instances are destroyed before new ones are created.
   - Results in application downtime.

2. **Rolling Update Strategy (Default):**
   - Gradually replaces old versions with new ones, one by one.
   - Ensures the application remains accessible during the upgrade process.

---

## Performing Updates
- Updates can include:
  - Changing the application version by updating the container image.
  - Modifying the number of replicas.
  - Updating deployment labels.

- Steps for Updates:
  1. Modify the deployment definition file (e.g., to change the container image).
  2. Apply changes using `kubectl apply -f <deployment-file>`.

---

## Rolling Back Deployments
If an update causes issues, Kubernetes allows rolling back to a previous version:

- **Command to Rollback:**
  - `kubectl rollout undo <deployment-name>`

- **How it Works:**
  - The pods in the new ReplicaSet are destroyed.
  - Older pods from the previous ReplicaSet are restored.
  - The application reverts to its previous stable state.

---

## Behind the Scenes of a Deployment
- **ReplicaSets:**
  - Kubernetes creates ReplicaSets under the hood to manage the number of pods.
  - During updates:
    - Old ReplicaSets are scaled down.
    - New ReplicaSets are scaled up.

- **Commands for Observing ReplicaSets:**
  - `kubectl get replicasets`: View all ReplicaSets and their states.
  - `kubectl describe deployment`: See detailed information about the deployment.

---

## Key Commands Recap
1. **Create a Deployment:**
   ```bash
   kubectl create -f <deployment-file>
   ```

2. **Update a Deployment:**
   ```bash
   kubectl apply -f <deployment-file>
   ```

3. **View Rollout Status:**
   ```bash
   kubectl rollout status <deployment-name>
   ```

4. **View Rollout History:**
   ```bash
   kubectl rollout history <deployment-name>
   ```

5. **Rollback a Deployment:**
   ```bash
   kubectl rollout undo <deployment-name>
   ```

---

## Best Practices
- **Always Save Deployment Files:**
  - Maintain deployment files in version control for traceability.
  - Modify files instead of relying solely on CLI commands for updates.
- **Use Rolling Updates:**
  - Ensure seamless application updates with no downtime.
- **Monitor Deployment Events:**
  - Use `kubectl describe deployment` for insights into deployment activities.

---

## Summary
Kubernetes makes it easy to update and rollback applications with its deployment rollouts and versioning capabilities. By leveraging deployment strategies, commands, and best practices, you can ensure your applications remain stable and responsive during updates.