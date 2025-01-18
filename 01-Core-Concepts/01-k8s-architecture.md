# Kubernetes Architecture & Overview

## Overview

Kubernetes is a powerful orchestration tool designed to automate the deployment, scaling, and management of containerized applications. This documentation provides a detailed overview of the Kubernetes architecture and components, ensuring developers understand how to interact with and deploy applications within a Kubernetes cluster.

---

## Key Concepts

### What Is a Node?

- **Node**: A machine, either physical or virtual, where Kubernetes is installed. Nodes were previously referred to as “minions.”
- A node can host one or more application containers.

### The Need for Clusters

- **Single Node Failure**: If the node hosting your application fails, the application becomes inaccessible.
- **Cluster**: To prevent downtime, Kubernetes groups multiple nodes into a **cluster**, ensuring application availability even if one node fails. This setup also enables **load sharing** across nodes.

---

## Cluster Components

### 1. **Master Node**

The master node orchestrates the cluster by managing container workloads and ensuring consistent application states across worker nodes. Key components of the master node include:

### a. API Server

- Acts as the **front end** of Kubernetes.
- All user interactions, CLI commands, and management tools communicate with the cluster through the **API server**.
- Example:
    
    ```bash
    # Get cluster information via kubectl
    kubectl cluster-info
    
    ```
    

### b. ETCD

- A **distributed, reliable key-value store** that holds all data for managing the cluster.
- Ensures data consistency across nodes and masters, implementing locks to prevent conflicts.

### c. Scheduler

- **Role**: Assigns newly created containers (or pods) to available nodes.
- **Logic**: Considers resource availability and policies when distributing workloads.

### d. Controllers

- Acts as the **brain** behind orchestration.
- Detects when components (nodes, containers, or endpoints) go down and takes corrective actions, such as launching new containers.

---

### 2. **Worker Node**

Worker nodes host application containers and provide the computational power for running workloads. Components include:

### a. Kubelet

- **Role**: An agent that runs on each node, ensuring containers operate as expected.
- **Responsibilities**:
    - Executes actions requested by the master node.
    - Monitors node and container health.

### b. Container Runtime

- The software responsible for running containers.
- Common example: **Docker**.

### c. Kube Proxy

- **Role**: Handles network communication between containers and external clients.
- **Responsibilities**:
    - Manages networking rules.
    - Enables services to communicate across the cluster.

---

## How Kubernetes Works

### 1. Information Storage

- Cluster data is stored in **ETCD** on the master node in a key-value format.
- This includes information on nodes, pods, services, and configuration states.

### 2. Command-Line Tool: `kubectl`

- Developers interact with Kubernetes using the **kubectl** CLI tool.

### Common Commands:

- **Deploy an application**:
    
    ```bash
    kubectl run <app-name> --image=<container-image> --port=<port>
    
    ```
    
- **View cluster information**:
    
    ```bash
    kubectl cluster-info
    
    ```
    
- **List all nodes**:
    
    ```bash
    kubectl get nodes
    
    ```
    

---

## High-Level Workflow

1. **Application Deployment**
    - Use `kubectl` to define application specifications and deploy containers.
2. **Workload Scheduling**
    - The **scheduler** assigns pods to appropriate worker nodes based on resources.
3. **Monitoring and Maintenance**
    - The **controllers** ensure that desired states are maintained, responding to failures.
4. **Networking**
    - **Kube Proxy** manages communication between services and external clients.

---

## Example Workflow

### Deploying a Simple Application

1. Create a simple **Nginx deployment**:
    
    ```bash
    kubectl create deployment nginx --image=nginx
    
    ```
    
2. Expose the deployment as a service:
    
    ```bash
    kubectl expose deployment nginx --type=LoadBalancer --port=80
    
    ```
    
3. Verify the deployment:
    
    ```bash
    kubectl get services
    
    ```
    
4. Access the application via the external IP provided.

---

## Summary

- A **node** is a host machine within a Kubernetes cluster.
- Clusters ensure application availability and load distribution.
- The **master node** orchestrates workloads, while **worker nodes** execute them.
- Key tools like `kubectl` enable developers to deploy, manage, and troubleshoot applications efficiently.