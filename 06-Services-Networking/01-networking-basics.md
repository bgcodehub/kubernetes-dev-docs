# Networking Basics in Linux

## Overview
Networking is a fundamental aspect of system administration and application development. Understanding how networking works in Linux helps in configuring systems, troubleshooting issues, and securing network communication.

---

## Core Networking Concepts

1. **Switching** - Connects devices within the same network and forwards data based on MAC addresses.
2. **Routing** - Determines the path that data packets take from source to destination.
3. **Gateways** - Acts as a bridge between different networks.
4. **DNS (Domain Name System)** - Translates domain names to IP addresses for easy access to resources.

---

## Configuring DNS in Linux
DNS settings in Linux can be managed using the `/etc/resolv.conf` file or through network manager tools. To check the current DNS configuration:

```bash
cat /etc/resolv.conf
```

To manually set a DNS server, edit the file and add:

```bash
nameserver 8.8.8.8
nameserver 8.8.4.4
```

For systems using `systemd-resolved`, you can update DNS settings using:

```bash
systemctl restart systemd-resolved
```

---

## Network Interfaces and Configuration
Linux systems use network interfaces (e.g., `eth0`, `wlan0`) to communicate over the network. To view available interfaces:

```bash
ip link show
```

Assigning an IP address to an interface:

```bash
ip addr add 192.168.1.100/24 dev eth0
```

To check the IP address configuration:

```bash
ip addr show
```

To configure a persistent network interface, edit the `/etc/network/interfaces` file (Debian-based systems) or `/etc/sysconfig/network-scripts/ifcfg-eth0` (RHEL-based systems).

---

## Routing and Gateways
Routing allows communication between different networks. To display the current routing table:

```bash
ip route show
```

To add a static route:

```bash
ip route add 192.168.2.0/24 via 192.168.1.1
```

To set a default gateway:

```bash
ip route add default via 192.168.1.1
```

---

## Network Packet Forwarding
By default, Linux does not forward packets between interfaces. To enable packet forwarding:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

To make this change persistent, edit `/etc/sysctl.conf` and add:

```bash
net.ipv4.ip_forward = 1
```

Apply the changes:

```bash
sysctl -p
```

---

## Conclusion
Understanding networking basics in Linux helps with configuring and troubleshooting systems efficiently. These foundational concepts will be essential for managing networks, setting up services, and ensuring secure communication across systems.

