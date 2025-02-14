# CNI in Kubernetes

## Overview
Container Network Interface (CNI) is a set of standards that define how container runtimes configure networking for containers. Kubernetes uses CNI plugins to manage container networking, ensuring seamless communication between pods and services within a cluster.

---

## Configuring CNI in Kubernetes

### Responsibilities of CNI
CNI is responsible for:
- Creating network namespaces for containers
- Assigning IP addresses to containers
- Connecting network namespaces to the appropriate networks
- Managing routing, masquerading, and firewall rules as required

### Specifying CNI Plugins in Kubernetes
CNI plugins must be invoked by Kubernetes components responsible for container creation. These plugins are specified in configuration files stored in:

```bash
/etc/cni/net.d/
```
Each configuration file defines the network type, IP assignment method, and routing rules.

### Available CNI Plugins
Kubernetes supports multiple CNI plugins, including:
- **bridge**: Creates a virtual bridge for containers to communicate within the same node.
- **flannel**: Provides an overlay network for cross-node communication.
- **calico**: Implements network policies and enforces security rules.
- **dhcp**: Assigns IP addresses dynamically from an external DHCP server.
- **weave**: Offers a distributed overlay network.

---

## CNI Plugin Configuration
A sample CNI configuration file for a bridge network:

```json
{
  "cniVersion": "0.3.1",
  "name": "mynet",
  "type": "bridge",
  "bridge": "cni0",
  "isGateway": true,
  "ipMasq": true,
  "ipam": {
    "type": "host-local",
    "subnet": "10.244.1.0/24",
    "rangeStart": "10.244.1.10",
    "rangeEnd": "10.244.1.100",
    "routes": [{"dst": "0.0.0.0/0"}]
  }
}
```

### Explanation:
- **`type`**: Specifies the plugin type (`bridge`, `flannel`, `calico`, etc.).
- **`isGateway`**: Designates the node as a gateway for outbound traffic.
- **`ipMasq`**: Enables IP masquerading for external communication.
- **`ipam`**: Defines IP address management settings.

---

## CNI Plugin Execution
When a pod is created, Kubernetes executes the configured CNI plugin in the following sequence:
1. **ADD command**: Allocates an IP and attaches the pod to the network.
2. **DEL command**: Cleans up network resources when the pod is deleted.
3. **CHECK command**: Ensures the pod's network is correctly configured.

Example of executing a CNI plugin manually:
```bash
sudo CNI_COMMAND=ADD CNI_NETNS=/var/run/netns/xyz /opt/cni/bin/bridge < /etc/cni/net.d/mynet.conf
```

---

## Conclusion
CNI plays a critical role in Kubernetes networking by defining and enforcing how containers communicate within a cluster. Understanding and configuring CNI plugins properly ensures efficient and secure network management for Kubernetes workloads.
