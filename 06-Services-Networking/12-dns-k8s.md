# DNS in Kubernetes

## Overview
Domain Name System (DNS) is a core component in Kubernetes, enabling service discovery and communication within a cluster. Kubernetes deploys a built-in DNS server by default, allowing pods and services to communicate using domain names instead of IP addresses.

## DNS Resolution in Kubernetes
Each Kubernetes cluster comes with an internal DNS system that resolves service and pod names. The Kubernetes DNS service maps internal names to IP addresses, ensuring that applications can dynamically locate services as they are created and scaled.

### Default DNS Behavior
- Each node and pod has a **nodename** and **IP address**.
- Kubernetes automatically registers node names and IP addresses in a DNS server.
- DNS resolution enables communication between different cluster components like pods and services.

## Service DNS Resolution
When a service is created in Kubernetes, the DNS system automatically assigns it a domain name. For example:
```sh
web-service.default.svc.cluster.local
```
This domain name allows any pod within the cluster to reach the service using its DNS name instead of its IP address.

### Example Scenario
1. A test pod with IP `10.244.1.5`.
2. A web-service pod with IP `10.244.2.5`.
3. The test pod needs to reach the web-service pod.
4. Kubernetes DNS resolves `web-service` to its assigned IP address.

## Fully Qualified Domain Names (FQDN)
Kubernetes groups services into hierarchical subdomains for better resolution:
- **Service Name**: `web-service`
- **Namespace**: `apps`
- **Subdomain**: `svc`
- **Cluster Root**: `cluster.local`
- **FQDN**: `web-service.apps.svc.cluster.local`

Pods in different namespaces must use FQDNs to communicate.

## Pod DNS Resolution
By default, Kubernetes does not create DNS records for pods, but this can be enabled.
- Kubernetes generates pod DNS names by replacing dots in the IP address with dashes.
- Example: A pod with IP `10.244.1.5` would have the hostname `10-244-1-5.default.pod.cluster.local`.
- DNS records allow inter-pod communication without requiring IP addresses.

## Conclusion
Kubernetes DNS simplifies service discovery by allowing pods and services to communicate using domain names. Understanding DNS behavior in Kubernetes helps in configuring networking efficiently and ensuring reliable connectivity within a cluster.

