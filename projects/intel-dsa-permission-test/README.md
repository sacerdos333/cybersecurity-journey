# Intel Driver & Support Assistant: Least-Privilege Browser Permission Lab

## Executive Summary

In this lab, I investigated whether Intel® Driver & Support Assistant (Intel DSA) required Norton Private Browser's **Apps on device** permission to scan a Windows workstation. I used a controlled block/allow comparison, reviewed the site's other browser permissions, hardened permissions that were not needed for the scan, and then repeated the scan.

The results showed the following relationship under the tested conditions:

- With **Apps on device** blocked, Intel DSA could not complete the scan.
- When the browser prompted during an intentional scan and I temporarily allowed **Apps on device**, Intel DSA completed the scan and displayed a green success/up-to-date status.
- The scan still worked after unrelated site permissions were hardened, indicating that those permissions were not required for this workflow.
- After testing, I restored **Apps on device** to **Ask** so future access requires an explicit decision.

This was a functional troubleshooting and least-privilege exercise on an authorized personal system. It was not a vulnerability scan, penetration test, or security audit of Intel, Norton, or Windows.

## Objectives

1. Determine whether Intel DSA needs browser-mediated access to its locally installed service.
2. Identify the minimum browser permission required for a driver scan.
3. Test whether hardening unrelated site permissions affects Intel DSA functionality.
4. Establish a safer repeatable process for future driver reviews.
5. Document the work as a public portfolio lab without exposing private or device-identifying information.

## Hypothesis

If Intel DSA depends on a locally installed component, blocking **Apps on device** should prevent the website from completing a scan. Temporarily allowing that permission during an intentional scan should restore the function, while unrelated browser permissions should remain blocked or at their privacy-preserving defaults.

## Environment

| Component | Lab context |
|---|---|
| Host | Personal Windows workstation |
| Browser | Norton Private Browser |
| Website | Intel Driver & Support Assistant on `www.intel.com` |
| Local component | Intel DSA application/service already installed |
| Primary permission | **Apps on device** |
| Test method | Controlled block/allow comparison followed by a hardened-configuration validation |

Exact hardware identifiers, Windows account information, device name, serial numbers, network details, browser profile data, and full software inventory are intentionally omitted.

## Scope and Safety Controls

- I performed the test only on a computer I own and administer.
- I verified the vendor domain before granting access.
- I changed browser site permissions individually rather than disabling security protections.
- I did not disable antivirus, firewall, browser safeguards, or Windows security controls.
- I did not install untrusted software or bypass access controls.
- I kept original screenshots and diagnostic information outside the public repository.
- I granted the device-access permission only for the intended scan and restored it to **Ask** afterward.

## Test Procedure

### Phase 1 — Establish the blocked condition

1. I confirmed that Intel DSA was installed on the workstation.
2. I opened the Intel DSA page in Norton Private Browser.
3. When `www.intel.com` requested access to other apps and services on the device, I selected **Block**.
4. I attempted the scan.
5. Intel DSA displayed: **“Sorry, something went wrong while trying to scan.”**

### Phase 2 — Test the required permission

1. I opened the site-specific settings for `www.intel.com`.
2. I changed only **Apps on device** to **Allow**.
3. I refreshed the Intel DSA page and repeated the scan.
4. Intel DSA completed the scan and displayed a green status indicating that the Intel drivers and software were up to date.

### Phase 3 — Harden unrelated permissions

1. I reviewed the remaining Intel site permissions instead of assuming that every default-allowed capability was necessary.
2. I blocked or retained privacy-preserving defaults for permissions that were unrelated to the driver-scan workflow.
3. I kept **Apps on device** set to **Ask** for normal use.
4. I started another Intel DSA scan to validate the hardened configuration.
5. The browser prompted for device access as expected.
6. I approved the request for that scan.
7. Intel DSA again displayed a green success banner, confirming that the hardened unrelated permissions did not prevent the scan.
8. I restored **Apps on device** to **Ask** after the test.

## Results

| Test condition | Observation | Interpretation |
|---|---|---|
| **Apps on device: Block** | Intel DSA reported a scan error | The browser page could not complete the expected local-service interaction while access was denied |
| **Apps on device: Allow** | The scan completed and displayed a green up-to-date result | The permission appears necessary for the tested browser-to-local-service workflow |
| Unrelated permissions hardened; **Apps on device: Ask** | The browser prompted during the scan; temporary approval produced another green success result | The unrelated permissions were not required for the tested scan, and just-in-time approval preserved functionality |
| Post-test state: **Ask** | Future scans require a new decision | Persistent access was removed while retaining a usable workflow |

## Finding and Risk Assessment

The test supports a functional dependency between the Intel website and a locally installed Intel DSA component. The requested permission expands the browser site's ability to communicate with an on-device service, so it deserves deliberate approval. However, the prompt itself is not evidence of a vulnerability, compromise, or malicious third-party connection.

For this workstation and test scope, I assessed the permission decision as a **low residual risk when properly controlled**:

