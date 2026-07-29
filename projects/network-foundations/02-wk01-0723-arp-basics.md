## My Understanding of the Valuable Foundations of Networking

### What Is the Address Resolution Protocol (ARP)?

- The Address Resolution Protocol (ARP) allows a device to discover the Media Access Control (MAC) address associated with a known IPv4 address on the local network.
- This mapping enables the device to place an IP packet inside an Ethernet frame and deliver it to the correct device or next-hop router.
- ARP operates between Layer 2 (Data Link) and Layer 3 (Network) of the OSI model.

### Why Does ARP Matter?

ARP is essential because Ethernet uses MAC addresses to deliver frames on a local network, while IPv4 uses IP addresses for logical addressing and routing.

However, ARP does not include built-in authentication. A malicious device can send false ARP information and associate the attacker's MAC address with the IP address of another device, such as the default gateway.

Examples of ARP-based attacks include:

- ARP spoofing
- ARP poisoning

Successful ARP poisoning can enable:

- Man-in-the-middle (MITM) attacks
- Credential theft
- Network traffic interception
- Session hijacking
- Denial-of-service (DoS) attacks

### My Output from Common Command-Line Tools

#### `arp -a`

- This command displays the computer's current ARP cache.
- The ARP cache contains IPv4-to-MAC address mappings that the computer has recently learned or that were configured manually.
- Common entries include:

  - **Internet Address:** The IPv4 address, such as `192.xxx.x.x`
  - **Physical Address:** The corresponding MAC address, such as `xx-xx-xx-xx-xx-xx`
  - **Type:** Indicates whether the entry is dynamic or static

A **dynamic** entry is learned automatically and may expire or be refreshed. A **static** entry is manually configured or maintained more permanently.

#### `ipconfig`

- I used `ipconfig` to identify my computer's IPv4 address, subnet mask, and default gateway.
- I then compared the default gateway's IPv4 address with the entries displayed by `arp -a`.
- This allowed me to locate the MAC address associated with the default gateway on my local network.

#### `ping` and `arp -a` Used Together

- I used `ping` to generate network traffic and then ran `arp -a` to observe whether an ARP cache entry appeared or was refreshed.
- If the destination was on the same subnet, my computer needed the destination device's MAC address.
- If the destination was outside the local subnet, my computer needed the MAC address of the default gateway instead.
- Examining ARP entries before and after generating traffic can help demonstrate how ARP mappings are learned and refreshed.

#### `net view` and `hostname`

- The `hostname` command displays the name assigned to the local computer.
- The `net view` command attempts to display computers or shared resources that are discoverable on the local Windows network.
- My network restricted device discovery, so `net view` did not return results that I could document.
- This did not indicate that ARP was failing. Network discovery can be restricted by firewall settings, Windows services, sharing settings, or network security policies.

### What This Activity Helped Me Understand

#### Why Can't Ethernet Deliver Frames Using Only an IP Address?

Ethernet operates at Layer 2, the Data Link layer of the OSI model. At this layer, Ethernet delivers frames using source and destination MAC addresses.

IPv4 addresses operate at Layer 3, the Network layer, and are used for logical addressing and routing packets between networks. Before an IPv4 packet can be transmitted over an Ethernet network, the sending device must determine which destination MAC address to place in the Ethernet frame.

If the destination is on the same subnet, the frame is addressed to the destination device's MAC address. If the destination is on another network, the frame is addressed to the MAC address of the default gateway.

This demonstrates how the layers of the OSI model perform separate but interconnected functions:

- **Layer 2:** Local frame delivery using MAC addresses
- **Layer 3:** Logical addressing and routing using IP addresses

#### What Information Does ARP Provide That IP Alone Cannot?

An IPv4 address identifies a device's logical location, but Ethernet cannot use that IPv4 address by itself to deliver a frame on the local network.

ARP resolves a known IPv4 address to the corresponding MAC address required for local Ethernet delivery. The operating system then stores recently resolved IPv4-to-MAC address mappings in the ARP cache. Reusing these cached mappings reduces the need to repeatedly broadcast ARP requests, which helps limit unnecessary network traffic.

#### Why Is the Lack of Authentication in ARP a Security Risk?

ARP was designed with the assumption that devices on a local network could be trusted. It does not verify whether an ARP reply is legitimate or whether the responding device actually owns the IPv4 address it claims to own.

An attacker can exploit this weakness by sending forged ARP messages that associate the attacker's MAC address with the IPv4 address of a trusted device, such as the default gateway. If the false mapping is accepted, it can poison the target computer's ARP cache and redirect network traffic through the attacker's device.

This can enable:

- Man-in-the-middle (MITM) attacks
- Network traffic interception
- Credential or session theft
- Modification of data in transit
- Denial-of-service (DoS) attacks

Because ARP operates only on the local network, the attacker generally must already have access to the same local network or broadcast domain.

> **My key takeaway:** Developing a strong understanding of the OSI model and the function of each layer helps me follow data as it travels from one device to another and ultimately reaches an application or end user. It also helps me identify where vulnerabilities can occur, such as ARP spoofing at the boundary between Layers 2 and 3. As an aspiring cybersecurity analyst, my goal is to understand not only how each networking protocol works, but also the security risks and indicators of compromise associated with it.

### Key Exam Takeaways

- ARP resolves IPv4 addresses to MAC addresses on the local network.
- An ARP request is normally sent as a Layer 2 broadcast.
- An ARP reply is normally sent as a unicast response.
- ARP works with IPv4; IPv6 uses Neighbor Discovery Protocol (NDP) instead.
- Remote destination IP addresses are not resolved to their own MAC addresses across the internet.
- When sending traffic to a remote network, the computer resolves the default gateway's IPv4 address to the gateway's MAC address.
- ARP has no built-in authentication and is therefore vulnerable to spoofing and poisoning.
