# Email security

• Email, a system for exchanging digital messages across the internet, operates on a store-and-forward model using mail transfer agents (MTAs).

• Message user agents (MUAs) compose and access emails, while mail submission agents (MSAs) ensure messages meet format standards before delivery.

• The Simple Mail Transfer Protocol (SMTP) handles email transmission, using message envelopes and the "user@domain" address format.

• Messages follow the Internet Message Format (IMF), including headers (with fields like From, To, Subject, and Date) and a body.

• Message-ID and ENVID uniquely identify messages.

• For accessing mailboxes, users employ protocols like POP and IMAP.

• Alice composes an email and sends it using her Mail User Agent (MUA) and the Submission Protocol.

• The Message Submission Agent (MSA) determines the recipient's mail exchange server using DNS.

• The message is then sent via SMTP to the recipient's mail exchange server, potentially through multiple MTAs.

• Finally, Bob retrieves the message using his MUA and either POP3 or IMAP4.

• RFC 5321 (2008) replaced ESMTPPRFC 821 (1982), introducing ESMTP, an extended SMTP version.

• Software should use ESMTP, but SMTP is supported for backward compatibility.

• EHLO is the ESMTP greeting command.

• MIME, an internet standard, extends email formats to support various character sets, non-text attachments, and multipart bodies.

• It's used in almost all email and described in six RFCs, including RFC 822 and RFC 2045-2049. MIME supports text, images, audio, video, and other data types.

• Key MIME features include character set support, content type labeling, non-text content support, and compound document support.

• Non-ASCII characters are handled using charset parameters in the Content-Type header.

• MIME uses base64 or quoted-printable encoding for non-text content.

• Multipart subtypes include mixed, digest, message, and alternative.

• Modern email clients often use HTML for email bodies, often including a plain text version.

• Local subaddressing uses tags in the email address for mailbox aliases.

• The email, sent from Crisis 2024 to Leonardo Querzoni at querzoni@diag.uniroma1.it, concerns instructions for final paper submission.

• The email's subject is "Instructions for Final Paper Submission - CRiSIS 2024."  The sender's address is crisis2024@easychair.org.

• RFC5233 Domain subaddressing allows the use of local names as domain subaddresses, which can be defined freely; for example, mastercourses@querzoni.diag.uniroma1.it is equivalent to querzoni@diag.uniroma1.it.

# Email threats

• Email systems face various threats, including spam, phishing, malware distribution, email spoofing, lack of traceability, data leakage, Man-in-the-Middle attacks, Business Email Compromise, and email bombing.

• Examples of email spoofing are provided, showing emails seemingly from  "Polizia criminale" and "*SAPIENZAN*,"  sent to Leonardo Querzoni.

• This email, sent from gaia.fiore@uniroma1.it to leonardo.querzoni@uniroma1.it with the subject "QUESTA AZIONE È OBBLIGATORIA," discusses email security challenges.

• These challenges include a lack of sender authentication and end-to-end encryption.

• The email mentions solutions like Sender Policy Framework (SPF), DomainKeys Identified Mail (DKIM), Domain-based Message Authentication, Reporting & Conformance (DMARC), and Authenticated Receiver Chain (ARC) to address these issues.
• SPF (Sender Policy Framework) verifies the sending server's IP address against the domain's SPF record, allowing domain owners to specify authorized mail servers.

• A successful SPF check confirms the sender's authorization, while failure may lead to rejection or flagging of the email.

• However, SPF has limitations, including potential failures with email forwarding, shared services, mailing lists, and size restrictions.

• It doesn't protect email content or fully verify the sender's address in the header.

• DKIM (DomainKeys Identified Mail) cryptographically signs email messages, verifying content integrity and domain authentication through DNS-published public keys.

• The signing process adds a DKIM-Signature header, and verification uses the published public key.

• DKIM uses selectors to subdivide key namespaces, supporting multiple public keys per signing domain.

• Selectors might identify office locations, signing dates, or users.

• This allows for delegating signing capabilities, enabling local message sending for travelers, and supporting "affinity" domains forwarding mail without their own MSA.

• Recipient mail servers verify DKIM signatures using the specified selector to retrieve the public key from DNS, checking the signature against the message content.