- **Likelihood:** Low when the domain, certificate, software source, and purpose are verified.
- **Impact:** Low to moderate if similar access were mistakenly granted to an untrusted or spoofed site, depending on the local service and browser implementation.
- **Mitigation:** Keep the permission at **Ask**, approve it only during an intentional scan, use the legitimate vendor site, and return the setting to **Ask** after use.

This is a qualitative lab assessment, not a formal enterprise risk rating.

## Technical Analysis

The deny-then-allow comparison supports the hypothesis. The scan failed while device access was blocked and succeeded when that permission was granted. The later validation also showed that the scan still worked after unrelated permissions were hardened.

Because **Apps on device** was the relevant variable, the evidence is consistent with Intel DSA using browser-mediated communication with its local application or service. The test does not identify the exact process, interface, port, protocol, or internal authorization mechanism. Confirming those details would require additional evidence such as service logs, browser diagnostics, packet capture, or process monitoring.

## Least-Privilege Outcome

The final configuration balances security and usability:

- `www.intel.com` is not given persistent device access.
- **Apps on device** remains set to **Ask**.
- Access is approved only during an intentional Intel DSA scan.
- Unrelated site permissions remain blocked or at privacy-preserving defaults.
- The setting is reviewed after each maintenance session.

This follows least privilege, just-in-time access, explicit authorization, change validation, and secure-default principles.

## Evidence

Only sanitized derivatives should be stored in the public repository.

| ID | Suggested file | Evidence purpose |
|---|---|---|
| E1 | [`01-intel-desktop-app-installed.png`](evidence/01-intel-desktop-app-installed.png) | Confirms that the local Intel DSA component was installed |
| E2 | [`02-permission-prompt-sanitized.png`](evidence/02-permission-prompt-sanitized.png) | Records the device-access request and the **Allow**/**Block** choices |
| E3 | [`03-blocked-scan-error-sanitized.png`](evidence/03-blocked-scan-error-sanitized.png) | Records the failed scan after access was blocked |
| E4 | [`04a-blocked-apps-on-device-sanitized.png`](evidence/04a-blocked-apps-on-device-sanitized.png) | Confirms the blocked test condition |
| E5 | [`04b-allowed-apps-on-device-sanitized.png`](evidence/04b-allowed-apps-on-device-sanitized.png) | Confirms the temporary allowed condition |
| E6 | [`05-scan-success-sanitized.png`](evidence/05-scan-success-sanitized.png) | Records the successful scan and green status |
| E7 | `06-final-ask-setting-sanitized.png` | Recommended evidence showing the final **Apps on device: Ask** state |

## Limitations

- The work was performed on one Windows workstation and one browser configuration.
- The observed outcomes show a strong relationship under the tested conditions but do not prove Intel DSA's internal design.
- A refresh and session-state change may have contributed to recovery after the first permission change.
- I did not independently monitor the Intel service state during every test condition.
- I did not collect packet captures, process telemetry, service logs, or browser developer diagnostics.
- Browser labels, defaults, and permission behavior may change between versions.
- A green Intel DSA result confirms that the scan completed and reported status; it is not an independent audit of every driver.
- The lab did not assess the security of Intel's infrastructure, Norton Private Browser, or the underlying local service.

## CompTIA Skill Mapping

This portfolio lab demonstrates concepts associated with CompTIA Security+ and CySA+; it is not an official performance-based question and does not claim certification credit.

### Security+

- Applied least privilege and secure defaults.
- Evaluated a trust boundary between a website and a local service.
- Used controlled troubleshooting and post-change validation.
- Preserved browser and endpoint protections during testing.
- Sanitized evidence before public disclosure.

### CySA+

- Established a hypothesis and compared controlled test conditions.
- Collected and interpreted observable evidence.
- Assessed risk without overstating the finding as a vulnerability.
- Documented scope, limitations, mitigations, and residual risk.
- Communicated results in a repeatable, reviewer-friendly format.

## Maintenance Procedure

I will review drivers and updates on the 16th of each month.

1. Run Windows Update and review optional updates carefully.
2. Use each device manufacturer's trusted support tool or official support site.
3. Review browser, antivirus/security software, firmware, and important application updates.
4. Verify the domain and purpose before granting device-access permissions.
5. For Intel DSA, approve **Apps on device** only when the scan is intentionally running.
6. Restore **Apps on device** to **Ask** after the scan.
7. Record failures, unusual prompts, or changes in behavior without publishing sensitive diagnostic data.

## Key Takeaway

This lab showed that a permission prompt should be tested and understood rather than accepted permanently or denied without context. By blocking access, observing the failure, granting only the relevant permission, hardening unrelated permissions, confirming successful operation, and restoring the permission to **Ask**, I maintained functionality while reducing unnecessary exposure.

---

*Product and company names are used only to describe the lab environment. Intel® is a trademark of Intel Corporation or its subsidiaries. Norton is a trademark of its respective owner.*
