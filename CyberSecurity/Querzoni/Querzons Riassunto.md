# Email Security

## Overview
- **Email** operates on a store-and-forward model using mail transfer agents (MTAs).
- **Components**:
  - Message User Agents (MUAs): Compose and access emails.
  - Mail Submission Agents (MSAs): Ensure message formatting standards.

## Transmission and Standards
- **SMTP (Simple Mail Transfer Protocol)**:
  - Manages email transmission.
  - Uses `user@domain` address format and message envelopes.
- **IMF (Internet Message Format)**:
  - Messages include headers (e.g., From, To, Subject, Date) and body.
- **Identifiers**: Message-ID and ENVID uniquely identify messages.
- **Access Protocols**: POP and IMAP for mailbox access.

### Process Example
1. Alice composes an email using her MUA.
2. The MSA determines the recipient’s mail exchange server via DNS.
3. The email is sent via SMTP to the recipient’s server (possibly through multiple MTAs).
4. Bob retrieves the email using POP3 or IMAP4.

### Extensions
- **ESMTP**:
  - Introduced via RFC 5321 (2008), replacing RFC 821 (1982).
  - **Greeting Command**: `EHLO`.
- **MIME (Multipurpose Internet Mail Extensions)**:
  - Supports various data types (e.g., text, images, audio, video).
  - Encodings: Base64 or quoted-printable for non-text content.
  - Features: Multipart types (mixed, digest, alternative) and character set support.

### Practices
- HTML bodies with plain-text alternatives.
- Subaddressing for flexible email aliases (e.g., `user+tag@domain.com`).
- **Domain Subaddressing**:
  - Enables aliases (e.g., `mastercourses@subdomain.domain.com`).

---

# Email Threats and Mitigations

## Threats
- Spam, phishing, malware, spoofing, data leakage, MITM attacks, BEC, and email bombing.

## Authentication Standards
1. **SPF**:
   - Verifies sending server's IP against the domain's SPF record.
   - Limitations: Fails with forwarding and shared services.
2. **DKIM**:
   - Uses cryptographic signatures to verify content integrity.
   - Enables key delegation via selectors.
3. **DMARC**:
   - Combines SPF and DKIM to prevent spoofing.
   - Actions: None, quarantine, or reject.
4. **ARC**:
   - Maintains authenticated status across intermediaries.

## Encryption
- **PGP**:
  - Provides confidentiality, authentication, and integrity.
  - Relies on a decentralized "web of trust."
- **S/MIME**:
  - Centralized PKI with X.509 certificates for key management.

---

# Network Security

## Intrusion Detection Systems (IDS)
- **Purpose**: Detect attacks compromising confidentiality, integrity, or availability.
- **Types**:
  - HIDS (Host-based): Monitors processes and file access.
  - NIDS (Network-based): Analyzes network traffic.
- **Methodologies**:
  - Misuse detection: Uses signatures of known attacks.
  - Anomaly detection: Flags deviations from normal behavior.
  - Compound detection: Combines both methods.

### Challenges
- High false-positive rates.
- Encrypted traffic analysis limitations.

### Architectures
- Centralized IDS: Simplifies management.
- Distributed IDS: Improves fault tolerance but is complex.

---

# Web Security

## Browser Security
- **Same-Origin Policy (SOP)**:
  - Restricts access to resources from the same origin (protocol, domain, port).
- **Cross-Origin Resource Sharing (CORS)**:
  - Relaxes SOP for controlled resource sharing.
  - Uses headers like `Access-Control-Allow-Origin`.

## Vulnerabilities and Countermeasures
1. **Path Traversal**:
   - **Prevention**: Input validation and static path lists.
2. **SQL Injection**:
   - **Prevention**: Prepared statements, whitelisting, and reduced database permissions.
3. **XSS (Cross-Site Scripting)**:
   - Types: Reflected, stored, DOM-based.
   - **Prevention**: CSP, Trusted Types, input sanitization.

## Cookies
- Attributes: Domain, path, Secure, HttpOnly, SameSite.
- Default: `SameSite=Lax`.

---

# Web Technologies

## HTTP/HTTPS
- Stateless protocol with methods like GET, POST, and HEAD.
- **HTTP/2**: Supports multiplexing, header compression, and server push.
- **HTTP/3**: Uses QUIC for reduced latency and improved error recovery.

## Modern Web Applications
- Shift to REST APIs and Single-Page Applications (SPAs).
- Tools:
  - **ngrok**: Exposes local servers to the internet.
  - **Burp Suite**: Web application security testing.

## Storage
- **Web Storage**: Larger capacity and better concurrency than cookies.
- **Cookies**: Used for authentication, personalization, and tracking.

---

# Summary

This document provides an overview of email security, network intrusion detection, web vulnerabilities, and modern web technologies. Key concepts include secure transmission standards (SPF, DKIM, DMARC), intrusion detection methodologies, CORS and CSP mechanisms, and advancements in HTTP protocols.
