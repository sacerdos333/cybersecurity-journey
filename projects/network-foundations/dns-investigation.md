## My Understanding of the Valuable Foundations of Networking

### What Is the Domain Name System (DNS)?

- My initial impression of what layer gets attacked most came to mind when I think about end users clicking on malicious hyperlinks or attachments in emails and text messages.
- It is clear now that this activity happens at the DNS because every internet connection depends on it.
- Two examples of DNS include:
  - Recursive Resolver which acts as a researcher to search for your request of an IP until it finds the answer
  - Authoritative Server which is the official source of who owns the DNS record for a domain 

### Why Does DNS Matter?

DNS is essential because once a computer knows where to send packets it needs to know the IP address of a specifically named webstie such as github.com

However, DNS is loved by hackers because there are so many methods of attacks.

Examples of DNS-based attacks include:

- ARP Tunneling
- DNS Cache Poisoning
- DNS Hijacking
- Fast Flux

### My Output from Common Command-Line Tools

#### `ipconfig /all - Check your DNS Servers`

- This command checks your DNS Servers.
- It looks for primary and possible secondary servers

#### nslookup - Resolve a website 

#### nslookup - set type=mx - Query a different record type

#### ipconfig /displaydns - View the DNS Cache

### What This Activity Helped Me Understand

#### How does DNS caching improve performance?

#### Why might DNS be an attractive target for attackers?