• A valid signature confirms message integrity and authenticity; an invalid signature indicates alteration or a key mismatch, potentially leading to rejection or quarantine depending on other factors like DMARC policy.
• SPF authenticates the sending server's IP address, while DKIM verifies email content using a public key.

• DKIM doesn't authenticate the visible "From" address and can be broken by email forwarding that alters content.

• DMARC is a standard combining SPF and DKIM to protect against spam and spoofing.

• It allows organizations to publish email authentication practices and specify actions for failed authentication checks, using policies like "none," "quarantine," or "reject."  DMARC checks DKIM signature validation, SPF record compliance, and domain alignment.

• Receiving servers report outcomes to the sending domain, providing aggregate and forensic reports.

• Strict DMARC policies can block legitimate messages due to indirect mailflows altering messages or IP addresses.

• Authenticated Received Chain (ARC) maintains the authenticated status of emails traversing multiple servers.

• It ensures that successful SPF, DKIM, and DMARC checks at the origin are preserved.

• Each intermediary adds its authentication results, creating a traceable chain of custody.

• This improves email deliverability, especially for emails processed by mailing lists or forwarding services, by aiding in passing DMARC checks.

• ARC complements, not replaces, DMARC.
• Authenticated Received Chain (ARC) adds three header fields: ARC-Authentication-Results, ARC-Seal, and ARC-Message-Signature, to verify email authenticity and integrity through intermediaries.

• ARC doesn't address sender trustworthiness or message content; intermediaries might still alter content or headers.

• ARC-Seal cryptographically signs ARC headers, while ARC-Message-Signature signs the entire message (excluding ARC-Seal).

• Intermediaries add ARC headers only when changes might affect DMARC checks.

• Verification involves checking the ARC-Seal and ARC-Message-Signature signatures, Authentication Results, and the entire ARC chain.

• PGP, using strong cryptography, provides confidentiality, authentication, and integrity for emails, employing public and private keys and a "web of trust" for key management.

• Individuals will gather and share digital signatures, expecting recipients to trust some signers.

• This creates a decentralized, resilient network of trust for public keys.

• Email security, using S/MIME, offers similar assurances to PGP but employs a centralized PKI for key management and X.509 certificates instead of simple key pairs.

• This approach offers less user control but improved interoperability.

# IDS

• No security system is completely intrusion-proof.

• Intrusion detection involves monitoring and analyzing system events for signs of attacks compromising confidentiality, integrity, or availability.

• Attacks are classified by type (Denial of Service, probing/scanning, compromises, viruses/worms/Trojan horses/rootkits, and Man-in-the-middle), network connections involved, source, environment, and automation level.

• Intrusion Detection Systems (IDS) use sensors, analysis engines, and response components to detect intrusions.

• Desired IDS characteristics include timely detection, fault tolerance, and high detection rates with low false alarm rates.

• Intrusion Detection Systems (IDS) utilize ROC curves to balance detection and false alarm rates.

• A key characteristic of effective IDSs is fault tolerance; they must be robust against attacks, including distributed denial-of-service (DoS) attacks, to maintain service.

• Timeliness, encompassing processing, propagation, and response times, is also crucial.

• IDS vulnerability is demonstrated by the example of attackers using numerous false attacks to mask a real intrusion.
• Intrusion Detection Systems (IDSs) can monitor various systems: hosts (HIDS), networks (NIDS), log files, and wireless networks.

• HIDS monitors host events, including processes and file access, using techniques like code analysis and log analysis, but lacks context and may impact performance.

• NIDS monitors network traffic, deployable inline or passively, but cannot analyze encrypted data.

• Log file monitoring provides access to unique information but requires complex tuning.

• Wireless network monitoring faces challenges due to broadcast nature and imprecise boundaries.

• IDSs can also monitor alerts from other IDSs for improved detection.

• Detection methodologies include misuse detection (signature-based, rule-based, state transition analysis, machine-learning) and anomaly detection (programmed, self-learning).

• Intrusion detection systems use anomaly detection methodologies.

• Rule-based methods characterize normal behavior with rules, flagging rule violations as suspected attacks.

• Statistical methods monitor variables over time, detecting threshold exceedances based on averages and standard deviations;  more advanced techniques like Bayesian inference are also used.

• Outlier detection identifies data points significantly different from a stochastic distribution model.

