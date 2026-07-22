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
- Why does networking use layers instead of one giant process?
- Which OSI layer do you think attackers target most often?
- What surprised you most about the tracert output?
