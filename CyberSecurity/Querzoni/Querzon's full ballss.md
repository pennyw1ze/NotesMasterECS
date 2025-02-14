# Email Security 1

- Email, a system for exchanging digital messages across the internet, operates on a store-and-forward model using mail transfer agents (MTAs).
- Message user agents (MUAs) compose and access emails, while mail submission agents (MSAs) ensure messages meet format standards before delivery.
- The Simple Mail Transfer Protocol (SMTP) handles email transmission, using message envelopes and the "user@domain" address format.
- Messages follow the Internet Message Format (IMF), including headers (with fields like From, To, Subject, and Date) and a body.
- Message-ID and ENVID uniquely identify messages.
- For accessing mailboxes, users employ protocols like POP and IMAP.
- Alice composes an email and sends it using her Mail User Agent (MUA) and the Submission Protocol.
- The Message Submission Agent (MSA) determines the recipient's mail exchange server using DNS.
- The message is then sent via SMTP to the recipient's mail exchange server, potentially through multiple MTAs.
- Finally, Bob retrieves the message using his MUA and either POP3 or IMAP4.
- RFC 5321 (2008) replaced ESMTPPRFC 821 (1982), introducing ESMTP, an extended SMTP version.
- Software should use ESMTP, but SMTP is supported for backward compatibility.
- EHLO is the ESMTP greeting command.
- MIME, an internet standard, extends email formats to support various character sets, non-text attachments, and multipart bodies.
- It's used in almost all email and described in six RFCs, including RFC 822 and RFC 2045-2049. MIME supports text, images, audio, video, and other data types.
- Key MIME features include character set support, content type labeling, non-text content support, and compound document support.
- Non-ASCII characters are handled using charset parameters in the Content-Type header.
- MIME uses base64 or quoted-printable encoding for non-text content.
- Multipart subtypes include mixed, digest, message, and alternative.
- Modern email clients often use HTML for email bodies, often including a plain text version.
- Local subaddressing uses tags in the email address for mailbox aliases.
- The email, sent from Crisis 2024 to Leonardo Querzoni at querzoni@diag.uniroma1.it, concerns instructions for final paper submission.
- The email's subject is "Instructions for Final Paper Submission - CRiSIS 2024." The sender's address is crisis2024@easychair.org.
- RFC5233 Domain subaddressing allows the use of local names as domain subaddresses, which can be defined freely; for example, mastercourses@querzoni.diag.uniroma1.it is equivalent to querzoni@diag.uniroma1.it.

---

# Email Security 2

- Email systems face various threats, including spam, phishing, malware distribution, email spoofing, lack of traceability, data leakage, Man-in-the-Middle attacks, Business Email Compromise, and email bombing.
- Examples of email spoofing are provided, showing emails seemingly from "Polizia criminale" and "*SAPIENZAN*," sent to Leonardo Querzoni.
- This email, sent from gaia.fiore@uniroma1.it to leonardo.querzoni@uniroma1.it with the subject "QUESTA AZIONE È OBBLIGATORIA," discusses email security challenges.
- These challenges include a lack of sender authentication and end-to-end encryption.
- The email mentions solutions like Sender Policy Framework (SPF), DomainKeys Identified Mail (DKIM), Domain-based Message Authentication, Reporting & Conformance (DMARC), and Authenticated Receiver Chain (ARC) to address these issues.
- **SPF (Sender Policy Framework)** verifies the sending server's IP address against the domain's SPF record, allowing domain owners to specify authorized mail servers.
  - A successful SPF check confirms the sender's authorization, while failure may lead to rejection or flagging of the email.
  - However, SPF has limitations, including potential failures with email forwarding, shared services, mailing lists, and size restrictions.
  - It doesn't protect email content or fully verify the sender's address in the header.
