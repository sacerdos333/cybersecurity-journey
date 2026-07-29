# My Understanding of the Valuable Foundations of Networking

## What Is the Dynamic Host Configuration Protocol (DHCP)?

The **Dynamic Host Configuration Protocol (DHCP)** automatically assigns network configuration information to clients. Without DHCP, administrators would need to configure every computer and device manually. This approach would be inefficient and difficult to manage on a modern enterprise network.

DHCP can provide clients with several important settings, including:

- IP address
- Subnet mask
- Default gateway
- DNS server addresses
- Lease duration

DHCP commonly uses a four-step exchange known as the **DORA process**:

1. **Discover** — A client without a valid IP configuration broadcasts a `DHCPDISCOVER` message to locate available DHCP servers.
2. **Offer** — A DHCP server responds with a `DHCPOFFER` containing a proposed IP address, lease duration, and other network settings.
3. **Request** — The client broadcasts a `DHCPREQUEST` message to accept one of the offers and identify the DHCP server it selected.
4. **Acknowledge** — The selected server sends a `DHCPACK`, confirming the lease and authorizing the client to use the assigned configuration.

The Request step also informs other DHCP servers that their offers were not selected, allowing them to return those proposed addresses to their available pools.

> **Important protocol detail:** DHCP uses **UDP port 67** for servers and **UDP port 68** for clients.

## Why Does DHCP Matter to Security Analysts?

DHCP simplifies network administration, but its trusted and automated nature can also make it a target for attackers.

Common DHCP-related threats include:

- **Rogue DHCP server** — An unauthorized server provides clients with malicious or incorrect network settings.
- **DHCP starvation** — An attacker sends numerous DHCP requests using spoofed MAC addresses to exhaust the server's available address pool.
- **Traffic redirection** — A rogue server may assign an attacker-controlled default gateway or DNS server, potentially enabling traffic interception, phishing, or man-in-the-middle attacks.
- **Denial of service** — Exhausting the DHCP address pool may prevent legitimate clients from receiving valid network configurations.

Security controls that can reduce these risks include:

- DHCP snooping
- Switch port security
- Network access control
- Authorized-server monitoring
- DHCP traffic and lease-log analysis
- Proper network segmentation

DHCP snooping is especially important because it allows a managed switch to distinguish between **trusted ports**, where legitimate DHCP server messages are permitted, and **untrusted ports**, where unauthorized server responses can be blocked.

## My Output from Common Command-Line Tools

### `ipconfig /all` — View DHCP Information

I previously used this command in other networking labs. In this activity, I reviewed its output specifically to understand the fields that tell the story of the computer's current DHCP lease.

Important fields included:

- DHCP Enabled
- IPv4 Address
- Subnet Mask
- Default Gateway
- DHCP Server
- DNS Servers
- Lease Obtained
- Lease Expires

Reviewing these values can help an analyst determine whether a client received a valid configuration and whether the DHCP server, gateway, or DNS settings appear legitimate.

### `ipconfig /release` and `ipconfig /renew` — Obtain a New Lease

I first reviewed my IP configuration and recorded the lease date and time. I then used the following commands:

```powershell
ipconfig /release
ipconfig /renew
```

The release command surrendered the existing DHCP-assigned address. The renew command requested a new lease from a DHCP server. Afterward, I used `ipconfig /all` again and observed the updated lease information.

> **Operational caution:** Releasing an IP address temporarily interrupts network connectivity. This command should not be used casually on a remote system because it could disconnect the administrative session.

### `ping github.com` — Verify Connectivity

After releasing and renewing the DHCP lease, I used the following command:

```powershell
ping github.com
```

This test helped me verify that:

- The computer received a usable IP configuration.
- A route to the network was available.
- DNS could resolve `github.com` to an IP address.
- Internet connectivity had returned.
- Round-trip response times could be observed.

A successful ping to a hostname demonstrates both name resolution and basic IP connectivity. However, an unsuccessful ping does not always prove that the destination is unavailable because firewalls may block ICMP traffic.

## What This Activity Helped Me Understand

### Why Is DHCP Preferred Over Static IP Addressing in Most Enterprise Environments?

DHCP is preferred because it makes IP address allocation more scalable, consistent, and manageable. Administrators can centrally update settings such as DNS servers, default gateways, and lease durations without manually reconfiguring every endpoint.

Static IP addresses are generally appropriate for essential infrastructure that must remain consistently reachable, such as certain routers, firewalls, servers, and network-management systems.

A **DHCP reservation** provides a specific IP address to a device based on its MAC address. Reservations offer predictable addressing while preserving centralized DHCP management. The decision between static addressing and reservations depends on the organization's infrastructure requirements and network design.

### What Could Happen If a Rogue DHCP Server Is Introduced?

A rogue DHCP server could provide clients with incorrect or malicious configuration information. For example, it could assign:

- An attacker-controlled default gateway
- A malicious DNS server
- An incorrect subnet mask
- An invalid IP address
- A shortened or manipulated lease

These settings could disrupt connectivity, redirect users to fraudulent websites, intercept traffic, or support a man-in-the-middle attack.

Controls such as DHCP snooping, port security, network access control, and DHCP log monitoring can help detect or prevent unauthorized DHCP activity.

### Why Is the Request Step Necessary After Receiving an Offer?

The Request step allows the client to formally select an offered configuration. Because more than one DHCP server may respond to the Discover message, the client broadcasts its Request so that all responding servers can see which offer was accepted.

The selected server can then complete the lease, while the other servers withdraw their offers and return their proposed addresses to their available pools. This coordination helps prevent address conflicts and duplicate assignments.

## Troubleshooting Insight

If a Windows client cannot contact a DHCP server, it may automatically assign itself an **Automatic Private IP Addressing (APIPA)** address in the `169.254.0.0/16` range.

An address beginning with `169.254` is therefore an important troubleshooting clue. It commonly indicates that the client could not obtain a valid DHCP lease. The device may communicate with some hosts on the same local segment, but it will generally be unable to reach other networks through a default gateway.

## Reflection

This activity helped me understand that DHCP does much more than provide a convenient IP address. It supplies several settings that determine how a client communicates with local systems, reaches remote networks, and resolves domain names.

From a security analyst's perspective, DHCP information can help explain unusual connectivity, incorrect routing, suspicious DNS behavior, or a possible rogue server. Comparing the DHCP server, default gateway, DNS servers, and lease information against an organization's approved network configuration can reveal important indicators of misconfiguration or malicious activity.

My key lesson is that an automatically assigned configuration should not automatically be trusted. Analysts must know what normal DHCP behavior looks like so they can recognize when the configuration no longer matches the expected network baseline.

## Key Exam Takeaways

- DHCP automatically supplies IP addresses and other network settings.
- DORA stands for **Discover, Offer, Request, and Acknowledge**.
- DHCP uses **UDP port 67 for servers** and **UDP port 68 for clients**.
- The client initially uses broadcasts because it does not yet have a usable IP configuration or know the DHCP server's address.
- DHCP leases are temporary and must be renewed.
- A DHCP reservation associates a consistent IP address with a device's MAC address.
- DHCP starvation attempts to exhaust the available address pool.
- A rogue DHCP server can provide a malicious gateway or DNS server.
- DHCP snooping can block unauthorized DHCP server responses on untrusted switch ports.
- A Windows APIPA address in the `169.254.0.0/16` range commonly indicates DHCP failure.
- `ipconfig /all` displays DHCP server, lease, gateway, and DNS information.
- A successful hostname ping tests both DNS resolution and basic connectivity.
- A failed ping alone does not prove that a host is offline because ICMP may be filtered.
