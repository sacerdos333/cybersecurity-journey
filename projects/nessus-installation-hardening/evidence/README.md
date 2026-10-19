# Nessus Installation & PKI Hardening — Evidence

## Purpose

This directory contains sanitized screenshots collected during my hands-on installation and security-hardening exercise using Tenable Nessus Essentials Plus on Ubuntu 22.04 LTS through Windows Subsystem for Linux (WSL2).

The screenshots provide supporting evidence for the installation, configuration changes, troubleshooting, and security validation procedures documented in the [main lab README](../README.md).

## Evidence Index

| File | Description |
|---|---|
| `01-environment-verification.png` | Verification of Ubuntu version, CPU architecture, WSL environment, and systemd status |
| `02-installer-integrity.png` | SHA-256 checksum verification against Tenable's published installer hash |
| `03-service-validation.png` | Confirmation that the Nessus service was successfully installed and running |
| `04-listener-hardening.png` | Verification that the Nessus management interface was restricted to `127.0.0.1:8834` |
| `05-pki-validation.png` | OpenSSL validation of the certificate chain and Subject Alternative Name extensions |
| `06-certificate-import.png` | Successful import of the custom HTTPS certificate into Nessus |
| `07-https-validation.png` | Final Nessus dashboard showing successful HTTPS access without certificate warnings |

*Evidence files will be added progressively as screenshots are reviewed and sanitized.*

## Evidence Handling and Security

Before publishing screenshots, I reviewed them for sensitive information and applied cropping or redaction where necessary.

Information intentionally excluded includes:

- Usernames and unnecessary workstation identifiers.
- License activation codes and account credentials.
- Passwords, private cryptographic keys, and authentication tokens.
- Personal contact information and residential addresses.
- Other information unnecessary to demonstrate the technical results.

Original, unredacted screenshots are retained privately and are not included in this public repository.

## Evidence Integrity

The screenshots represent actual observations from my personal cybersecurity lab. Redactions are limited to protecting sensitive information, and the relevant commands, results, and verification details are preserved.

Screenshots are intended to support the documented findings rather than independently establish that every security control has been exhaustively tested.

---

**Lab:** Nessus Essentials Plus Installation and HTTPS Security Hardening  
**Completed:** October 19, 2026  
**Evidence status:** In progress — screenshots under review
