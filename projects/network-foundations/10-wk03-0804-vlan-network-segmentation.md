# VLANs and Network Segmentation

## What Is Network Segmentation?

Network Segmentation is a layer 2 logical architecture that uses Virtual Local Area Networks to segment systems according to their function and security requirement. 

## What Is a VLAN?

Virtual Local Area Network operates on layer 2 as a logical architecture to segment systems

## VLAN vs. Subnet

A VLAN -- Virtual Local Area Network logically separates devices at OSI Layer 2

A subnet divides IP networks at Layer 3

## Broadcast Domains

A broadcast is used by ARP when it needs the MAC address associated with another local IP. Segmented VLANs have their own broadcast domain so that ARP doesn't have access to every device on the network. 

## Inter-VLAN Routing

When VLANs need to communicate, layer 2 switches cannot forward traffic between subnets. Inter-VLAN routing handles this with a layer 3 router, switch or firewall. Understanding firewalls and ACLs from a previous learning concept helps to put this into perspective.  

## Segmentation and ACLs

The firewall can enforce allow and deny rules for subnets to communicate with other domains in a design often referred to as least privilege.

## Why Segmentation Matters to Security Analysts

An analyst is particularly concerned with how segmentation works because it helps to set up a logical architecture for controlling lateral movement between systems 

## My Network Investigation

### ipconfig

We identify our addressing in this run of ipconfig.

IPv4: 192.XXX....
Subnet Mask: 255....
Default Gateway: 192.XXX....

### arp -a

We are looking at ARP -a output, specifically the dynamic entries so we can ask ourself; Are these addresses in my subnet? 

### route print -4

We locate the IPv4 routing table and look for the specific default entry 0.0.0.0 and the route corresponding to your local network.

We interpret this as essentially meaning that "On-Link" refers to this network is reachable through this interface and the default route means that for destinations without a more specific matching route, send the traffic toward this gateway. 

### ping and tracert

We are now testing our boundary using commands we are familiar with. I will first ping my gateway and then tracert 8.8.8.8, essentially comparing the two.

My gateway should normally appear as the first routed hop toward the internet. 

## SOC Investigation Scenario

You work in a SOC supporting this environment:

VLAN 10 — Employees
10.10.10.0/24

VLAN 20 — Servers
10.10.20.0/24

VLAN 30 — IoT
10.10.30.0/24

A SIEM generates an alert:

SOURCE
10.10.10.57

DESTINATIONS
10.10.20.11
10.10.20.12
10.10.20.13
10.10.20.14
10.10.20.15

DESTINATION PORT
445/TCP

RESULT
BLOCKED

Think through the evidence.

Question 1

Which VLAN contains the suspicious source?

Question 2

Which VLAN is being targeted?

Question 3

What service normally uses TCP 445?

Question 4

Why might connections to several sequential server addresses be suspicious?

Question 5

Most importantly:

> What security control appears to be working correctly?

The key observation is that the attempts were:

BLOCKED

Your segmentation/firewall policy may have prevented the compromised workstation from reaching the server network.

That's exactly why analysts care about architecture—not merely individual alerts.

## My Reflection

### Why does segmentation reduce an attacker's ability to move laterally?

Segmentation reduces lateral movement by breaking the network into controlled zones, making it harder for attackers to pivot from one system to another, thus containing breaches and limiting damage.

### What is the difference between a VLAN and a subnet?

A VLAN (Virtual Local Area Network) and a Subnet (Subnetwork) both segment networks to improve performance, security, and manageability, but they operate at different OSI layers and use different mechanisms.

VLAN works at Layer 2 (Data Link Layer), grouping devices logically based on switch configurations, regardless of physical location. It uses VLAN tagging (IEEE 802.1Q) to identify traffic and creates separate broadcast domains without additional physical hardware. Devices in different VLANs require a Layer 3 device (router or Layer 3 switch) to communicate.

Subnet operates at Layer 3 (Network Layer), dividing an IP network into smaller IP address ranges using subnet masks. Each subnet has its own network address and limits broadcast traffic within its range. Communication between subnets also requires routing.

### Why is routing required when devices on different IP subnets need to communicate?

Routing is required because Layer 2 devices cannot interpret IP addresses to forward between subnets — only a Layer 3 device like a router can perform that function

### How can ACLs or firewall policies make segmentation more valuable from a security perspective?

ACLs and firewall policies turn segmentation from a simple network split into a secure, monitored, and enforceable boundary. They limit attack surfaces, contain breaches, and provide visibility—making segmentation far more valuable from a security standpoint

## Key Exam Takeaways

VLAN
→ Layer 2
→ Separate broadcast domains

Subnet
→ Layer 3
→ Logical IP network

Inter-VLAN communication
→ Requires Layer 3 routing

Segmentation
→ Limits communication and blast radius

ACL / Firewall
→ Controls permitted traffic between segments

TCP 445
→ SMB

Compromised host → Other internal systems
→ Possible lateral movement
