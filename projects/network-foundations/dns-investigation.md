# My Understanding of the Valuable Foundations of Networking

## What Is the Domain Name System (DNS)?

The Domain Name System (DNS) is a distributed naming system that translates human-readable domain names, such as `github.com`, into IP addresses that computers use to communicate.

Before studying DNS, I associated many cyberattacks with users clicking malicious hyperlinks or opening attachments in emails and text messages. I now understand that DNS often plays an important supporting role in these attacks. When a malicious link contains a domain name, DNS may resolve that name to an attacker-controlled IP address.

DNS operates at the Application layer of the TCP/IP model and is generally associated with Layer 7 of the OSI model.

Two important DNS components include:

* **Recursive resolver:** Receives a DNS request from a client and searches for the answer. It may respond from its cache or contact other DNS servers until it finds the requested record.
* **Authoritative name server:** Maintains the official DNS records for a domain and provides authoritative answers for those records.

A typical DNS resolution process may include the following steps:

1. The client sends a query to a recursive resolver.
2. The resolver checks its cache for an existing answer.
3. If necessary, the resolver contacts a root name server.
4. The root server directs it to the appropriate top-level domain server, such as the server responsible for `.com`.
5. The top-level domain server identifies the domain’s authoritative name server.
6. The authoritative server returns the requested DNS record.
7. The resolver caches the answer and returns it to the client.

## Why Does DNS Matter?

Computers exchange network traffic using IP addresses, but people generally remember names more easily than numerical addresses. DNS allows a user to enter a domain such as `github.com` while the computer obtains the IP address needed to reach that service.

DNS is essential to websites, email systems, cloud applications, software updates, and many other network services. If DNS becomes unavailable, users may be unable to locate services even when the destination systems remain online. If DNS answers are manipulated, users may be redirected to malicious systems without realizing that the destination has changed.

Attackers target and misuse DNS because it is essential, widely trusted, and commonly permitted through security controls.

Examples of DNS-related attacks and techniques include:

* **DNS tunneling:** Attackers encode command-and-control traffic or stolen data inside DNS queries and responses to bypass some security controls.
* **DNS cache poisoning:** False DNS information is inserted into a resolver’s cache, potentially directing users to attacker-controlled destinations.
* **DNS hijacking:** An attacker changes DNS settings or interferes with DNS resolution to redirect users to unintended or malicious destinations.
* **Fast flux:** Attackers rapidly change the IP addresses associated with a malicious domain, making the supporting infrastructure more difficult to identify and block.
* **DNS amplification:** An attacker uses spoofed DNS requests to cause large responses to be sent to a victim as part of a distributed denial-of-service attack.

## My Output from Common Command-Line Tools

### `ipconfig /all` — Identify Configured DNS Servers

The following command displays detailed configuration information for the computer’s network adapters:

```cmd
ipconfig /all
```

I used this output to identify the DNS servers assigned to my computer. A network adapter may have a preferred DNS server and one or more alternate servers for redundancy.

The listed server may be a local router, an organization’s internal DNS server, a public resolver, or a resolver assigned by a VPN. If the listed DNS server is my local router, the router may answer from its cache or forward the request to an upstream resolver.

<img width="914" height="499" alt="Output from ipconfig showing configured DNS server information" src="https://github.com/user-attachments/assets/0472f084-d3e1-4d82-b57a-8c4417cee89f" />

> **Security note:** Before publishing command-line screenshots, I should redact public IP addresses, usernames, device names, VPN identifiers, and other information that could reveal details about my network or identity.

### `nslookup` — Resolve a Domain Name

I used `nslookup` to resolve domain names and identify the DNS server that answered each request:

```cmd
nslookup github.com
nslookup microsoft.com
```

I compared the results for GitHub and Microsoft and repeated the lookups with my VPN enabled and disabled.

With the VPN enabled, `nslookup` displayed a resolver assigned by the VPN. With the VPN disabled, it displayed my local router as the DNS resolver or forwarding server. This demonstrated that enabling a VPN can change which DNS service handles my requests.

`nslookup` did not display the complete network route or the path through the VPN tunnel. Instead, it showed:

* The DNS server that handled the lookup
* The address of that DNS server
* The DNS answer returned for the requested domain