- **DKIM (DomainKeys Identified Mail)** cryptographically signs email messages, verifying content integrity and domain authentication through DNS-published public keys.
  - The signing process adds a DKIM-Signature header, and verification uses the published public key.
  - DKIM uses selectors to subdivide key namespaces, supporting multiple public keys per signing domain.
  - Selectors might identify office locations, signing dates, or users.
  - This allows for delegating signing capabilities, enabling local message sending for travelers, and supporting "affinity" domains forwarding mail without their own MSA.
  - Recipient mail servers verify DKIM signatures using the specified selector to retrieve the public key from DNS, checking the signature against the message content.
  - A valid signature confirms message integrity and authenticity; an invalid signature indicates alteration or a key mismatch, potentially leading to rejection or quarantine depending on other factors like DMARC policy.
- SPF authenticates the sending server's IP address, while DKIM verifies email content using a public key.
- DKIM doesn't authenticate the visible "From" address and can be broken by email forwarding that alters content.
- **DMARC** is a standard combining SPF and DKIM to protect against spam and spoofing.
  - It allows organizations to publish email authentication practices and specify actions for failed authentication checks, using policies like "none," "quarantine," or "reject."
  - DMARC checks DKIM signature validation, SPF record compliance, and domain alignment.
  - Receiving servers report outcomes to the sending domain, providing aggregate and forensic reports.
  - Strict DMARC policies can block legitimate messages due to indirect mailflows altering messages or IP addresses.
- **Authenticated Received Chain (ARC)** maintains the authenticated status of emails traversing multiple servers.
  - It ensures that successful SPF, DKIM, and DMARC checks at the origin are preserved.
  - Each intermediary adds its authentication results, creating a traceable chain of custody.
  - This improves email deliverability, especially for emails processed by mailing lists or forwarding services, by aiding in passing DMARC checks.
  - ARC complements, not replaces, DMARC.
  - Authenticated Received Chain (ARC) adds three header fields:
    - ARC-Authentication-Results.
    - ARC-Seal.
    - ARC-Message-Signature.
  - These verify email authenticity and integrity through intermediaries.
  - ARC doesn't address sender trustworthiness or message content; intermediaries might still alter content or headers.
  - ARC-Seal cryptographically signs ARC headers, while ARC-Message-Signature signs the entire message (excluding ARC-Seal).
  - Intermediaries add ARC headers only when changes might affect DMARC checks.
  - Verification involves checking the ARC-Seal and ARC-Message-Signature signatures, Authentication Results, and the entire ARC chain.
- **PGP (Pretty Good Privacy)** uses strong cryptography to provide confidentiality, authentication, and integrity for emails, employing public and private keys and a "web of trust" for key management.
  - Individuals gather and share digital signatures, expecting recipients to trust some signers.
  - This creates a decentralized, resilient network of trust for public keys.
- **S/MIME (Secure/Multipurpose Internet Mail Extensions)** offers similar assurances to PGP but employs a centralized PKI for key management and X.509 certificates instead of simple key pairs.
  - This approach offers less user control but improved interoperability.

---
# Network Intrusion Detection Systems (IDS)

---

## Overview
- **No security system is completely intrusion-proof.**
- Intrusion detection involves monitoring and analyzing system events for signs of attacks that compromise:
  - **Confidentiality**, **Integrity**, or **Availability**.

---

## Attack Classification
- **Types of Attacks**:
  - Denial of Service (DoS).
  - Probing/scanning.
  - Compromises.
  - Viruses, worms, Trojan horses, rootkits.
  - Man-in-the-Middle (MITM).
- **Other Dimensions**:
  - Network connections involved.
  - Source and environment.
  - Automation level.

---

## IDS Components
- **Core Components**:
  - Sensors.
  - Analysis engines.
  - Response units.

- **Key Characteristics**:
  - **Timely Detection**: Processing, propagation, and response times are crucial.
  - **Fault Tolerance**: Must remain robust even during distributed denial-of-service (DDoS) attacks.
  - **High Accuracy**: High detection rates with minimal false alarms.

---

## Monitoring Systems
1. **Host-based IDS (HIDS)**:
   - Monitors host events like processes and file access.
   - Techniques: Code analysis, log analysis.
   - **Drawbacks**: Lacks context and may impact system performance.

