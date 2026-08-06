# Wireless Security Fundamentals

## My Understanding of Wireless Security

Today's cybersecurity practice expanded on my previous lessons involving Layer 2 security, VLANs, network segmentation, and 802.1X by focusing on how wireless networks authenticate users and protect data transmitted over radio waves.

Unlike wired Ethernet, wireless communication occurs through radio frequency signals that can be received by any device within range. Because physical access to a switch port is no longer required, strong authentication and encryption become critical security controls.

This lesson reinforced that wireless security is not simply about hiding a network or creating a password—it is about verifying identity, protecting communications, and preventing unauthorized access.

---

# Why Wireless Security Matters

A wired Ethernet network generally requires physical access to a switch port before a device can communicate on the network.

Wireless networking changes that model.

Any device within radio range can detect a wireless signal, making authentication and encryption the primary methods used to determine who should be allowed to communicate.

Without appropriate security controls, an attacker could attempt to:

* Connect to the wireless network.
* Capture unencrypted traffic.
* Impersonate a legitimate wireless network.
* Trick users into connecting to malicious infrastructure.

For this reason, enterprise wireless security combines multiple layers of defense including authentication, encryption, certificate validation, monitoring, and network segmentation.

---

# WPA2 vs. WPA3

## WPA2

Wi-Fi Protected Access 2 (WPA2) has been the enterprise standard for many years.

Its primary security features include:

* AES encryption
* Strong authentication options
* Support for both Personal and Enterprise deployments

Although WPA2 remains widely deployed, newer standards have improved upon it.

---

## WPA3

WPA3 is the latest generation of Wi-Fi security.

Compared with WPA2, WPA3 provides:

* Improved protection against password guessing attacks
* Stronger authentication mechanisms
* Better protection for modern wireless environments
* Enhanced cryptographic security

Although WPA2 remains common, organizations increasingly deploy WPA3 as newer hardware becomes available.

For certification purposes, I should remember:

```text
WPA2
↓
Secure wireless standard

WPA3
↓
Enhanced wireless security
↓
Improved authentication protection
↓
Greater resistance to password attacks
```

---

# Personal vs. Enterprise Authentication

Wireless authentication can generally be divided into two deployment models.

## WPA Personal (PSK)

Personal mode uses a **Pre-Shared Key (PSK)**.

Everyone connecting to the network knows the same password.

Advantages include:

* Easy setup
* Simple administration
* Ideal for home environments

Disadvantages include:

* Shared credentials
* Password changes affect every user
* Limited accountability

---

## WPA Enterprise

Enterprise mode authenticates each user individually.

Instead of everyone sharing one password, authentication is performed using technologies such as:

* 802.1X
* RADIUS
* Individual user credentials
* Certificates
* Multi-factor authentication (when implemented)

Conceptually:

```text
User
   ↓
802.1X Authentication
   ↓
RADIUS Server
   ↓
Network Access
```

Enterprise authentication provides significantly greater accountability because individual user activity can be associated with specific authenticated identities.

---

# 802.1X and RADIUS

Today's lesson connected directly with yesterday's study of switch security.

Whether authentication occurs on:

* A wired Ethernet switch
* A wireless access point

the authentication workflow is very similar.

The three major components are:

### Supplicant

The client requesting network access.

### Authenticator

The switch or wireless access point controlling access.

### Authentication Server

Usually a RADIUS server responsible for validating credentials.

Conceptually:

```text
Supplicant
      │
      ▼
Authenticator
      │
      ▼
RADIUS Server
```

This relationship is an important certification topic because it appears in both wired and wireless enterprise environments.

---

# AES Encryption

Authentication and encryption serve different purposes.

Authentication answers:

> "Who are you?"

Encryption answers:

> "Can anyone else read this communication?"

Enterprise wireless networks commonly use **AES (Advanced Encryption Standard)** to protect wireless traffic.

Conceptually:

```text
Authenticate
      ↓
Exchange Encryption Keys
      ↓
Encrypt Wireless Traffic
      ↓
Secure Communication
```

Even if someone intercepts encrypted wireless traffic, the encryption should prevent them from understanding the contents without the proper cryptographic keys.

---

# Common Wireless Threats

## Evil Twin Access Point

An Evil Twin is a malicious wireless access point designed to imitate a legitimate network.

Example:

```text
Legitimate
Office-WiFi

Malicious
Office-WiFi-Free
```

If users connect to the attacker's access point, the attacker may attempt to intercept credentials or network traffic.

---

## Rogue Access Point

A rogue access point is an unauthorized wireless device connected to an organization's network.

