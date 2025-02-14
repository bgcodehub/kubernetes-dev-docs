# Kubernetes DNS Implementation

## Overview
Kubernetes provides an internal DNS service to facilitate service discovery and enable communication between pods and services. This system ensures that workloads within the cluster can resolve each other using DNS names instead of requiring hardcoded IP addresses.

## How Kubernetes Implements DNS
Kubernetes deploys a DNS service within the cluster, typically using **CoreDNS**. Before version 1.12, Kubernetes used **kube-dns** as its DNS provider. CoreDNS runs as a pod in the `kube-system` namespace and serves as the primary resolver for all pods within the cluster.

### DNS Resolution in Kubernetes
Each pod in Kubernetes has an associated hostname and an IP address. To facilitate service discovery, Kubernetes:
1. Assigns each pod a unique IP address.
2. Registers service and pod names in an internal DNS system.
3. Allows pods to resolve other pods and services using DNS queries.

### Default DNS Server Configuration
When a pod is created, Kubernetes automatically configures its `/etc/resolv.conf` file to use the cluster’s DNS service. The DNS server is referenced using an internal IP address (e.g., `10.96.0.10`), which pods use to resolve other cluster components.

Example `/etc/resolv.conf` configuration:
```plaintext
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local
```
This configuration ensures that:
- Queries for services within the same namespace do not require fully qualified domain names (FQDNs).
- Queries for services in different namespaces require a FQDN (e.g., `web-service.apps.svc.cluster.local`).

## CoreDNS Deployment in Kubernetes
CoreDNS is deployed as a **replicated service** within Kubernetes. It runs as two pods for redundancy and is managed using a **ConfigMap**.

### CoreDNS ConfigMap
CoreDNS requires a configuration file (`Corefile`) to manage its DNS resolution. This file is stored in a **ConfigMap** and mounted into the CoreDNS pods.

Example CoreDNS `Corefile`:
```plaintext
.:53 {
    log
    errors
    health
    kubernetes cluster.local in-addr.arpa ip6.arpa {
        pods insecure
        fallthrough in-addr.arpa ip6.arpa
    }
    prometheus :9153
    cache 30
    loop
    reload
    loadbalance
}
```
### How CoreDNS Resolves Names
When a service is created, Kubernetes automatically registers it in the internal DNS system. For example, creating a service named `web-service` in the `default` namespace will result in the following domain structure:
- `web-service.default.svc.cluster.local`

A pod within the same namespace can resolve it simply as:
```sh
nslookup web-service
```
A pod from another namespace would need to query:
```sh
nslookup web-service.default.svc.cluster.local
```

### How DNS Records Are Created
- **Services**: CoreDNS maps service names to their corresponding ClusterIP.
- **Pods**: Pods can have DNS records if explicitly enabled in CoreDNS.
- **External Queries**: Any unresolved domain names are forwarded to an external resolver.

### DNS Record Format
Pod names are transformed into DNS records by replacing dots (`.`) with dashes (`-`). Example:
- Pod IP: `10.244.1.5`
- DNS Record: `10-244-1-5.default.pod.cluster.local`

## Testing Kubernetes DNS Resolution
To validate that Kubernetes DNS is functioning correctly:
1. Create a test pod with DNS utilities:
   ```sh
   kubectl run dnsutils --image=tutum/dnsutils --command -- sleep infinity
   ```
2. Access the pod and run a DNS query:
   ```sh
   kubectl exec -it dnsutils -- nslookup web-service.default.svc.cluster.local
   ```
   This should return the ClusterIP of `web-service`.

## Conclusion
Kubernetes DNS enables seamless service discovery and communication within a cluster. By using CoreDNS, Kubernetes ensures that pods and services can dynamically resolve each other without requiring manual IP configurations. Understanding how DNS works in Kubernetes is crucial for managing scalable and distributed applications efficiently.