2. **Network-based IDS (NIDS)**:
   - Monitors network traffic, deployable inline or passively.
   - **Limitation**: Cannot analyze encrypted traffic.

3. **Log File Monitoring**:
   - Access to unique information.
   - **Challenge**: Requires complex tuning.

4. **Wireless Network Monitoring**:
   - Faces challenges due to:
     - Broadcast nature.
     - Imprecise boundaries.

---

## Detection Methodologies
- **Misuse Detection**:
  - Identifies known attacks using:
    - Signatures.
    - Rules.
    - Machine learning.
  - **Limitation**: Ineffective against zero-day attacks.

- **Anomaly Detection**:
  - Flags deviations from normal behavior using:
    - Statistical methods.
    - Outlier detection.
    - Distance-based analysis.

- **Compound Detection**:
  - Combines misuse and anomaly detection for broader coverage.

---

## IDS Architectures
1. **Centralized IDS**:
   - Simplifies management.
   - **Limitation**: Lacks scalability.

2. **Distributed IDS**:
   - Offers better fault tolerance and customization.
   - **Challenge**: Higher complexity in management.

---

## IDS Reactions
- **Alarm Notifications**: Alerts administrators about potential threats.
- **Non-Destructive Actions**:
  - Patching.
  - Firewall rule injections.
  - Dropping malicious packets or blocking connections.

---

# Network Security

---

## General Principles
- **No network security is completely intrusion-proof.**
- **Segmentation**:
  - Divides networks into internal and external zones with controlled checkpoints.
  - **Limitation**: Border defenses are vulnerable to internal breaches.

---

## Zero Trust Model
- Assumes no user or device is inherently trustworthy.
- Verifies access continuously and enforces **least privilege access**.
- **Core Components**:
  - Identity and Access Management (IAM).
  - Device security.
  - Network segmentation.
  - Data protection.
  - Continuous monitoring.

---

## Advanced Segmentation Techniques
1. **Microsegmentation**:
   - Provides finer-grained control at the application or workload level.
   - **Ideal for**: Zero Trust architectures.

2. **VLANs**:
   - Implement logical segmentation at OSI Layer 2.

---

## Security Information and Event Management (SIEM)
- **Purpose**:
  - Unifies security information from various sources.
  - Provides a comprehensive view for incident detection and response.
- **Benefits**:
  - Helps with regulatory compliance via logs and reports.
  - Provides contextual knowledge and actionable intelligence.

---

# Web Technologies

---

## HTTP and HTTPS
- **HTTP**:
  - Stateless protocol with methods like GET, POST, and HEAD.
- **HTTP/2**:
  - Improves page load speed with features like:
    - Multiplexing.
    - Header compression.
    - Server push.
  - Addresses HTTP/1.x's head-of-line blocking issue.
- **HTTP/3**:
  - Based on QUIC, uses UDP instead of TCP.
  - Reduces overhead and improves error recovery.

---

## Client-Side and Server-Side Technologies
- **Client-Side**: HTML, CSS, JavaScript.
- **Server-Side**: Python, NodeJS, Java, C#, PHP.

---

## Modern Web Development
1. **Single-Page Applications (SPAs)**:
   - Dynamically update pages without full reloads.
   - **Limitation**: Can hinder code inspection.
2. **REST APIs**:
   - Serve static resources and data in JSON format.
   - Clear separation of frontend and backend.
3. **WebSockets**:
   - Overcome HTTP limitations for continuous interactions.
   - Enable push notifications.

---

## Web Storage
- **Cookies**:
  - Attributes: Domain, path, Secure, HttpOnly, SameSite.
  - Default: `SameSite=Lax`.
- **Web Storage**:
  - Provides larger capacity and better concurrency handling.

---

## Tools
- **ngrok**:
  - Makes local servers accessible over the internet.
  - Web interface at `http://127.0.0.1:4040`.
- **Burp Suite**:
  - A web application security testing platform.
  - Features: HTTP(S) interception, request repetition, brute-forcing.

---
Thanks to Galaxy AI (slides summarization) and ChatGPT (Markdown formatting)