Unlike an Evil Twin, a rogue access point may actually have direct connectivity into the internal corporate network.

---

## Weak Passwords

Weak or commonly used passwords remain one of the simplest methods attackers use to compromise wireless networks.

Strong wireless security begins with strong authentication credentials.

---

# My Wireless Investigation

## `netsh wlan show interfaces`

I used:

```powershell
netsh wlan show interfaces
```

to examine the current status and security configuration of my active wireless connection.

### My Observation

This command provided a significant amount of information about my wireless connection and helped me understand that Wi-Fi involves much more than simply being "connected" to a network.

My observations included:

* **Authentication Method:** WPA2-Personal
* **Cipher:** CCMP
* **Signal Quality:** 62%
* **RSSI:** -72 dBm
* **Receive Rate:** 576 Mbps
* **Transmit Rate:** 576 Mbps
* **Radio Type:** 802.11ax (Wi-Fi 6)

While reviewing the output, several fields stood out because I wanted to understand what they represented rather than simply record their values.

One field that caught my attention was the **AP BSSID**. I learned that while the **SSID** is the wireless network name that users recognize, the **BSSID** is the unique MAC address of the wireless access point's radio. Multiple access points can broadcast the same SSID while each maintains its own unique BSSID. This becomes important in enterprise environments where users roam between access points without changing wireless networks.

I also noticed that my wireless connection was using **WPA2-Personal**, even though my router supports WPA3. This raised an investigative question that I plan to explore further by reviewing my router's wireless security configuration. My current hypothesis is that the router may be configured for WPA2 compatibility or operating in a WPA2/WPA3 transition mode.

Another unfamiliar field was the **Cipher**, which displayed **CCMP**. I learned that CCMP is the protocol that applies **AES encryption** to protect wireless communications. This reinforced an important security concept: authentication determines **who** is allowed to connect to the network, while encryption protects **the confidentiality of the data** being transmitted after authentication succeeds.

I also observed that my **Receive Rate** and **Transmit Rate** changed each time I executed the command. Initially, I thought this was unusual, but I learned that these values represent the negotiated wireless link rate between my laptop and the access point. They naturally fluctuate as the wireless environment changes based on factors such as signal strength, interference, network activity, and radio conditions. These values should not be confused with my Internet service speed.

Finally, I investigated the difference between the **Signal Quality** field and **RSSI**. During this lab, my signal quality measured approximately 62%, while the RSSI measured approximately -72 dBm. I learned that Windows presents Signal Quality as an easy-to-understand percentage, while RSSI is a direct measurement of the received wireless signal strength. Because RSSI values are measured in negative dBm, values closer to zero indicate stronger signals. An RSSI of **-72 dBm** generally represents a usable wireless connection and is consistent with my signal quality, especially considering my router is located in the basement while I was working from my first-floor office.

Overall, this exercise taught me that wireless networking involves many measurable characteristics beyond simply connecting to a Wi-Fi network. More importantly, it reinforced that cybersecurity analysts should investigate what individual fields represent and how they relate to one another rather than simply memorizing command output.

---

## `netsh wlan show profiles`

I used:

```powershell
netsh wlan show profiles
```

to review the wireless profiles currently stored on my computer.

### My Observation

> ## `netsh wlan show profiles`

I used:

```powershell
netsh wlan show profiles
```

to review the wireless network profiles currently stored on my Windows computer.

### My Observation

This command showed that my computer has **three saved wireless profiles**. One of the profiles corresponded to my mobile phone, which reminded me that Windows stores wireless profiles for networks it has successfully connected to in the past, including mobile hotspot connections. This was a useful reminder that saved wireless profiles are not limited to traditional wireless routers.

Initially, I believed that the remaining profiles represented both my Ethernet and Wi-Fi connections. After reviewing the command more carefully, I learned that **`netsh wlan show profiles` displays only wireless (WLAN) profiles**. Ethernet connections are managed separately by Windows and do not appear in this command's output. This reinforced the importance of understanding exactly what a command is reporting before drawing conclusions.

While reviewing the saved profiles, I began thinking like a security analyst and asked an important question:

> **How do I determine which wireless profiles are still needed and which ones should be removed?**

I learned that Windows does not automatically identify profiles as "active," "old," or "unused." Instead, this requires a manual review based on my own network usage. Questions I should ask include:

* Do I still use this wireless network?
* Do I still own this device or hotspot?
* Will I reasonably connect to this network again?
* Is this profile from a temporary location such as a hotel, airport, conference, or coffee shop?

If the answer is **no**, removing the saved profile is generally a good security practice because it reduces unnecessary trusted network entries on my computer.

