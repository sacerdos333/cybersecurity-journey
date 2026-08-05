# Switch Security and Layer 2 Defense

## My Understanding of Layer 2 Switching

Today's cybersecurity practice expanded on my previous work with ARP, VLANs, and network segmentation by examining how Ethernet switches forward traffic and how Layer 2 functionality can be protected from abuse.

An Ethernet switch primarily operates at **Layer 2 — the Data Link layer — of the OSI model**. The switch learns the source MAC addresses of devices communicating through its interfaces and associates those MAC addresses with specific switch ports.

Conceptually, a switch might learn information similar to:

| Switch Port | MAC Address       |
| ----------- | ----------------- |
| Port 1      | AA-AA-AA-AA-AA-AA |
| Port 2      | BB-BB-BB-BB-BB-BB |
| Port 3      | CC-CC-CC-CC-CC-CC |

When a frame arrives, the switch examines the **destination MAC address** and consults its MAC address table, sometimes called a **CAM table**, to determine the appropriate interface through which to forward the frame.

This allows a switch to send known unicast traffic toward the intended destination rather than unnecessarily forwarding it through every switch port.

---

## ARP Cache vs. Switch MAC Address Table

An important distinction I reinforced today is that an **ARP cache and a switch MAC address table are related but serve different purposes**.

### ARP Cache

An endpoint uses ARP to associate an IPv4 address with a MAC address on the local network.

Conceptually:

```text
IP Address
    ↓
   ARP
    ↓
MAC Address
```

The Windows command:

```powershell
arp -a
```

allows me to examine these IP-to-MAC mappings on my computer.

### Switch MAC Address Table

A switch maintains a different mapping:

```text
MAC Address
     ↓
Switch Port
```

The switch uses this information when determining where an Ethernet frame should be forwarded.

Therefore:

```text
ARP Cache
IP Address → MAC Address

Switch MAC/CAM Table
MAC Address → Switch Port
```

Understanding this distinction is important because different Layer 2 attacks target different mechanisms.

---

## What Is MAC Flooding?

A **MAC flooding attack**, sometimes referred to as a **CAM table overflow attack**, attempts to manipulate or exhaust a switch's MAC address table by generating traffic containing large numbers of fabricated source MAC addresses.

Conceptually:

```text
Attacker
   │
   ├── Fake MAC 0001
   ├── Fake MAC 0002
   ├── Fake MAC 0003
   ├── Fake MAC 0004
   └── Many additional MAC addresses
              │
              ▼
        Switch MAC Table
```

If a switch can no longer maintain the expected MAC-to-port mappings, its handling of unknown-destination traffic can be affected.

This could potentially increase an attacker's opportunity to observe traffic that would normally be forwarded only toward the appropriate destination.

Modern managed switches can implement protections against this behavior, so MAC flooding should not simply be thought of as automatically "turning a switch into a hub."

---

## ARP Poisoning vs. MAC Flooding

One of my key takeaways is that **ARP poisoning and MAC flooding are not the same attack**, even though both involve Layer 2 concepts.

| Attack            | Target                     |                                       |
| ----------------- | -------------------------- | ------------------------------------- |
| ARP Poisoning     | IP-to-MAC associations     |                                       |
| MAC Flooding      | Switch MAC/CAM table       |                                       |
| Primary Objective | Redirect/intercept traffic | Manipulate switch forwarding behavior |

ARP poisoning attempts to convince another device that an IP address belongs to an incorrect MAC address.

MAC flooding instead attempts to manipulate the switch's ability to maintain MAC-to-port mappings.

This distinction is important when determining what evidence I would expect to find during a security investigation.

---

# Port Security

## What Is Port Security?

**Port security** is a defensive mechanism available on managed switches that can restrict which MAC addresses are permitted to communicate through a switch interface.

For example:

```text
Switch Port
     │
     ▼
Authorized Workstation
     │
     ▼
Expected MAC Address
```

An administrator could configure a switch port to permit only a limited number of MAC addresses.

If an unauthorized device is connected or an unexpected number of MAC addresses appear, the switch can respond according to its configured security policy.

This can help defend against:

* Unauthorized network devices
* MAC flooding
* Improper device connections
* Certain Layer 2 attacks

---

## Port Security Violation Modes

Cisco-style switch port security commonly includes three violation modes that are useful to recognize for certification exams.

### Protect

Unauthorized frames are dropped while the port can continue forwarding traffic from permitted MAC addresses.

### Restrict

Unauthorized traffic is dropped, while the switch can also record or report the security violation.

### Shutdown

The switch can place the affected interface into an error-disabled state following a port-security violation.