• Distance-based methods address difficulties in estimating multidimensional distributions by computing distances between data points and analyzing local neighborhood densities.
• Intrusion Detection Systems (IDS) use anomaly detection, profiling normal behavior, and immune system-inspired approaches to identify deviations.

• Compound detection maintains models for both normal and abnormal behaviors to classify events.

• Misuse-based detection focuses on known attacks, while anomaly-based detection targets deviations from the norm; compound-based detection combines both.

• Online IDS tools analyze data streams for timely attack detection, requiring strong processing power, while offline tools perform post-analysis of audit data.

• Centralized IDS architectures simplify management but lack scalability, whereas distributed architectures offer better fault tolerance and customization.

• IDS reactions range from alarming administrators to employing non-destructive actions like patching or firewall rule injection.

• Actions include dropping malicious packets, blocking connections, and rate limiting.

# IDS attacks

• No network security is completely intrusion-proof.

• Intrusion detection monitors systems for attacks compromising confidentiality, integrity, or availability.

• Attacks are classified by type (Denial of Service, probing/scanning, compromises, viruses/worms/Trojan horses/rootkits, Man-in-the-middle), network connections involved, source, environment, and automation level.

• Intrusion Detection Systems (IDS) aim for high detection rates and low false alarm rates,  while maintaining fault tolerance and quick recovery from attacks to ensure dependable service.

• Key performance indicators for IDS include detection rate, false alarm rate, accuracy, and precision, often visualized using Receiver Operating Characteristics (ROC) curves.

• Intrusion Detection Systems (IDSs) have various aspects including timeliness, fault tolerance, and categorization based on monitored system, detection methodology, time aspects, architecture, and reaction type.

• Host-based IDSs (HIDS) monitor events within a single host, including network traffic, logs, and processes, often focusing on sensitive hosts or encrypted channels.

• Techniques used include code analysis, sandbox execution, and log analysis, but lack of context and performance impact are drawbacks.

• Network-based IDSs (NIDS) monitor traffic within network segments, often at network boundaries, analyzing data at various layers from application to lower layers.
• Intrusion Detection Systems (IDS) monitor various systems: networks (NIDS), logs of specific applications, wireless networks, and alerts from other IDSs.

• Network-based IDS can be inline or passive, but cannot monitor encrypted traffic.

• Log-based IDS access unique information but require complex tuning.

• Wireless IDS face challenges due to broadcast nature and imprecise network boundaries.

• IDS detection methodologies include misuse detection (signature-based, rule-based, state transition analysis, machine learning) and anomaly detection (programmed, self-learning, rule-based, statistical, distance-based, profiling).

• Compound detection uses models for both normal and abnormal behaviors.

• Misuse detection is ineffective against zero-day attacks, while anomaly detection has a higher false positive rate.

• Intrusion Detection Systems (IDS) utilize signature-based detection for known threats and anomaly-based detection for deviations from normal behavior.

• Compound-based detection combines both approaches, offering broad coverage but requiring both signature updates and baseline management.

• Online IDS tools analyze data streams for timely attack detection, demanding high processing power, while offline tools allow for more complex post-analysis of audit data.

• Centralized IDS architectures simplify configuration but are less scalable, whereas distributed architectures offer better fault tolerance and customization but are more complex to manage.

• Distributed agent-based IDSs are an area of ongoing research.
• Intrusion Detection Systems (IDS) primarily report alarms but can also react to attacks through patching, firewall rule injection, or increased sensor sensitivity.

• Network security employs segmentation, dividing networks into internal and external zones with controlled checkpoints.

• However, this "border defense" approach has limitations due to complex boundaries and potential internal breaches.

• Zero Trust, conversely, assumes no user or device is inherently trustworthy, verifying access continuously and using least privilege access.

• Key components include Identity and Access Management (IAM), device security, network segmentation, data protection, and continuous monitoring.

• Network segmentation, achieved physically or logically, divides networks into isolated domains, improving security and monitoring.

• Microsegmentation offers finer-grained control at the application or workload level, ideal for Zero Trust architectures.

• VLANs implement logical segmentation at OSI layer 2.  Security Information and Event Management (SIEM) systems unify security information from various sources, providing a comprehensive view for incident detection and response.