I also observed that the IP address returned for GitHub changed when I enabled the VPN. Distributed services may return different endpoints based on the DNS resolver, apparent geographic location, load balancing, availability, or other network conditions.

### `tracert` — Compare Visible Network Paths

I used `tracert` with the VPN enabled and disabled to compare the visible Layer 3 paths to GitHub:

```cmd
tracert github.com
```

With the VPN enabled, the trace displayed eight visible hops. Without the VPN, it displayed eighteen visible hops.

The VPN trace did not necessarily represent a physically shorter route. After traffic entered the encrypted VPN tunnel, many of the underlying routers carrying the traffic were concealed from the perspective of `tracert`. The results therefore represented visible logical hops—not a complete count of every physical network device involved.

I also observed that:

* The first VPN hop took approximately 8 milliseconds.
* The first non-VPN hop took approximately 2 milliseconds.
* The final response time was nearly the same in both tests.

The first non-VPN hop represented my nearby home router, which explains its relatively low response time. The first visible VPN hop represented a virtual gateway or tunnel endpoint and involved additional VPN processing.

The similar final response times suggested that the VPN exit location and subsequent route to GitHub were efficient. This experiment taught me that a higher hop count does not automatically mean greater latency.

> **Important:** The response times displayed for individual hops should not be added together. Each value is a separate round-trip measurement from my computer to that hop.

A `Request timed out` message at an intermediate hop does not necessarily indicate that the route failed. A router may continue forwarding traffic while refusing or limiting the ICMP responses used by `tracert`.

### `nslookup` with `set type=mx` — Query an MX Record

I used an interactive `nslookup` session to request mail exchanger records:

```cmd
nslookup
set type=mx
gmail.com
```

The output displayed MX preference values and mail exchanger hostnames for `gmail.com`.

An **MX record** identifies the mail servers responsible for receiving email for a domain. Its preference value helps sending systems determine which mail server should be attempted first. A lower preference number generally identifies the more-preferred mail server.

This exercise demonstrated that DNS stores more than website IP addresses. It also contains records used for email delivery, aliases, name-server delegation, reverse lookups, and other services.

### `ipconfig /displaydns` — View the Local DNS Cache

I used the following command to examine records cached by Windows:

```cmd
ipconfig /displaydns
```

The output contained an entry for:

```text
cc-api-data.adobe.io
```

The record included:

* **Record Type:** 5
* **Time to Live:** 15 seconds
* **Section:** Answer
* **CNAME Record:** A canonical destination hostname

Record type 5 identifies a **CNAME record**. This means that `cc-api-data.adobe.io` is an alias for another canonical hostname. The computer may then resolve that canonical hostname to obtain its IP address.

The 15-second Time to Live, or TTL, allows Windows to cache the answer briefly. A short TTL enables the service provider to change the destination relatively quickly for load balancing, failover, content delivery, or infrastructure maintenance.

The TTL controls how long the DNS answer may remain cached. It does not determine how long the application’s network connection remains active.

Additional fields included:

* **Data Length:** The size of the DNS record’s returned data—not the amount of application data downloaded.
* **Section:** The portion of the DNS response containing the record. `Answer` means the record was included as an answer to the query.
* **CNAME Record:** The canonical hostname to which the alias points.

## What This Activity Helped Me Understand

### Why Is DNS Considered a Critical Internet Service?

DNS allows users and applications to locate services using recognizable names. Websites, email systems, cloud platforms, authentication services, and software updates all commonly depend on DNS.

If DNS becomes unavailable, users may be unable to locate services even when the destination servers remain online. If DNS information is manipulated, users may be directed to malicious systems.

DNS is therefore essential to the availability, integrity, security, and usability of modern networks.

### How Does DNS Caching Improve Performance?

DNS caching stores previously resolved records for the duration of their TTL values. When a cached answer remains valid, the client or resolver can reuse it instead of repeating the complete resolution process.

Caching:

* Reduces DNS lookup time
* Improves website and application responsiveness
* Reduces traffic to upstream DNS servers
* Decreases the workload on authoritative servers
* Provides limited resilience during temporary upstream interruptions

TTL values must balance efficiency with freshness. A longer TTL improves caching efficiency but causes DNS changes to take longer to propagate. A shorter TTL permits faster changes but produces more DNS queries.

DNS caches should not be flushed routinely simply to improve performance. Flushing is more appropriate during troubleshooting or after identifying stale, incorrect, or potentially poisoned records.