Conceptually:

```text
Unexpected MAC Address
          │
          ▼
   Port Security Policy
          │
    ┌─────┼─────┐
    │     │     │
 Protect Restrict Shutdown
```

---

# Sticky MAC Learning

Another concept I reviewed today was **sticky MAC learning**.

Instead of requiring an administrator to manually enter every permitted MAC address, supported switches can dynamically learn MAC addresses and associate them with a port-security configuration.

Conceptually:

```text
Authorized Device Connects
          ↓
Switch Learns MAC
          ↓
MAC Associated With Port
          ↓
Unexpected MAC Appears
          ↓
Port-Security Policy Evaluates It
```

This can make port-security administration more practical while still restricting unauthorized devices.

---

# Rogue Device Risks

Layer 2 security is also closely connected with physical security.

If an organization leaves active Ethernet wall jacks or unused switch ports unrestricted, an unauthorized individual might connect a device directly to the corporate network.

```text
Unauthorized Device
        │
        ▼
   Ethernet Jack
        │
        ▼
      Switch
        │
        ▼
Corporate Network
```

Potential defenses include:

* Port security
* Disabling unused switch ports
* 802.1X authentication
* Network Access Control (NAC)
* VLAN assignment
* Endpoint compliance policies

This reinforced an important cybersecurity principle for me:

> Physical access to network infrastructure can create cybersecurity risk if technical controls are not also implemented.

---

# 802.1X and Network Access Control

I also learned about **IEEE 802.1X**, which provides port-based network access control.

Rather than assuming that a device should receive network access simply because it is physically connected, 802.1X can require authentication first.

The basic architecture includes:

```text
Supplicant
    │
    ▼
Authenticator
    │
    ▼
Authentication Server
```

### Supplicant

The endpoint requesting network access.

### Authenticator

Typically the switch or wireless access point controlling access to the network.

### Authentication Server

Validates authentication information. **RADIUS** is commonly associated with this function.

A useful certification relationship is:

```text
802.1X
→ Port-Based Network Access Control

RADIUS
→ Centralized AAA

AAA
→ Authentication
→ Authorization
→ Accounting
```

This extends the principle of least privilege to network connectivity: simply reaching a network connection point does not necessarily mean a device should automatically be trusted.

---

# My Layer 2 Investigation

For today's hands-on exercise, I used Windows networking commands to examine Layer 2 information associated with my own system and local network.

## `ipconfig /all` — Identify My Active Network Interface

I used:

```powershell
ipconfig /all
```

I reviewed information including:

* Active network adapter
* IPv4 address
* Subnet mask
* Default gateway
* Physical/MAC address

### My Observation

ipconfig /all — Identifying My Active Network Interface

I used:

ipconfig /all

to identify the network adapters installed on my computer and determine which interface was actively being used for network connectivity.

My Observation

During this investigation, I learned how to distinguish between network adapters that were actively connected and adapters that were disconnected or otherwise not being used for my current network connection.

An unexpected finding was that my Wi-Fi adapter was being used for my active Internet connection instead of my Ethernet adapter, even though a physical Ethernet cable was connected to my PC.

Rather than simply recording this observation, I investigated why Windows was preferring the wireless connection. I reviewed the network adapter configuration and advanced network settings and made adjustments intended to make Ethernet the preferred connection. I also reviewed and modified relevant Ethernet adapter power-management settings that could affect availability or connectivity.

This became an unexpected but valuable troubleshooting exercise because it demonstrated that the presence of a physical Ethernet connection does not necessarily mean Windows is actively using that interface for network traffic.

I plan to verify the configuration again after restarting or logging back into the system to determine whether Ethernet remains the preferred network interface.

This exercise reinforced the importance of verifying the actual active interface rather than making assumptions based on physical connectivity.

---

## `arp -a` — Examine My ARP Cache

I used:

```powershell
arp -a
```

This allowed me to examine IP-to-MAC mappings currently stored by my computer.

### My Observation

arp -a — Examining My ARP Cache

I used:

arp -a

to examine the ARP cache maintained by my computer.

My Observation

When I investigated the ARP cache, I observed that IP-to-MAC address mappings could appear as either dynamic or static entries.

This provided a preliminary view of the Layer 2 information currently known by my computer and helped me visualize the relationship between an IPv4 address and the MAC address associated with a device or interface on the local network.

The exercise also generated an important investigative question for me:

How can I verify that a MAC address shown in the ARP table actually belongs to the physical device I believe is using the corresponding IP address?

That question helped move the exercise beyond simply reading command output. An ARP entry provides evidence of an IP-to-MAC association known by my computer, but additional information may be required to confidently associate that MAC address with a specific physical device.