• The text describes a Security Information and Event Management (SIEM) system that aids in regulatory compliance by generating logs and reports.

• The SIEM provides contextual knowledge and actionable intelligence.

• Examples of sources and relevant bibliography are included, citing NIST publications, research papers on intrusion detection, and a report on the Sapphire/Slammer worm.

# Web technology 

• This presentation introduces HTTP and HTTPS protocols, detailing their properties including statelessness and encryption.

• URLs, their components, and URL encoding are explained.

• HTTP requests and responses are examined, including common methods (GET, POST, HEAD) and status codes.

• The presentation then covers client-side web technologies (HTML, CSS, JavaScript) and server-side languages (Python, NodeJS, Java, C#, PHP).

• Finally, it demonstrates creating quick HTTP servers using Python and PHP, and introduces ngrok for making local servers internet-accessible.

• ngrok, version 2.3.40, is running in the United States, forwarding http://localhost:5000 to http://2781-151-31-172-3.ngrok.io and https://2781-151-31-172-3.ngrok.io.

• The ngrok web interface is accessible at http://127.0.0.1:4040.  A WebHackIT training challenge involves serving a specific page content at a ngrok.io URL.

• HTTP/2, a revision of the HTTP protocol, aims to improve page load speed through features like header compression, server push, request pipelining, and multiplexing.

• HTTP/1.x's head-of-line blocking problem, hindering pipelining efficiency, is addressed by HTTP/2.
• HTTP 1.x's pipelining, while aiming to improve efficiency, suffers from head-of-line blocking.

• HTTP/2, accessible via an HTTP/1 upgrade, uses a binary protocol enabling request multiplexing through streams, messages, and frames, mitigating head-of-line blocking at the application layer.

• However, HTTP/3, based on QUIC and using UDP instead of TCP, further addresses this issue by reducing overhead and improving error recovery.

• HTTP is stateless, necessitating sessions, often implemented using cookies, which can be manipulated client-side with JavaScript or server-side with PHP.

• Cookies are used for authentication, personalization, and tracking, raising privacy concerns, especially with third-party cookies.

• Web storage offers an alternative to cookies, providing client-side storage with larger capacity and improved concurrency handling.

• The Document Object Model (DOM) allows JavaScript to manipulate HTML documents as a tree structure, enabling dynamic web page updates.

• Traditional web applications heavily rely on server-side HTML generation, leading to scalability challenges and difficulties in supporting diverse clients.

• Modern approaches utilize REST APIs, serving primarily static resources and minimal data in JSON format.

• This allows for clear frontend/backend separation, enabling easier support for various platforms.

• Modern client-side frameworks employ the Single-Page Application (SPA) paradigm, dynamically updating the page without full reloads for improved user experience, though this can hinder code inspection.

• WebSockets address the limitations of HTTP for continuous interactions and push notifications.
• HTTP defines basic and digest authentication methods, detailed in RFC 7617 and RFC 7616 respectively.

• OAuth and Single Sign-On (SSO) utilize identity providers like Google or Facebook for access to protected resources.

• Burp Suite, a Java-based web application security testing platform, offers features like HTTP(S) interception, repeating, and request comparison.

• A tutorial covers Burp Suite's proxy, repeater, and intruder tabs, highlighting functionalities such as request modification, history inspection, and brute-forcing (though not needed for a described CTF challenge).

• A training challenge involves a web application potentially leaking crucial information, solvable by identifying and using hidden comments and base64 decoding.

# Web sec 1

• Software vulnerabilities, stemming from bugs, design flaws, misconfigurations, or aging systems, are a primary cause of insecurity.

• A vulnerability arises from a system flaw, attacker access to it, and the attacker's ability to exploit it.

• The cost of vulnerabilities is significant, with estimates suggesting 20 flaws per thousand lines of code and that 95% of breaches could be prevented by patching.

• Responsible disclosure of vulnerabilities lacks a universal process, with varying guidelines from vendors and organizations like CERT and OIS.

• Different disclosure policies, ranging from full vendor disclosure to immediate public disclosure, have varying impacts on transparency and vendor response.

• Examples like the ProxyLogon vulnerability in Microsoft Exchange Servers illustrate the potential consequences of unpatched flaws.

• Web application vulnerabilities are particularly prevalent due to factors such as delusive simplicity, lack of security awareness, and increasing code complexity.

• Countermeasures include client-side filters, sandboxes, and server-side techniques like prepared statements and web application firewalls.

• Path traversal attacks, where attackers manipulate file paths to access unauthorized files, highlight the importance of input validation.

• User-controlled input should not be part of filenames to prevent path traversals.

• Validation of all user inputs is crucial;  a static list of allowed file paths is ideal.

• Alternatively, compute the canonical path and verify it's within the webroot.

• Defense-in-depth strategies, such as reduced web server privileges and sandbox environments, should be implemented alongside input validation.

• The document root contains web pages, while the server root holds logs and configurations.
• Web server file permissions should be carefully managed, defining specific users and groups (e.g., www & wwwgroup) with appropriate access.

• Additional server capabilities like automatic directory listing, symbolic link following, and server-side includes increase security risks.

• Command and code injection vulnerabilities arise from using functions like PHP's `system` and `eval` without input validation, allowing attackers to execute arbitrary commands or code, access files, and gain server control.

• SQL injection, another input validation vulnerability, lets attackers manipulate database queries to access or modify data, or even delete tables.

• Preventing these vulnerabilities requires avoiding functions that execute system commands or dynamically evaluate code, properly escaping special characters, and using reduced privileges or sandbox environments for web servers.

• The text describes SQL injection vulnerabilities.

• Attackers can exploit these by using inline comments to bypass authentication checks,  or by using 'OR' statements to always satisfy password conditions.

• Additionally, if stacked queries are enabled, attackers can add, modify, or delete database entries.

• A vulnerable code example shows how a search parameter can be manipulated to reveal sensitive data.
• SQL injection attacks can leak data from various tables using techniques like UNION SELECT and exploiting database metadata in information_schema or sqlite_master.

• Second-order SQL injection involves storing a malicious payload in the database for later execution.

• Blind SQL injection, where responses don't reveal query results or errors, can be exploited using conditional behavior, error triggering, or time delays to extract data.

• Prepared statements are recommended to prevent SQL injection, along with whitelisting and database permission restrictions.

• Server-side request forgery (SSRF) allows attackers to manipulate server requests, controlling the target URI and data, potentially accessing internal services or leaking data.

• Blind SSRF, where server feedback is absent, can still be used to gain information by analyzing request times.

• Preventing Server-Side Request Forgery (SSRF) involves a whitelist approach, limiting requests to specific hosts.

• However, this is difficult when connections to unexpected hosts are needed.

• Alternatively, isolating the requesting host from the network and sensitive data offers protection.

# Web sec 2

• This presentation covers web security, focusing on browser security and the Same Origin Policy (SOP).

• It explains the browser's execution model, JavaScript's interaction with the DOM and BOM, and the browser sandbox's role in safely executing JavaScript.

• The SOP, defined as a triplet (protocol, domain, port), restricts script access to resources from the same origin.

• However, the SOP's complexity and inconsistent browser implementations are noted, along with attacks like DNS rebinding that circumvent it.

• Mitigations like DNS pinning and rejecting requests with unrecognized Host headers are discussed.

• JSON-P, a workaround for cross-origin reads, is presented as an outdated and insecure method.

• Finally, Cross-Origin Resource Sharing (CORS) is introduced as a controlled mechanism to relax SOP restrictions, allowing cross-site resource access when the Origin header matches the Access-Control-Allow-Origin header.

• A simple request from http://a.com to http://b.com, allowed by the server's wildcard Access-Control-Allow-Origin, grants script access to the response.

• When credentials are included in a request,  Access-Control-Allow-Credentials must be true.

• For non-simple requests, like PUT, a preflight OPTIONS request is made; the server must whitelist the origin and allow the method in both the preflight response and the actual request for the script to access the response content.
• CORS headers, including `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, and others, manage cross-origin requests.

• Browser implementations of CORS, particularly origin validation, present configuration pitfalls.

• Insecure CORS configurations can be exploited, for example, through the `null` origin or improper origin validation in server-side code.

• The `postMessage` API allows cross-origin messaging, but requires origin validation to prevent vulnerabilities.

• Cookies' scope is determined by attributes like `domain`, `path`, `Secure`, `HttpOnly`, and `SameSite`, influencing their accessibility in cross-site requests.

• Since February 2020, `SameSite=Lax` is the default, and `SameSite=None` requires `Secure`.

• Cookie attachment to a request depends on these attributes and whether the request is cross-site.

• A cross-site request from https://www.example.com to various site.com URLs shows that cookies lang=en and uid=u1 are included, but sid=s2 is not, due to it being a cross-site request.

• The Cookie header only transmits cookie name and value; the server lacks information on secure connection, setting domain, and the cookie's origin.

• While RFC 2109 offered including domain and path, browser support is absent.

• Setting the domain attribute allows subdomains to share cookies, but servers cannot identify cookie origins based on domain or path.

• Most servers use the first encountered cookie with a matching name, with browser order prioritizing older and longer-path cookies.

• This can bypass CSRF protections, enabling attacks like login CSRF and session fixation.
• The text details vulnerabilities related to cookies, focusing on how attackers can exploit them.

• Sibling domains can overwrite cookies, allowing attackers to gain unauthorized access.

• Attackers can overflow the browser's cookie jar, deleting HttpOnly cookies and bypassing security measures.

• Cookie prefixes like `__Secure-` and `__Host-` offer improved security, but cookies remain challenging to use securely.

• Cross-site request forgery (CSRF) abuses automatic cookie attachment to perform actions on a victim's behalf.

• CSRF defenses include synchronizer tokens embedded in forms and cookie-to-header tokens verified by the server.

• Effective CSRF tokens are session-specific.

• Web applications use Referer header inspection to validate requests, checking if they originate from allowed pages.

• However, Referer headers can be suppressed by networks, machines, browsers, or user settings.

• Validation can be lenient (accepting requests without headers, potentially leaving vulnerabilities) or strict (rejecting them, potentially blocking legitimate requests).

• The SameSite cookie attribute mitigates CSRF attacks, offering default protection against classic attackers but not related-domain attackers.

• Fetch Metadata, supported by Chromium-based browsers over HTTPS, provides contextual request information to servers for dropping suspicious requests and mitigating various attacks.
• Fetch metadata headers (Sec-Fetch-Dest, Sec-Fetch-Mode, Sec-Fetch-Site, Sec-Fetch-User) provide information about a request's destination, mode, site relation, and user initiation.

• Resource and navigation isolation policies mitigate various attacks based on these headers.

• Cross-site scripting (XSS) vulnerabilities involve injecting JavaScript code into web pages, categorized as reflected, stored, or DOM-based.

• Reflected XSS occurs when unsanitized user input is embedded in a webpage; stored XSS involves permanently storing malicious code on the server; and DOM-based XSS uses browser-side manipulation of attacker-controlled data.

• XSS prevention requires preprocessing user input, using escaping libraries, and employing Trusted Types for DOM-based attacks.

• However,  evasion techniques exist, and filters may have limitations.

• Various XSS payloads, some browser-dependent, highlight the complexity of this vulnerability.

• Content Security Policy (CSP) controls which resources a webpage can load, initially mitigating content injection vulnerabilities like XSS.

• It now also restricts framing, mixed content, form submission targets, and navigation URLs.

• CSP uses directives like `font-src`, `script-src`, and `default-src` to filter resources, accepting hosts, schemes, 'self', and 'none' as values.

• Specific script/stylesheet values include `unsafe-inline`, `unsafe-eval`, nonce and hash-based whitelisting, and `strict-dynamic`.

• Some values, like nonces, override others such as `unsafe-inline`.
• Content Security Policy (CSP) controls content inclusion, allowing resources from the page's origin and specified domains like example.com.

• Scripts are whitelisted via SHA256 hashes, nonces, or 'strict-dynamic'.

• Code-reuse attacks exploit JS framework gadgets for code execution by injecting HTML elements.

• While CSP and other mechanisms mitigate script injection,  dangling markup injection can steal data, including CSRF tokens.

• Trusted Types aim to prevent DOM XSS by restricting dangerous injection sinks to trusted typed objects, enforced via CSP headers.

• HTTPS is preferred over HTTP to prevent SSL stripping, and HSTS enforces HTTPS connections, though it can be bypassed via NTP manipulation by forging future timestamps to expire HSTS policies.