This exercise also led me to think about **Evil Twin attacks**. At first, I believed that simply having many saved wireless profiles could create an Evil Twin vulnerability. After further study, I refined my understanding.

The number of saved profiles is not the security issue by itself. Rather, the risk comes from **continuing to trust wireless networks that are no longer necessary**. If an attacker creates a fraudulent wireless network using the same SSID as one of my previously trusted networks, there is a greater chance that I could mistake it for the legitimate network if I am not paying close attention.

This reinforced an important cybersecurity principle:

> **Security is not only about protecting systems—it is also about carefully managing trust.**

Periodically reviewing and removing wireless profiles that are no longer needed helps reduce unnecessary trust relationships and encourages me to be more intentional about the networks my computer recognizes and attempts to connect to.

Overall, this exercise helped me understand that saved wireless profiles represent a history of trusted network relationships. As a future cybersecurity analyst, I should periodically review those relationships just as I would review user accounts, firewall rules, or other trusted security configurations.

---

## `netsh wlan show profile`

I used:

netsh wlan show profile name="<profile>"

I examined the detailed configuration of my active wireless profile.

### My Observation

I examined both wireless profiles currently stored on my computer and noticed that although they contained many similar settings, the security capabilities advertised by each profile were different.

Initially, I believed the profiles might represent separate wireless frequency bands such as 2.4 GHz and 5 GHz. After further investigation, I learned that the differences actually reflected supported authentication and encryption combinations rather than radio frequencies.

One profile primarily contained WPA2-Personal authentication using CCMP and GCMP encryption, while the second profile supported a much broader collection of security capabilities including WPA3-Personal, GCMP-256, GCMP, CCMP, and WPA2 compatibility.

This helped me understand that a wireless profile stores much more than the name of a wireless network. It also records security capabilities and connection preferences that Windows can use when negotiating a secure connection with a wireless access point.

One of my biggest takeaways from this exercise was learning to distinguish between three different concepts:

The wireless adapter's hardware capabilities
The saved wireless profile
The security settings actually negotiated during the active wireless connection

Although my wireless adapter supports modern technologies such as WPA3 and Wi-Fi 6E, my current connection was still negotiated using WPA2-Personal with CCMP encryption. This demonstrated that the final connection depends upon the capabilities of both the client and the wireless access point rather than the client hardware alone.

This investigation reinforced that cybersecurity analysts should understand not only what technologies are supported but also what technologies are actually being used during a live network connection.

---

## `ipconfig /all`

I compared my wireless and Ethernet adapters.

### My Observation

One of the first things I noticed was that both adapters possessed their own unique IPv4 and IPv6 addresses.

Initially, I questioned why the same computer would require multiple IP addresses. Through this investigation, I realized that each network interface operates as an independent Layer 3 device with its own Layer 2 MAC address and Layer 3 IP addressing.

Although both adapters belong to the same computer, they represent separate communication paths into the network.

I also observed that each adapter maintained its own:

- MAC address
- IPv4 address
- IPv6 address
- DHCP lease
- Default gateway
- DNS configuration

This reinforced my understanding that Windows treats each network interface independently, allowing either Ethernet or Wi-Fi to become the active network connection depending on configuration and availability.

This exercise connected many of the networking concepts I have recently studied including Layer 2 addressing, Layer 3 addressing, DHCP, ARP, and routing.

Rather than viewing Ethernet and Wi-Fi as competing technologies, I now understand that they are simply different physical methods of reaching the same network infrastructure.

---

# SOC Investigation

## Observations

Users report repeated wireless authentication prompts.

Additional evidence includes:

- Strong wireless signal
- Unexpected default gateway
- Invalid certificate
- Multiple authentication failures

## Possible Hypotheses

- Evil Twin access point
- Misconfigured access point
- Expired wireless certificate
- Authentication server issue
- User configuration problem

## Evidence Supporting Each Hypothesis

The combination of an unexpected gateway and invalid certificate increases the likelihood that users may be connecting to an unauthorized wireless access point rather than the legitimate corporate network.

However, authentication failures alone do not prove an attack. Similar symptoms could occur if certificates have expired or wireless infrastructure has been misconfigured.

## Additional Evidence Needed

Before concluding that an Evil Twin attack has occurred, I would investigate:

- Wireless controller logs
- RADIUS authentication logs
- Certificate information
- DHCP lease assignments
- DNS records
- Client event logs
- Inventory of authorized wireless access points
- Physical location of the reported access point

## Most Likely Conclusion

Based on the currently available evidence, an Evil Twin attack is one possible explanation, but additional evidence is required before reaching a final conclusion.