Possible sources of additional evidence could include:

Router or DHCP client information
Device network settings
Manufacturer information associated with the MAC address
Asset inventories in an enterprise environment
Switch MAC address tables in a managed network

I enjoyed this exercise because it demonstrated how a simple command can become the starting point for a larger network investigation.

---

## Identifying My Default Gateway

I compared the default gateway shown by:

```powershell
ipconfig
```

with the corresponding entry in:

```powershell
arp -a
```

This demonstrated how my computer maintains an IP-to-MAC mapping for the gateway interface it uses for local Ethernet communication.

### My Observation

Identifying My Default Gateway

I compared the default gateway identified through:

ipconfig

with the corresponding IP address in:

arp -a
My Observation

I successfully identified my default gateway and located its corresponding dynamic IP-to-MAC mapping in the ARP table.

This helped reinforce my understanding that the default gateway is normally the Layer 3 device my computer sends traffic toward when the destination is outside my local subnet.

In my home network, that gateway functionality is provided by my router.

The exercise also helped connect two pieces of information:

Default Gateway IP
        ↓
      ARP
        ↓
Gateway Interface MAC Address

My computer knows the gateway by its IP address for Layer 3 communication, but when communicating with that gateway across the local Ethernet/Wi-Fi network, it also needs the appropriate Layer 2 MAC address.

This helped me see how Layer 2 and Layer 3 work together rather than operating as completely separate processes.

---

## Generating Traffic and Observing ARP

I generated traffic to a known device on my own network using:

```powershell
ping <device-IP>
```

and then checked:

```powershell
arp -a
```

again.

### My Observation

Generating Traffic and Observing ARP

For the next exercise, I selected a device that I own on my local network and identified the IP address it was using over Wi-Fi.

From my PC, I generated traffic to that device using:

ping <device-IP>
My Observation

The device successfully responded to the ping:

Packets: Sent = 4, Received = 4, Lost = 0

After generating the traffic, I immediately examined my ARP cache again using:

arp -a

This time I found a dynamic IP-to-MAC mapping for the device I had just contacted.

This was an especially useful exercise because I was able to observe ARP behavior rather than only reading about it.

Before communicating with another local IPv4 device, my computer needs the Layer 2 information required to deliver Ethernet/Wi-Fi frames on the local network. If the required IP-to-MAC mapping is not already available in the ARP cache, ARP can be used to resolve it. The resulting mapping can then be temporarily cached.

My observation demonstrated the process:

Known Device IP
       ↓
Generate Network Traffic
       ↓
Local IPv4 Communication Requires MAC Address
       ↓
ARP Resolution if Mapping Is Needed
       ↓
IP-to-MAC Mapping Cached
       ↓
Dynamic Entry Visible with arp -a

This was a valuable real-world demonstration of a concept I had previously studied theoretically.

It also reinforced why an ARP table does not necessarily contain every device currently connected to the network. My computer does not need to maintain an ARP entry for every connected device; entries are learned and cached as local communication requires them and can later age out.

---

## `netstat -e` — Examine Ethernet Statistics

I used:

```powershell
netstat -e
```

to review Ethernet interface statistics including:

* Bytes
* Unicast packets
* Non-unicast packets
* Discards
* Errors

### My Observation

netstat -e — Examining Ethernet Statistics

Lastly, I used:

netstat -e

to examine Ethernet interface statistics maintained by Windows.

The output included information such as:

Bytes
Unicast packets
Non-unicast packets
Discards
Errors
My Observation

This exercise helped me understand that network communication can also be examined from a statistical perspective rather than only by looking at individual connections or IP addresses.

The Bytes counters provided an indication of the amount of network data transmitted and received, while the packet counters helped demonstrate how Ethernet traffic can be categorized.

I also learned the distinction between:

Unicast traffic — traffic intended for a specific destination.
Non-unicast traffic — traffic involving broadcast or multicast communication.

This connected directly with my previous study of VLANs because broadcasts are normally contained within a Layer 2 broadcast domain.

The Discards and Errors fields were also important from a troubleshooting perspective. Unexpected increases in these counters could provide evidence that further investigation of an interface or network connection is warranted.

Although netstat -e provides relatively simple statistics, it demonstrated another important cybersecurity lesson:

Network analysis is often about establishing what normal activity looks like so that unusual behavior can later be recognized.

---

# SOC Investigation Scenario

I analyzed the following simulated switch-security event:

