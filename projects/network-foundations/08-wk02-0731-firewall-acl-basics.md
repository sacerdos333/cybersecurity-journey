# My Understanding of the Valuable Foundations of Networking

## What Is a Firewall?

In plain language, a firewall acts as a security checkpoint for a host or network. It examines network traffic and determines whether packets should be allowed, blocked, or logged according to established security rules.

Firewalls can protect individual computers, network boundaries, cloud environments, and specific applications. Their inspection capabilities depend on the type of firewall and how it is configured.

Two foundational firewall types are:

* **Stateless firewall:** Evaluates each packet independently using information such as source and destination IP addresses, port numbers, and protocols.
* **Stateful firewall:** Tracks active connections and uses the state and context of a session when deciding whether traffic should be permitted.

Modern environments may also use next-generation firewalls that provide application awareness, intrusion prevention, threat intelligence, and deeper inspection of network traffic.

## What Is an Access Control List (ACL)?

An Access Control List (ACL) is an ordered list of rules used to permit or deny traffic based on specified conditions. These conditions may include:

* Source IP address
* Destination IP address
* Source or destination port
* Network protocol
* Traffic direction
* Interface or network location

ACLs can be compared to traffic signs on a highway. For example, one sign may prohibit trucks from entering a side street, while another may require certain vehicles to stop at a railroad crossing.

ACL rules are normally evaluated from top to bottom. The first matching rule determines whether the traffic is permitted or denied, and processing stops. Therefore, a broad rule placed too early may override a more specific rule located below it.

This concept is important because ACL rule order and the **first-match principle** are frequently tested on certification exams.

Many ACLs also include an **implicit deny** at the end. If traffic does not match an earlier permit rule, it is automatically denied, even when a final deny rule is not visibly displayed.

Two common security approaches are:

* **Default deny:** Block all traffic unless it is explicitly permitted.
* **Default allow:** Permit all traffic unless it is explicitly blocked.

Default deny is generally more secure because it follows the principles of least privilege and minimizes unnecessary exposure.

## Why Do Secure Firewalls and ACLs Matter to Security Analysts?

SOC analysts regularly investigate firewall events. Understanding firewall behavior is essential when reviewing logs, analyzing suspicious activity, and determining whether a connection represents legitimate traffic or a potential attack.

Questions an analyst should ask include:

* Why was this connection blocked or allowed?
* Which firewall or ACL rule processed the traffic?
* What were the source and destination IP addresses?
* Is the destination expected for this system or user?
* Which port and protocol were involved?
* Was the connection initiated internally or externally?
* Did the event occur once, or is it part of a repeated pattern?
* Does the traffic indicate normal activity, a configuration error, reconnaissance, or an attack?

Firewall logs can help analysts identify port scanning, unauthorized connection attempts, malware command-and-control traffic, policy violations, and improperly configured rules.

## My Output from Common Command-Line Tools

### `netsh advfirewall show allprofiles` — Check Windows Firewall Status

I opened Command Prompt as an administrator and ran:

```powershell
netsh advfirewall show allprofiles
```

I found three Windows Firewall profiles:

* **Domain:** Used when the computer is connected to and authenticated with an Active Directory domain.
* **Private:** Used for trusted networks, such as a properly secured home network.
* **Public:** Used for untrusted networks, such as public Wi-Fi.

For each profile, I observed:

* Firewall state
* Default inbound action
* Default outbound action
* Logging configuration
* Location of the firewall log file

The active profile matters because the same firewall rule may be enabled for one profile but disabled for another.

### Windows Defender Firewall with Advanced Security — View Firewall Rules

I opened **Windows Defender Firewall with Advanced Security** and reviewed the available inbound and outbound rules.

I observed the following:

* Rule names
* Enabled and disabled rules
* Allow and block actions
* Associated firewall profiles
* Protocols and port numbers
* Authorized programs and services

I selected a rule and asked:

* What application or service does this rule support?
* Is it an inbound rule or an outbound rule?
* Which profiles does it apply to?
* Why might the rule exist?
* Is its scope appropriately restricted?
* Would disabling the rule disrupt a legitimate service?

I reviewed the rules without modifying or disabling any of them.

### `netstat -abno` — Observe Listening Services

I opened Command Prompt as an administrator and ran:

```powershell
netstat -abno
```

I observed:

* Active connections
* Listening ports
* Executable names
* Process identifiers (PIDs)
* Local and remote addresses
* TCP connection states

I compared the listening services with the Windows Firewall rules and considered which services should be reachable from the network and which should remain protected.

A listening port does not automatically mean that the service is accessible from another system. A host firewall, network firewall, ACL, or another security control may still block inbound traffic to that port.

### Visit `https://github.com` — Verify a Secure Connection

After visiting `https://github.com`, I ran:

```powershell
netstat -ano | find ":443"
```

I observed active connections involving TCP port 443, which is commonly associated with HTTPS traffic.