## Confidence Level

Medium

## Reason

Although several indicators point toward a possible wireless impersonation attack, multiple legitimate infrastructure problems could produce similar symptoms. Additional evidence should be collected before declaring a security incident.

---

# My Reflection

Today's lesson became much more than a study of WPA2, WPA3, and wireless security terminology. What began as a review of enterprise wireless authentication evolved into a complete investigation of how my own computer establishes and secures a wireless connection.

By examining my wireless adapter capabilities, saved wireless profiles, and active wireless connection separately, I developed a much clearer understanding of how Windows negotiates a secure Wi-Fi connection. Rather than viewing the wireless connection as a single process, I now understand that it is built from several independent components working together.

One of my biggest breakthroughs today was recognizing the difference between:

- The capabilities of my wireless hardware
- The information stored in my saved wireless profiles
- The authentication and encryption actually negotiated during a live connection

This investigation also reinforced an important cybersecurity lesson: supported technologies and active technologies are not always the same. My wireless adapter supports WPA3, Wi-Fi 6E, and multiple enterprise security features, yet my current wireless connection successfully negotiated WPA2-Personal using CCMP encryption because that was the best mutually supported configuration between my computer and my wireless access point.

I also gained a much deeper appreciation for the information available through Windows networking commands. Rather than simply recording command output, I learned to investigate fields such as RSSI, Signal Quality, BSSID, authentication methods, encryption ciphers, and wireless capabilities to better understand how the entire system operates.

Perhaps the most valuable lesson from today was learning to build a mental model of wireless networking instead of memorizing isolated terms. As I continue preparing for Network+, Security+, and CySA+, I believe this systems-based approach will make future technologies much easier to understand because I will already know where they fit within the larger enterprise networking ecosystem.

---

# Key Exam Takeaways

## Network+

- WPA2 and WPA3 secure modern wireless networks.
- WPA3 provides stronger authentication protections than WPA2.
- WPA Personal uses a shared Pre-Shared Key (PSK).
- WPA Enterprise commonly uses 802.1X and RADIUS.
- AES is the underlying encryption algorithm used by modern wireless security.
- CCMP and GCMP are encryption protocols used with AES.
- Wireless adapters advertise supported capabilities, while active connections negotiate the security settings actually used.
- RSSI provides a direct measurement of received signal strength, while Signal Quality is a simplified Windows representation.
- BSSID identifies the unique MAC address of an individual wireless access point, while SSID identifies the wireless network name.

## Security+

- Authentication and encryption perform different security functions.
- Enterprise wireless authentication commonly relies on 802.1X and RADIUS.
- Evil Twin attacks attempt to impersonate legitimate wireless networks.
- Rogue access points create unauthorized network entry points.
- Saved wireless profiles represent trusted network relationships and should be periodically reviewed.
- Certificate validation is an important part of secure enterprise wireless authentication.

## CySA+

- Wireless investigations require evidence from multiple sources.
- Analysts should distinguish between adapter capabilities, saved profiles, and active connections.
- Strong wireless signals alone do not prove that users are connected to legitimate infrastructure.
- Invalid certificates, unexpected gateways, and authentication failures should be investigated together rather than independently.
- Security investigations should remain evidence-driven and avoid conclusions until sufficient supporting evidence has been collected.

---

# Final Takeaway

Today's investigation fundamentally changed the way I think about wireless networking.

Instead of viewing Wi-Fi as a simple connection between my computer and my router, I now understand that a secure wireless connection is the result of several technologies working together through a negotiation process.

```text
Wireless Adapter
        │
Hardware Capabilities
        │
        ▼
Saved Wireless Profile
        │
Connection Preferences
        │
        ▼
Authentication Negotiation
        │
        ▼
Wireless Access Point
        │
Mutually Supported Security
        │
        ▼
Active Connection
(Authentication, Cipher, RSSI, Signal Quality)
        │
        ▼
Secure Wireless Communication

```

Building this mental model helped transform today's lesson from memorizing terminology into understanding how secure enterprise wireless networking actually operates.

This investigation also reinforced an important principle that applies to cybersecurity as a whole:

> **Understanding how systems interact is far more valuable than memorizing the individual technologies they contain.**

As I continue my cybersecurity journey, I want to approach every new technology by asking not only *"What is it?"* but also *"Where does it fit?"* and *"How does it interact with the rest of the system?"*

I believe this systems-thinking approach will not only help me pass the Network+, Security+, and CySA+ certification exams, but also prepare me to investigate, troubleshoot, and secure real enterprise environments as a future cybersecurity analyst.