```text
09:14:02  Gi0/14 learned MAC 00-AA-01
09:14:02  Gi0/14 learned MAC 00-AA-02
09:14:03  Gi0/14 learned MAC 00-AA-03
09:14:03  Gi0/14 learned MAC 00-AA-04
09:14:04  Gi0/14 learned MAC 00-AA-05
09:14:04  Gi0/14 learned MAC 00-AA-06

09:14:06  PORT SECURITY VIOLATION
```

## Initial Assessment

Seeing numerous MAC addresses rapidly appear on a single switch interface would be unusual if that interface were expected to support only one workstation.

A **MAC flooding attack** would therefore be one hypothesis worth investigating.

However, I should not immediately conclude that malicious activity occurred.

Legitimate explanations could include:

* A virtualization host
* A downstream switch
* An IP phone with a workstation connected through it
* Other approved network infrastructure

As an analyst, I would gather additional evidence by reviewing:

1. Switch logs
2. Port configuration
3. Asset inventory
4. 802.1X/NAC authentication records
5. Endpoint activity
6. Historical behavior for that switch interface

The evidence should determine whether the activity represents legitimate network behavior, a configuration issue, or malicious activity.

---

# Why This Matters to Security Analysts

Today's lesson reinforced that network security requires understanding **normal network behavior before identifying abnormal behavior**.

A SOC analyst reviewing a Layer 2 alert should be able to ask:

```text
What happened?

What device generated the activity?

What should normally be connected to this port?

Is this behavior expected?

What security control detected it?

What additional evidence supports or contradicts my hypothesis?
```

This evidence-driven approach is important because a security alert represents something that requires investigation—it does not automatically prove that an attack occurred.

---

# My Reflection

Today's exercise connected several networking concepts I previously studied.

ARP taught me how an endpoint determines the MAC address associated with an IPv4 address on its local network.

VLANs demonstrated how Layer 2 networks can be separated into broadcast domains.

Today's switch-security lesson showed me how switches learn MAC addresses, how attackers may attempt to manipulate those mechanisms, and how controls such as port security, 802.1X, NAC, and VLAN segmentation can reduce the risk.

One of my biggest takeaways is that I need to distinguish between technologies that may initially appear similar.

For example:

```text
ARP Cache
IP → MAC

Switch MAC Table
MAC → Port

ARP Poisoning
Manipulates IP-to-MAC associations

MAC Flooding
Targets switch MAC/CAM table behavior
```

Understanding these distinctions is more useful than simply memorizing terminology because it helps me determine **where a problem is occurring, what evidence I should look for, and which defensive control could mitigate it**.

---

# Key Exam Takeaways

## Network+

* Ethernet switches primarily make forwarding decisions using MAC addresses.
* MAC addresses operate at Layer 2 of the OSI model.
* ARP resolves IPv4 addresses to MAC addresses on the local network.
* Switches maintain MAC/CAM tables mapping MAC addresses to switch ports.
* VLANs create separate Layer 2 broadcast domains.
* Port security can restrict which MAC addresses are permitted on switch interfaces.
* 802.1X provides port-based network access control.

## Security+

* Layer 2 attacks can target trusted network mechanisms.
* MAC flooding attempts to manipulate or exhaust a switch's MAC/CAM table.
* ARP poisoning manipulates IP-to-MAC associations.
* Port security can help prevent unauthorized devices and certain Layer 2 attacks.
* Disabling unused network ports reduces attack surface.
* 802.1X and NAC can require authentication before granting network access.
* Physical security and network security work together as part of defense in depth.

## CySA+

* Multiple MAC addresses rapidly appearing on an unexpected switch port can warrant investigation.
* An alert is evidence requiring analysis, not automatic proof of compromise.
* Analysts should establish expected behavior before identifying anomalies.
* Switch logs, asset inventories, NAC/802.1X records, and endpoint telemetry can be correlated during Layer 2 investigations.
* Security analysis should follow an evidence-driven process:

```text
Alert
  ↓
Establish Context
  ↓
Develop Hypothesis
  ↓
Gather Corroborating Evidence
  ↓
Determine Scope
  ↓
Reach Conclusion
```

---

# Final Takeaway

Today's practice moved my understanding from simply knowing **how Layer 2 networking works** toward understanding **how Layer 2 behavior can be monitored and defended**.

The progression of my networking practice is becoming increasingly connected:

```text
ARP
 ↓
MAC Addressing
 ↓
Switching
 ↓
VLAN Segmentation
 ↓
Port Security
 ↓
802.1X / NAC
 ↓
Monitoring
 ↓
Security Investigation
```

This foundation will help me not only prepare for Network+, Security+, and CySA+, but also develop the analytical mindset required for SOC and cybersecurity analyst responsibilities.
