## 1 and 2
Chapter 1 and 2 introduce EUDIW, explains what analysis will be performed, define the well known EUDIW environment, roles, use-case, components. Then emphasize the necessity of a robust secure storage and lists key threats to this element.
The authors aimed to answare 2 main questions:
- **RQ1**. Do mobile secure storage technologies meet the security requirements of the EUDI Wallet, given their inherent advantages such as availability and usability?
- **RQ2**. What alternative technologies to mobile solutions can be explored for secure storage in the EUDI Wallet?

## 3 Answer to RQ1: assessment Of mobile local WSCD
From the potential WSCD implementations, we provide below an overview of local internal WSCD solutions:
- Trusted Execution Environments (TEE): TEEs provide a secure area within the main processor of a device, separate from the system’s main operating system (OS). LoA high, provides:
	- KeyStore for android;
	- Secure Enclave for IOS (AES encryption engine);
	  
- StrongBox: Keymaster, a security module available through the Keystore API. This module is integrated into the device’s embedded Secure Element, including the Titan M2 chip.

The researcher performed a market analysis in Italy in order to understand what smartphones are used. The market analysis shows that among all mobile vendors, only the high-end models from Samsung and Google Pixel phones are equipped with an eIDAS High-
compliant WSCD that meets the AVA_VAN.5 criteria.
-  Samsung: High-end smartphones constitute 31% of Samsung’s total global sales. With Samsung holding a market share of 29.19% (as shown in Table 2), it can be estimated that an ≈ 31% × 29.19% = 9.05% of Samsung phones are potentially eIDAS-compliant;
- Google Pixel: Pixel phones account for 6.8% of Android sales in five European countries. Considering Italy’s proportional market share for Pixels 75.4/(75.4 + 58.2 + 80 + 72.5 + 67.8) ≈ 21.3%, it estimates to ≈ 6.8% × 21.3% = 1.45%.
- An evaluation is currently ongoing on some technology Apple Secure Enclave. potentially fulfilling requirements for the eIDAS High LoA. If successful, we would expect the percentage to increase to approximately 10%. 

For a total of compatible device with eIDAS High LoA of  10.5% (or potentially 20.5%).
That's for the hardware. But also, not every CC-certified WSCD meets the EUDI Wallet’s
cryptographic requirements or technical specifications.
The SOG-IS, for instance, recommends using the NIST Elliptic Curve Digital Signature Algorithm (ECDSA) with various curve and hash implementations. Only the ECDSA with the P-256 curve is supported by just two out of the three CC-certified local WSCDs: KNOX Vault and the Titan M2 chip.

## 4 Answer to RQ2: assessment of alternative WSCD solutions

Alternative options to EUDI WSCD:
INTERNAL:
- Embedded Secure Elements (**eSE**): eSEs are dedicated hardware components designed to resist physical and logical attacks, providing an enclave for secure data storage and processing within host devices like smartphones, tablets, or wearables;
- Embedded SIM (**eSIM**): This embedded SIM can house multiple SIM profiles, facilitating seamless operator switches without the need for physical SIM card replacement. Initially designed to simplify SIM provisioning and enhance the user experience, its application has now expanded across various domains, including mobile banking, healthcare, and IoT;
EXTERNAL:
- Secure elements (**SE**) or smart cards:  independent devices offering secure cryptographic storage and processing capabilities;
- Backend systems or remote Hardware Security Modules (**HSM**): These dedicated devices are equipped with robust hardware and software defenses to secure key information and ensure the integrity of cryptographic operations. 

Given this alternative devices, the authors lists possible alternative solutions to the EUDI WSCD that implements the options listed above in a consistent secure way.

Later chapters says what has already been sayed, emphasizing the fact that the numbers in the research are approximation, due to the poor statistical data, and are limited to italian market.