This task reinforced how legitimate outbound HTTPS traffic can be permitted while the firewall continues to enforce security policy. It also demonstrated that port 443 identifies the service being used, but encryption does not automatically guarantee that all traffic using that port is trustworthy.

## Thinking Like a Cybersecurity Analyst — Investigative Challenge

I considered a hypothetical inbound TCP connection that was blocked on port **3389**, which is commonly associated with Microsoft Remote Desktop Protocol (RDP).

I asked the following investigative questions:

* Was the source an internal or external IP address?
* Was the source address expected and authorized?
* Is Remote Desktop enabled on the target system?
* Should this system accept RDP connections?
* Which firewall rule blocked the connection?
* Has the source attempted connections to other systems or ports?
* Were there repeated attempts over a short period?
* Does the activity resemble legitimate administration, reconnaissance, or a brute-force attack?
* Were any related authentication attempts successful?
* Is the destination system exposed directly to the internet?

A single blocked RDP attempt does not prove that an attack occurred. However, repeated attempts from an unfamiliar source, especially when directed at multiple hosts, may indicate scanning or an attempted intrusion.

The most important defensive principle is to allow only the access necessary for an approved business purpose. This principle is known as **least privilege**.

Role-Based Access Control (RBAC) supports least privilege by assigning permissions according to a person’s job responsibilities. However, RBAC and firewall rules operate at different layers:

* **Firewall rules and ACLs** control network traffic.
* **RBAC** controls what authenticated users or roles are authorized to access or perform.

These controls work together as part of a defense-in-depth strategy.

## What This Activity Helped Me Understand

### Why Are Stateful Firewalls Generally More Secure Than Stateless Firewalls?

Stateful firewalls are generally more secure because they maintain a state table and understand whether packets belong to an established or expected connection.

For example, when an internal system initiates an outbound connection, a stateful firewall can recognize the legitimate return traffic. An unsolicited inbound packet that does not belong to an established session can be blocked.

Stateless firewalls evaluate packets independently. They are fast and useful for straightforward filtering, but they have less context when evaluating traffic.

Stateful inspection provides stronger context-aware control, although it requires additional processing and memory and may be vulnerable to resource-exhaustion attacks against the state table.

### Why Is Rule Order So Important in an ACL?

ACLs are evaluated sequentially from top to bottom. When a packet matches a rule, the specified permit or deny action is applied, and the device normally stops evaluating the remaining rules.

A general rule placed before a specific rule can make the specific rule ineffective. For example, a broad deny rule at the beginning of an ACL could block legitimate traffic that a later rule was intended to permit.

ACLs should normally place more specific rules before broader rules. Administrators should also document, test, and periodically review them to identify shadowed, redundant, expired, or overly permissive entries.

### Why Is a Default-Deny Policy More Secure Than Default Allow?

A default-deny policy blocks access unless it has been explicitly authorized. This approach:

* Follows the principle of least privilege
* Reduces the attack surface
* Prevents unexpected services from being automatically exposed
* Requires administrators to identify legitimate business requirements
* Limits the effect of configuration mistakes and newly installed services

A default-allow policy creates more risk because traffic remains permitted unless someone identifies it and creates a rule to block it.

## Security Analyst Reflection

This activity helped me understand that a firewall is more than a device that simply blocks traffic. It is an enforcement point for security policy and an important source of evidence during an investigation.

I also learned that firewall effectiveness depends on the quality, order, scope, and maintenance of its rules. An overly broad allow rule can create unnecessary exposure, while an improperly ordered rule can block legitimate business traffic or make another rule ineffective.

As a future cybersecurity analyst, I must be able to connect firewall events with listening services, process identifiers, authentication records, endpoint activity, and network patterns. A blocked connection is only the beginning of the investigation. The larger context determines whether the event represents expected activity, a configuration problem, reconnaissance, or an attempted compromise.

## Key Exam Takeaways

* Firewalls can operate on individual hosts or at network boundaries.
* Stateless firewalls evaluate packets independently.
* Stateful firewalls track active sessions and connection state.
* ACLs are evaluated from top to bottom.
* The first matching ACL rule normally determines the action.
* Place specific ACL rules before broader rules.
* Many ACLs end with an implicit deny.
* Default deny is more secure than default allow.
* Inbound and outbound rules control different traffic directions.
* Windows Firewall uses Domain, Private, and Public profiles.
* A listening port does not guarantee that the service is remotely accessible.
* TCP port 443 commonly supports HTTPS.
* TCP port 3389 commonly supports Microsoft RDP.
* Encrypted traffic is not automatically trustworthy.
* Repeated blocked connections may indicate scanning, brute-force activity, or another attempted intrusion.
* Firewall logs should be correlated with endpoint, authentication, DNS, and other network logs.
* Least privilege, RBAC, ACLs, and firewall policies are related controls, but they are not interchangeable.
* Rule reviews should identify overly broad, redundant, shadowed, expired, and unused rules.
* Defense in depth uses multiple security controls so that one failed control does not expose the entire environment.
