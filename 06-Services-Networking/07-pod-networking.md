# Pod Networking in Kubernetes

## Introduction
Pod networking in Kubernetes is essential for ensuring communication between pods within a cluster and with external services. This documentation outlines the networking setup required for Kubernetes pods and how they interact within the cluster.

## Understanding Pod Networking
Beyond node communication, Kubernetes introduces another networking layer at the pod level. As the Kubernetes cluster grows with more pods and services, proper networking becomes crucial. This section covers:
- How pods are addressed
- How they communicate with each other
- Accessing services running on pods internally and externally

## Kubernetes Networking Requirements
Kubernetes does not come with a built-in networking solution but expects implementations to meet the following requirements:
1. Each pod must get a unique IP address.
2. Every pod should be able to communicate with other pods within the same node.
3. Every pod should be able to communicate with pods on other nodes using the same IP address.
4. The IP address range or subnet does not matter as long as automatic assignment and connectivity between pods are ensured.

## Networking Solutions
There are many networking solutions available that meet these requirements. We relate these solutions to previously discussed networking concepts such as IP address management and network namespaces in CNI (Container Network Interface).

### Cluster Setup
We assume a three-node cluster where each node has an external network IP in the 192.160.1.x series:
- Node 1: 192.160.1.11
- Node 2: 192.160.1.12
- Node 3: 192.160.1.13

When containers are created, Kubernetes automatically creates network namespaces for them.

### Creating a Bridge Network
To enable communication, namespaces are attached to a network. A bridge network is created on each node and brought up with an assigned IP address:
- Node 1 Bridge: 10.244.1.1
- Node 2 Bridge: 10.244.2.1
- Node 3 Bridge: 10.244.3.1

This allows containers to be assigned IPs from their respective bridge networks.

### IP Address Management and Routing
Each container gets a unique IP, allowing seamless communication between them. However, pods on different nodes need routing:
1. Node 1 has a pod at 10.244.1.2.
2. Node 2 has a pod at 10.244.2.2.
3. Node 1’s pod does not know how to reach 10.244.2.2, as it is in a different network.
4. A route entry is added to Node 1’s routing table to direct traffic for 10.244.2.2 to Node 2’s IP (192.160.1.12).

A similar routing approach is applied for all nodes, ensuring pod communication across the cluster.

### Using a Central Router
Instead of manually configuring routes on each node, a better approach is to use a router and configure default routes. The router will:
- Act as the default gateway for all nodes.
- Manage routes between all networks.
- Ensure connectivity by forwarding packets to the appropriate destinations.

### Automating the Process
A script is created to:
1. Assign IPs to containers.
2. Connect containers to the network.
3. Automatically execute on new container creation.

This script is integrated with Kubernetes' CNI to ensure new pods are connected to the network automatically.

## Conclusion
By implementing these networking configurations, we establish reliable communication between pods and services in a Kubernetes cluster. The next step involves exploring how Kubernetes configures CNI to handle these processes automatically.