### Why Is DNS an Attractive Target for Attackers?

DNS is attractive to attackers because it is essential, widely trusted, distributed, and commonly permitted through firewalls. Attackers may manipulate DNS answers, disrupt name resolution, redirect users, hide malicious infrastructure, establish command-and-control channels, or exfiltrate data.

Because legitimate network activity generates many DNS requests, malicious DNS traffic may be difficult to identify without effective logging, monitoring, and behavioral analysis.

Potentially suspicious DNS activity includes:

* Queries for newly registered or suspicious domains
* Large numbers of failed lookups
* Long or randomly generated subdomain names
* Unusually frequent DNS requests
* Requests sent to unauthorized external resolvers
* Unexpected changes to DNS configuration
* Domains that rapidly change their associated IP addresses
* Queries containing unusually large encoded strings

## Personal Reflection

This investigation helped me move beyond thinking of DNS as a simple tool for translating domain names into IP addresses. I now recognize DNS as a distributed system supporting websites, email, cloud applications, and many other network services.

Comparing `nslookup` and `tracert` with my VPN enabled and disabled demonstrated that different tools answer different investigative questions. `nslookup` showed which DNS resolver handled a request and which address it returned, while `tracert` showed the Layer 3 hops visible from my computer.

I was especially interested in how the VPN changed the DNS resolver, destination address, visible hop count, and first-hop response time. The experiment demonstrated that fewer visible hops do not necessarily mean fewer physical routers or better performance. It reinforced the importance of interpreting command output instead of drawing a conclusion from a single measurement.

Examining the Windows DNS cache provided a real example of a CNAME record and helped me understand how aliases and TTL values support flexible service delivery. These exercises strengthened my ability to connect networking concepts with the investigative responsibilities of a cybersecurity analyst.

## Key Exam Takeaways

### Network+

* DNS translates domain names into IP addresses and supports multiple record types.
* DNS generally uses UDP port 53 for standard queries.
* TCP port 53 is used when needed, including zone transfers and certain larger responses.
* A recursive resolver obtains answers for clients and may cache them.
* An authoritative name server maintains official records for a domain.
* TTL determines how long a DNS record may be cached.
* Common DNS records include:

  * `A` — Hostname to IPv4 address
  * `AAAA` — Hostname to IPv6 address
  * `CNAME` — Alias to canonical hostname
  * `MX` — Mail exchanger
  * `NS` — Authoritative name server
  * `PTR` — Reverse lookup
  * `TXT` — Text-based information and policies
  * `SOA` — Administrative information about a DNS zone
* `nslookup` and `ipconfig /displaydns` are useful DNS troubleshooting tools.
* `tracert` displays visible Layer 3 hops; it does not show the DNS resolution process.

### Security+

* DNS cache poisoning and hijacking can redirect users to malicious destinations.
* DNS amplification can support distributed denial-of-service attacks.
* DNS tunneling can conceal command-and-control traffic or data exfiltration.
* DNSSEC provides authentication and integrity protection for DNS data, but it does not encrypt DNS queries.
* DNS over HTTPS and DNS over TLS encrypt traffic between a client and compatible resolver.
* DNS filtering can prevent systems from resolving known malicious or prohibited domains.
* DNS configuration must be protected against unauthorized changes.

### CySA+

* DNS logs can contain indicators of compromise and evidence of malicious behavior.
* Analysts should establish a baseline of normal DNS activity before identifying anomalies.
* Long, encoded, or high-volume subdomain queries may indicate DNS tunneling.
* Repeated requests for algorithmically generated domains may indicate malware.
* Requests sent to unauthorized external resolvers may indicate evasion or a policy violation.
* DNS evidence should be correlated with endpoint, firewall, proxy, authentication, and threat-intelligence data.
* A suspicious DNS lookup alone does not prove malicious activity; context and supporting evidence are required.
* Sensitive network information should be redacted before screenshots or investigation results are published.

> **My key takeaway:** DNS is much more than a system for translating domain names into IP addresses. It is a critical and trusted component of network communication that attackers can manipulate or misuse. By comparing DNS and routing behavior with my VPN enabled and disabled, examining different record types, and reviewing the local DNS cache, I learned how to interpret command-line evidence and connect networking fundamentals to cybersecurity analysis.
