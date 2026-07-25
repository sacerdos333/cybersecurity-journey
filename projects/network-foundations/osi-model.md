## My documentation of understanding the valuable foundations of networking.

### What the Open System Interconnection (OSI) model is:
- A layered approch to separating components of a system on a network

### Why does it matter:
- Using a layered approach helps to narrow in on problems during transmission.

### My Ouput from common command line tools
#### Ping google.com
- IP ADDRESS RETURNED WITH FOUR ICMP REPLIES - Pinging google.com IP... with 32 bytes of data:
  - Reply from IP...: bytes=32 time=14ms TTL=107
  - Reply from IP...: bytes=32 time=14ms TTL=107
  - Reply from IP...: bytes=32 time=14ms TTL=107
  - Reply from IP...: bytes=32 time=14ms TTL=107
- Ping statistics for IP...:
  - Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
- RESPONSE TIMES - Approximate round trip times in milli-seconds:
  - Minimum = 14ms, Maximum = 14ms, Average = 14ms
 
#### Tracert google.com
- Using this tool helped me identify hops made between the following:
  - My Router
  - My ISP
  - Intermediate Routers
  - Google's network
- Learning reflection - Each hop is another device forwarding packets. 

#### IPCONFIG /ALL
- Understanding the inner workings of my pc configuration and how it communicates along the layers of the OSI model involved the following:
  - IPv4 Address
  - Default Gateway
  - DNS Servers
  - MAC Address
- Learning reflection - I undertrand that these details will come up frequently in my work as an IT and Cybersecurity professional.

#### Installation and Packet Capture using Wireshark
- Capturing traffic while refreshing a web page allows me to see the following:
  - DNS
  - TCP
  - TLS
  - HTTP/HTTPS
- Learning reflection - gettting an understanding of how packets are traced helps to reinforce concepts like protocol names

### This Activity helped me understand the following introductory concepts:
#### Why Does Networking Use Layers Instead of One Giant Process?

Networking uses a layered structure to divide the complicated process of digital communication into smaller, more manageable functions. Each layer performs a specific role and interacts with the other layers through standardized interfaces.

This approach provides several important benefits:

* **Reduces complexity:** Separating networking into layers makes each function easier to study, design, implement, and manage.
* **Creates standard interfaces:** Clearly defined interactions allow hardware, software, and protocols from different vendors to work together.
* **Establishes a shared vocabulary:** Models such as the OSI model give network professionals, developers, vendors, and users a consistent way to describe network operations.
* **Supports independent development:** Technologies and protocols within one layer can often be updated without redesigning the entire communication process.
* **Improves troubleshooting:** Technicians can isolate a problem by examining the layer responsible for the affected function.

For example, when troubleshooting a failed network connection, a technician can move through the layers systematically—checking the physical connection, network addressing, transport behavior, and application services—instead of treating the network as one giant, interconnected process.

> **My understanding:** Layering makes networking modular. It allows a complex communication system to be understood and managed as a collection of specialized functions. This structure improves interoperability, scalability, technology development, and troubleshooting.

#### Source Consulted

Kumar, S., Dalal, S., & Dixit, V. (2014). *The OSI Model: Overview on the Seven Layers of Computer Networks*. **International Journal of Computer Science and Information Technology Research, 2**(3), 461–466. ISSN 2348-120X (Online). Published July–September 2014. Available through [Research Publish Journals](https://www.researchpublish.com/).

#### Which OSI layer do you think attackers target most often?
> I believe the application layer is one of the most frequently targeted OSI layers because it contains internet-facing services, processes user input, and provides access to valuable information. Attackers commonly exploit websites, email services, APIs, and other applications at this layer. Phishing also uses application-layer services as a delivery method, although the deception primarily targets the user rather than the network layer itself. Layers 3 and 4 are also frequently attacked through techniques such as IP spoofing, port scanning, SYN floods, and other denial-of-service attacks. Therefore, the most targeted layer depends on the attacker’s objective and how attack activity is measured.

#### What Surprised You Most About the `tracert` Output?

I was most surprised that `tracert` could identify many of the individual hops along the route and display their associated IP addresses. Before completing this exercise, I did not realize how many routers and other network devices might help move a packet from my home network to its destination.

Seeing each hop made me curious about what the devices were, who operated them, and what role they played in forwarding my traffic. For example, one of the early hops displayed the hostname `host1.uscdc.edu`, which appeared between my home router and equipment associated with my fiber internet provider. This unexpected hostname led me to investigate how router names are assigned and why a hostname may not clearly identify the device's current owner or purpose.

> **My key takeaway:** Internet communication is not a direct connection between my router and the destination. Packets may travel through numerous routers operated by an ISP, transit providers, hosting organizations, or other networks. Each hop represents another routing decision that helps move the packet toward its destination.

I also learned that `tracert` provides only a partial view of the route. Some devices may not respond, hostnames may be outdated or misleading, and the return traffic may follow a different path.
