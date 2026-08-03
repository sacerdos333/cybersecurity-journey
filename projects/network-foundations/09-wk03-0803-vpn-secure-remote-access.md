# VPNs and Secure Remote Access

## What Is a VPN?

Two important concepts:
- Encryption protects the confidentiality of the information.
- Encapsulation places the original network traffic inside another packet so it can traverse the VPN tunnel.

Security properties that we achieve are:
- Confidentiality
  - Someone intercepting the traffic should not be able to read it.
- Integrity
  - Someone shouldn't be able to secretly modify the traffic without detection
- Authentication
  - The VPN needs some method of determining "Are you actually authorized to establish this connection?"
  - Authentication can also include:
    - Username/password
    - Certificate
    - Pre-shared key
    - MFA

## Remote-Access vs. Site-to-Site VPN

### Remote-Access
One endpoint connects to an organization's network
Example:
> An employee working from home securely accessing internal resources

### Site-to-Site VPN
Individual employees don't necessarily establish the tunnel. The network gateways establish it. This will securely connect entire networks.
Remember this distinction:

Remote Access:
> User -> Network

Site-to-Site:
> Network -> Network

## IPsec

IPsec operates at the Network layer (Layer 3) and can protect IP communications.

## AH vs. ESP

AH — Authentication Header

Provides integrity/authentication capabilities, but does not provide encryption of the packet payload.

ESP — Encapsulating Security Payload

Can provide:
- Confidentiality
- Integrity
- Authentication

For exam purposes, if you're asked which IPsec component provides encryption, think:

> ESP

## Tunnel Mode vs. Transport Mode

Transport mode primarily protects the payload of the original IP packet [Original IP Header][Encrypted Payload]

Tunnel mode encapsulates the entire original IP packet and adds a new outer IP header. [NEW IP Header][Encrypted Original Packet]

Tunnel mode is particularly important with site-to-site VPNs

## Split Tunneling vs. Full Tunneling

### Full Tunnel

Everything travels through the VPN

Laptop
   │
   │ VPN
   ▼
Corporate Network
   │
   ├── Internal Resources
   │
   └── Internet

Security advantage the organization can inspect more of the user's traffic through corporate controls.

Disadvantage is that more bandwidth is consumed, higher latency, and larger VPN infrastructure load

### Split tunnel

Only corporate traffic used the VPN. Regular internet traffic goes directly through the user's normal connection.

                  ┌── Corporate Network
Laptop ──VPN──────┤
                  │
Laptop ───────────── Internet

Advantage is that VPN bandwidth is reduced and a better internet performance.

Security concern can be that an endpoint is simultaneously connected to a trusted corporate environment and an external network. Additional security considerations must be considered.


## My Command-Line Investigation

Performance of a before-and-after network investigation:

Step 1. -- Establish a baseline

Step 2. -- Establish a Route Baseline

Step 3. -- Check your Public IP

Step 4. -- Connect to your VPN

## > Step 1 - Establish a baseline

### ipconfig

Record IPv4 Address: XXX.XXX.....
Default Gateway: XXX.XXX....

### route print

I learned that in my route table the 0.0.0.0/0 route is my default route.

## > Step 2 - Establish a Route Baseline

### tracert github.com

Record:

- Number of Hops: 18
- First Hop: Router... [XXX.XXX...]

## > Step 3 - Check Your Public IP

Search What is my IP in favorite browser

Public IP: [Redacted]

## > Step 4 - Connect to Your VPN

Repeat IPCONFIG, ROUTE PRINT, and TRACERT GITHUB.COM

## VPN Off vs. VPN On Comparison

In the IP Config output the IPv4 Address changes when the VPN is on.

In Route Print output the IPv4 Route Table includes three new destinations when the VPN is on.

In TRACERT GitHub.com output the hops took longer to create in the output while the VPN was on. Also the first hop only showed the VPN configured IPv4 Address. The total number of hops was only 8 while the VPN was on. 

I checked my public IP while the VPN was on and the address was completely different

## Security Analyst Perspective

If a user reports that the internet is normal, but when I connect to the company VPN I cannot reach an internal server.

After checking VPN Status: Connected, Internet: Working, and Internal Server: UNREACHABLE - we can't assume the VPN is broken

Possible causes using layered troubleshooting:
- Missing route to the internal subnet
- Incorrect VPN routing policy
- DNS resolution problem
- Firewall rule blocking the connection
- ACL denying the user's subnet
- Authentication/authorization restrictions
- VPN gateway configuration problem

## My Reflection

### Question 1

What is the difference between encryption and encapsulation?

In short, encapsulation is about packaging (for transmission or key exchange), while encryption is about protecting the content from unauthorized access. Both often work together in secure protocols.

### Question 2

Why might an organization prefer full tunneling even though it consumes more bandwidth?

Full tunneling is preferred when maximum security, compliance, and centralized control are priorities, even if it means using more bandwidth. Split tunneling is better when performance and user experience are more important, but it requires careful policy design to avoid security risks

### Question 3

What security risk can split tunneling introduce?

While VPN split tunneling can enhance performance and convenience, it introduces significant security risks including data leakage, malware exposure, and bypassing corporate defenses. Careful configuration, selective routing, and strong endpoint security are essential to minimize these risks and maintain a secure network environment

### Question 4

If a user's VPN connects successfully but an internal application remains unreachable, why shouldn't you immediately blame the VPN?

In short, VPN connection status ≠ verified internal network path. The real issue is often in routing, DNS, firewall, or application configuration, not the VPN itself

## Key Exam Takeaways

| Network+                | Security+                 | CySA+                      |
| ----------------------- | ------------------------- | -------------------------- |
| VPN concepts            | Encryption in transit     | VPN log investigation      |
| IPsec                   | Secure remote access      | Routing analysis           |
| Routing                 | Authentication            | Log correlation            |
| Tunneling               | Split tunneling risks     | Connectivity investigation |
| Remote vs. site-to-site | Network security controls | Incident troubleshooting   |


