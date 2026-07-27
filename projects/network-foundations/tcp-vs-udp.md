# My Understanding of the Valuable Foundations of Networking

## What Is the Transport Layer, Specifically TCP and UDP?

The Transport layer is Layer 4 of the OSI model. It manages end-to-end communication between applications and uses port numbers to direct network traffic to the correct service or process.

The two primary Transport-layer protocols are TCP and UDP:

- **Transmission Control Protocol (TCP)** is connection-oriented. It establishes a session using the three-way handshake:
  1. SYN
  2. SYN-ACK
  3. ACK
- TCP provides acknowledgments, sequencing, retransmission, flow control, and error recovery.
- **User Datagram Protocol (UDP)** is connectionless. It sends datagrams without first establishing a session or requiring acknowledgment of delivery.
- UDP is not limited to one-way communication. Two devices can exchange UDP traffic, but the protocol itself does not establish or track a connection.

## Why Does the Transport Layer Matter?

The Transport layer allows multiple applications to communicate across a network simultaneously. TCP and UDP use logical port numbers to direct incoming data to the appropriate application or service.

For example:

- An IP address identifies the destination device.
- A TCP or UDP port identifies the application endpoint on that device.
- A PID identifies the operating-system process using that endpoint.

TCP is best suited to communications requiring accuracy and reliable delivery. UDP is useful when speed and low overhead are more important than guaranteed delivery.

## My Output from Common Command-Line Tools

### `netstat -ano` — View Network Endpoints and Connections

The `netstat -ano` command displayed the following information:

- Protocol
- Local address and port
- Foreign address and port
- TCP connection state
- Process ID (PID)

I observed 43 TCP connections in the `ESTABLISHED` state. I also noticed that UDP port 5353 appeared frequently.

Unlike TCP entries, UDP entries do not normally display states such as `LISTENING` or `ESTABLISHED` because UDP does not establish and maintain connections.

### `netstat -an | findstr "LISTENING"` — View Listening TCP Ports

This command helped me identify TCP ports that were waiting to accept incoming connections.

During the investigation, I learned to ask:

> Which services should I reasonably expect to be listening on this workstation?

Two listening ports that appeared in the output were:

- **TCP 135:** Microsoft RPC Endpoint Mapper, which helps clients locate Windows Remote Procedure Call services.
- **TCP 445:** Server Message Block (SMB), which supports Windows file sharing, printer sharing, and other network services.

These ports may be normal on a Windows workstation, but they should be protected from untrusted networks and should not ordinarily be exposed directly to the public internet.

### `tasklist /FI "PID eq <PID>"` — Match a PID to a Process

After identifying a PID with `netstat`, I used `tasklist` to determine which process owned the network endpoint.

The output included:

- Image Name
- PID
- Session Name
- Session Number
- Memory Usage

This activity reinforced the relationship among a port, PID, and process:

- A **port** is a logical communication endpoint used by an application or service.
- A **PID** is the temporary numerical identifier Windows assigns to a running process.
- The **Image Name** identifies the executable associated with that process.

A port number does not represent the process itself. It identifies the network endpoint that the process is using.

### Observe Browser Connections with `netstat -ano`

I first ran `netstat -ano` to establish a baseline and recorded 662 network entries. I then opened a new browser window, visited `https://github.com`, and ran the command again. The second output contained 702 entries.

This increase demonstrated that opening a website can create multiple network endpoints and connections. However, I cannot assume that all 40 additional entries belonged exclusively to GitHub because browsers, background applications, and Windows services may create or close connections at the same time.

To identify GitHub-related traffic more precisely, I would need to compare the timestamps, destination IP addresses, PIDs, processes, and connection states from both observations.

## What This Activity Helped Me Understand

### Why Is TCP Considered More Reliable Than UDP?

TCP is considered more reliable because it provides:

- Connection establishment
- Sequence numbers
- Acknowledgments
- Retransmission of missing data
- Flow control
- Congestion control
- Ordered delivery

These capabilities make TCP appropriate for applications in which complete and correctly ordered data is important, including web browsing, email, remote administration, and file transfers.

UDP has less protocol overhead because it does not provide these reliability mechanisms. It is commonly used for applications such as real-time voice, video streaming, gaming, DHCP, and DNS, where speed or timely delivery may be more important than retransmitting every missing packet.

### Why Would DNS Often Use UDP Instead of TCP?

DNS commonly uses UDP port 53 because most DNS queries and responses are small. UDP avoids the additional overhead of establishing a TCP session, allowing DNS servers to answer requests efficiently.

DNS can use TCP port 53 when necessary, including:

- When a response is too large for the available UDP message size
- When a UDP response is returned with the **Truncated (TC) flag**
- During certain DNS zone transfers, such as AXFR and IXFR
- When reliability or a persistent connection is required

Therefore, DNS does not exclusively use UDP; it can use either UDP or TCP depending on the situation.

### Which Process or Port Surprised Me Most?

I was surprised to see UDP port 5353 appear so frequently. UDP 5353 is associated with **Multicast DNS (mDNS)**.

mDNS allows devices and applications to discover local services and resolve `.local` hostnames without querying a traditional DNS server. It may be used to locate printers, streaming devices, and other systems on the local network.

Seeing UDP 5353 is often normal, but mDNS can also reveal device names and advertised services. From a security perspective, unnecessary mDNS traffic can increase the available information and attack surface on a network.

## Reflection

This activity helped transform the Transport layer from an abstract OSI model concept into something I could observe directly on my Windows workstation. Before this investigation, I understood TCP and UDP mainly as protocols associated with reliability and speed. Using `netstat` and `tasklist` helped me see how those protocols connect to actual ports, processes, services, and applications.

One of my most valuable lessons was learning that a port is not the same thing as a physical Ethernet port or a running process. A TCP or UDP port is a logical endpoint, while a PID identifies the process currently using that endpoint.

I also learned that command output must be interpreted carefully. A large number of `netstat` entries does not necessarily mean that every entry is an active or suspicious connection. TCP entries may be listening, established, waiting to close, or in another state, while UDP endpoints do not maintain TCP-style connection states.

As I continue preparing for a cybersecurity analyst role, I want to become more comfortable distinguishing expected network activity from unusual activity and tracing a suspicious connection from its port and PID back to the responsible process.

## Key Exam Takeaways

- The Transport layer is **Layer 4** of the OSI model.
- **TCP is connection-oriented**, while **UDP is connectionless**.
- The TCP three-way handshake is **SYN → SYN-ACK → ACK**.
- TCP provides acknowledgments, sequencing, retransmission, flow control, and congestion control.
- UDP has lower overhead but does not guarantee delivery, order, or duplicate protection.
- A port number identifies a logical application endpoint; a PID identifies a running process.
- TCP and UDP use separate port-number spaces. TCP port 53 and UDP port 53 are distinct endpoints.
- Ports range from **0 through 65535**.
- Common port categories are:
  - **0–1023:** Well-known ports
  - **1024–49151:** Registered ports
  - **49152–65535:** Dynamic or private ports
- Common ports relevant to this investigation include:
  - **TCP/UDP 53:** DNS
  - **UDP 5353:** Multicast DNS
  - **TCP 135:** Microsoft RPC Endpoint Mapper
  - **TCP 445:** SMB
  - **TCP 443:** HTTPS
  - **TCP 22:** SSH
- DNS commonly uses UDP port 53 but can use TCP port 53 when needed.
- UDP entries do not normally show `LISTENING` or `ESTABLISHED` states because UDP does not establish sessions.
- A listening port is not automatically malicious; it must be evaluated according to the service, process, host role, firewall exposure, and expected baseline.
- `netstat -ano` can identify connections, endpoints, states, and PIDs.
- `tasklist /FI "PID eq <PID>"` can associate a PID with its executable.
- PIDs are temporary and may change when a process stops or restarts.

> **My key takeaway:** The Transport layer connects network communication to applications through TCP and UDP port numbers. By combining `netstat` with process information, I can begin determining which applications are communicating, whether their activity is expected, and which findings may require further investigation.
