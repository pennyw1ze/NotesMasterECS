# Hardware-Backed Keystore

## Overview

The availability of a Trusted Execution Environment (TEE) in a system on a chip (SoC) offers an opportunity for Android devices to provide hardware-backed, strong security services to the Android OS, to platform services, and even to third-party apps (in the form of Android-specific extensions to the standard Java Cryptography Architecture, see [`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)).

## Glossary

Here is a quick overview of Keystore components and their relationships.

**`AndroidKeyStore`**

The Android Framework API and component used by apps to access Keystore functionality. It is an implementation of the standard Java Cryptography Architecture APIs, but also adds Android-specific extensions and consists of Java code that runs in the app's own process space. `AndroidKeyStore` fulfills app requests for Keystore behavior by forwarding them to the keystore daemon.

**keystore daemon**

An Android system daemon that provides access to all Keystore functionality through a [Binder API](https://cs.android.com/android/platform/superproject/+/android-latest-release:system/hardware/interfaces/keystore2/aidl/android/system/keystore2/IKeystoreService.aidl). This daemon is responsible for storing _keyblobs_ created by the underlying KeyMint (or Keymaster) implementation, which contain the secret key material, encrypted so Keystore can store them but not use or reveal them.

**KeyMint HAL service**

An AIDL server that implements the [`IKeyMintDevice`](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/interfaces/security/keymint/aidl/android/hardware/security/keymint/IKeyMintDevice.aidl) HAL, providing access to the underlying KeyMint TA.

**KeyMint trusted app (TA)**

Software running in a secure context, most often in TrustZone on an ARM SoC, that provides all of the secure cryptographic operations. This app has access to the raw key material, and validates all of the access control conditions on keys before allowing their use.

**`LockSettingsService`**

The Android system component responsible for user authentication, both password and fingerprint. It's not part of Keystore, but is relevant because Keystore supports the concept of _authentication bound keys_: keys that can be used only if the user has authenticated. `LockSettingsService` interacts with the Gatekeeper TA and Fingerprint TA to obtain authentication tokens, which it provides to the keystore daemon, and which are consumed by the KeyMint TA.

**Gatekeeper TA**

The component running in the secure environment that's responsible for authenticating user passwords and generating authentication tokens used to prove to the KeyMint TA that an authentication was done for a particular user at a particular point in time.

**Fingerprint TA**

The component running in the secure environment that's responsible for authenticating user fingerprints and generating authentication tokens used to prove to the KeyMint TA that an authentication was done for a particular user at a particular point in time.

## Architecture

The Android Keystore API and the underlying KeyMint HAL provide a basic but adequate set of cryptographic primitives to allow the implementation of protocols using access-controlled, hardware-backed keys.

The KeyMint HAL is an OEM-provided service used by the Keystore service to provide hardware-backed cryptographic services. To keep private key material secure, HAL implementations don't perform any sensitive operations in user space, or even in kernel space. Instead, the KeyMint HAL service running in Android delegates sensitive operations to a TA running in some kind of secure environment, typically by marshalling and unmarshalling requests in some implementation-defined wire format.

The resulting architecture looks like this:

![Access to KeyMint](/static/docs/security/images/access-to-keymint.png)

**Figure 1.** Access to KeyMint.

The KeyMint HAL API is low level, used by platform-internal components, and not exposed to app developers. The higher-level Java API that is available to apps is described on the [Android Developer site](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html).

## Access control

Android Keystore provides a central component for the storage and use of hardware-backed cryptographic keys, both for apps and for other system components. As such, access to any individual key is normally limited to the app or system component that created the key.

### Keystore domains

To support this access control, keys are identified to Keystore with a _key descriptor_. This key descriptor indicates a _domain_ that the descriptor belongs to, together with an identity within that domain.

Android apps access Keystore using the standard Java Cryptography Architecture, which identifies keys with a string alias. This method of identification maps to the Keystore `APP` domain internally; the UID of the caller is also included to disambiguate keys from different apps, preventing one app from accessing another's keys.

Internally, the frameworks code also receives a unique numeric _key ID_ after a key has been loaded. This numeric ID is used as the identifier for key descriptors within the `KEY_ID` domain. However, access control is still performed: even if one app discovers a key ID for another app's key, it can't use it in normal circumstances.

However, it is possible for an app to grant use of a key to a different app (as identified by UID). This grant operation returns a unique grant identifier, which is used as the identifier for key descriptors within the `GRANT` domain. Again, access control is still performed: even if a third app discovers the grant ID for a grantee's key, it can't use it.

Keystore also supports two other domains for key descriptors, which are used for other system components and aren't available for app-created keys:

-   The `BLOB` domain indicates that there is no identifier for the key in the key descriptor; instead, the key descriptor holds the keyblob itself and the client handles keyblob storage. This is used by clients (for example, `vold`) that need to access Keystore before the data partition is mounted.
-   The `SELINUX` domain allows system components to share keys, with access governed by a numeric identifier that corresponds to an SELinux label (see [SELinux policy for keystore\_key](#selinux-policy)).

### SELinux policy for keystore\_key

The identifier values used for `Domain::SELINUX` key descriptors are configured in the `keystore2_key_context` SELinux policy file. Each line in these files maps a numeric to an SELinux label, for example:

# wifi\_key is a keystore2\_key namespace intended to be used by wpa supplicant and
# Settings to share Keystore keys.
102            u:object\_r:wifi\_key:s0

A component that needs access to the key with ID 102 in the `SELINUX` domain must have the corresponding SELinux policy. For example, to allow `wpa_supplicant` to get and use these keys, add the following line to `hal_wifi_supplicant.te`:

allow hal\_wifi\_supplicant wifi\_key:keystore2\_key { get, use };

The numeric identifiers for `Domain::SELINUX` keys are divided into ranges to support different partitions without collisions:

| Partition | Range | Config files |
| --- | --- | --- |
| System | 0 ... 9,999 | `/system/etc/selinux/keystore2_key_contexts, /plat_keystore2_key_contexts` |
| Extended System | 10,000 ... 19,999 | `/system_ext/etc/selinux/system_ext_keystore2_key_contexts, /system_ext_keystore2_key_contexts` |
| Product | 20,000 ... 29,999 | `/product/etc/selinux/product_keystore2_key_contexts, /product_keystore2_key_contexts` |
| Vendor | 30,000 ... 39,999 | `/vendor/etc/selinux/vendor_keystore2_key_contexts, /vendor_keystore2_key_contexts` |

The following specific values have been defined for the system partition:

| Namespace ID | SEPolicy label | UID | Description |
| --- | --- | --- | --- |
| 0 | `su_key` | N/A | Super user key. Only used for testing on userdebug and eng builds. Not relevant on user builds. |
| 1 | `shell_key` | N/A | Namespace available to shell. Mostly used for testing, but can be used on user builds as well from the command line. |
| 100 | `vold_key` | N/A | Intended for use by vold. |
| 101 | `odsign_key` | N/A | Used by the on-device signing daemon. |
| 102 | `wifi_key` | `AID_WIFI(1010)` | Used by Android's Wifi subsystem including `wpa_supplicant`. |
| 103 | `locksettings_key` | N/A | Used by `LockSettingsService` |
| 120 | `resume_on_reboot_key` | `AID_SYSTEM(1000)` | Used by Android's system server to support resume on reboot. |

### Access vectors

Keystore allows control over which operations can be performed on a key, in addition to controlling overall access to a key. The `keystore2_key` permissions are described in the [`KeyPermission.aidl`](https://cs.android.com/android/platform/superproject/+/android-latest-release:system/hardware/interfaces/keystore2/aidl/android/system/keystore2/KeyPermission.aidl) file.

### System permissions

In addition to the per-key access controls described in [SELinux policy for keystore\_key](#selinux-policy), the following table describes other SELinux permissions that are required to perform various system and maintenance operations:

| Permission | Meaning |
| --- | --- |
| `add_auth` | Required for adding auth tokens to Keystore; used by authentication providers such as Gatekeeper or `BiometricManager`. |
| `clear_ns` | Required for deleting all keys in a specific namespace; used as a maintenance operation when apps are uninstalled. |
| `list` | Required by the system for enumerating keys by various properties, such as ownership or whether they are authentication bound. This permission isn't required by callers enumerating their own namespaces (covered by the `get_info` permission). |
| `lock` | Required for notifying keystore that the device was locked, which in turn evicts super-keys to ensure that authentication bound keys are unavailable. |
| `unlock` | Required to notifying keystore that the device was unlocked, restoring access to the super-keys that protect authentication bound keys. |
| `reset` | Required for resetting Keystore to factory default, deleting all keys that aren't vital to the functioning of the Android OS. |

## History

In Android 5 and lower, Android had a simple, hardware-backed cryptographic services API, provided by versions 0.2 and 0.3 of the Keymaster hardware abstraction layer (HAL). Keystore provided digital signing and verification operations, plus generation and import of asymmetric signing key pairs. This is already implemented on many devices, but there are many security goals that can't easily be achieved with only a signature API. Android 6.0 extended the Keystore API to provide a broader range of capabilities.

### Android 6.0

In Android 6.0, Keymaster 1.0 added [symmetric cryptographic primitives](/docs/security/features/keystore/features), AES and HMAC, and an access control system for hardware-backed keys. Access controls are specified during key generation and enforced for the lifetime of the key. Keys can be restricted to be usable only after the user has been authenticated, and only for specified purposes or with specified cryptographic parameters.

In addition to expanding the range of cryptographic primitives, Keystore in Android 6.0 added the following:

-   A usage control scheme to allow key usage to be limited, to mitigate the risk of security compromise due to misuse of keys
-   An access control scheme to enable restriction of keys to specified users, clients, and a defined time range

### Android 7.0

In Android 7.0, Keymaster 2 added support for key attestation and version binding.

[Key attestation](/docs/security/features/keystore/attestation) provides public key certificates that contain a detailed description of the key and its access controls, to make the key's existence in secure hardware and its configuration remotely verifiable.

[Version binding](/docs/security/features/keystore/version-binding) binds keys to the operating system and patch level version. This ensures that an attacker who discovers a weakness in an old version of the system or the TEE software can't roll a device back to the vulnerable version and use keys created with the newer version. In addition, when a key with a given version and patch level is used on a device that has been upgraded to a newer version or patch level, the key is upgraded before it can be used, and the previous version of the key invalidated. As the device is upgraded, the keys ratchet forward along with the device, but any reversion of the device to a previous release causes the keys to be unusable.

### Android 8.0

In Android 8.0, Keymaster 3 transitioned from the old-style C-structure HAL to the C++ HAL interface generated from a definition in the new hardware interface definition language (HIDL). As part of the change, many of the argument types changed, though types and methods have a one-to-one correspondence with the old types and the HAL struct methods.

In addition to this interface revision, Android 8.0 extended the attestation feature of Keymaster 2 to support [ID attestation](/docs/security/features/keystore/attestation#id-attestation). ID attestation provides a limited and optional mechanism for strongly attesting to hardware identifiers, such as device serial number, product name, and phone ID (IMEI or MEID). To implement this addition, Android 8.0 changed the ASN.1 attestation schema to add ID attestation. Keymaster implementations need to find some secure way to retrieve the relevant data items, as well as to define a mechanism for securely and permanently disabling the feature.

### Android 9

In Android 9, updates included:

-   Update to [Keymaster 4](https://android.googlesource.com/platform/hardware/interfaces/+/main/keymaster/4.0/)
-   Support for embedded Secure Elements
-   Support for secure key import
-   Support for 3DES encryption
-   Changes to version binding so that `boot.img` and `system.img` have separately set versions to allow for independent updates

### Android 10

Android 10 introduced version 4.1 of the Keymaster HAL, which added:

-   Support for keys that are only usable when the device is unlocked
-   Support for keys that can only be used in early boot stages
-   Optional support for [hardware-wrapped storage keys](https://source.android.com/docs/security/features/encryption/hw-wrapped-keys.html)
-   Optional support for device-unique attestation in StrongBox

### Android 12

Android 12 introduced the new KeyMint HAL, which replaces the Keymaster HAL but provides similar functionality. In addition to all of the features above, the KeyMint HAL also includes:

-   Support for ECDH key agreement
-   Support for user-specified attestation keys
-   Supoprt for keys with a limited number of uses

Android 12 also includes a new version of the keystore system daemon, rewritten in Rust and known as `keystore2`

### Android 13

Android 13 added v2 of the KeyMint HAL, which adds support for [Curve25519](https://en.wikipedia.org/wiki/Curve25519) for both signing and key agreement.

## Features

This page contains information about the cryptographic features of [Android Keystore](/docs/security/features/keystore), as provided by the underlying KeyMint (or Keymaster) implementation.

## Cryptographic primitives

Keystore provides the following categories of operations:

-   Creation of keys, resulting in private or secret key material that is accessible only to the secure environment. Clients can create keys in the following ways:
    -   Fresh key generation
    -   Import of unencrypted key material
    -   Import of encrypted key material
-   Key attestation: Asymmetric key creation generates a certificate holding the public key part of the keypair. This certificate optionally also holds information about the metadata for the key and the state of the device, all signed by a key chaining back to a trusted root.
-   Cryptographic operations:
    -   Symmetric encryption and decryption (AES, 3DES)
    -   Asymmetric decryption (RSA)
    -   Asymmetric signing (ECDSA, RSA)
    -   Symmetric signing and verification (HMAC)
    -   Asymmetric key agreement (ECDH)

**Note:** Keystore and KeyMint don't handle public key operations for asymmetric keys.

Protocol elements, such as purpose, mode, and padding, as well as [access control constraints](#key_access_control), are specified when keys are generated or imported and are permanently bound to the key, ensuring the key can't be used in any other way.

In addition to the list above, there is one more service that KeyMint (previously Keymaster) implementations provide, but which isn't exposed as an API: Random number generation. This is used internally for generation of keys, Initialization Vectors (IVs), random padding and other elements of secure protocols that require randomness.

## Necessary primitives

All KeyMint implementations provide:

-   [RSA](http://en.wikipedia.org/wiki/RSA_\(cryptosystem\))
    -   2048, 3072, and 4096-bit key support
    -   Support for public exponent F4 (2^16+1)
    -   Padding modes for RSA signing:
        -   RSASSA-PSS (`PaddingMode::RSA_PSS`)
        -   RSASSA-PKCS1-v1\_5 (`PaddingMode::RSA_PKCS1_1_5_SIGN`)
    -   Digest modes for RSA signing:
        -   SHA-256
    -   Padding modes for RSA encryption/decryption:
        -   Unpadded
        -   RSAES-OAEP (`PaddingMode::RSA_OAEP`)
        -   RSAES-PKCS1-v1\_5 (`PaddingMode::RSA_PKCS1_1_5_ENCRYPT`)
-   [ECDSA](http://en.wikipedia.org/wiki/Elliptic_Curve_DSA)
    -   224, 256, 384, and 521-bit key support are supported, using the NIST P-224, P-256, P-384, and P-521 curves, respectively
    -   Digest modes for ECDSA:
        -   No digest (deprecated, will be removed in the future)
        -   SHA-256
-   [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
    -   128 and 256-bit keys are supported
    -   [CBC](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29), CTR, ECB, and GCM. The GCM implementation does not allow the use of tags smaller than 96 bits or nonce lengths other than 96 bits.
    -   Padding modes `PaddingMode::NONE` and `PaddingMode::PKCS7` is supported for CBC and ECB modes. With no padding, CBC or ECB mode encryption fails if the input isn't a multiple of the block size.
-   [HMAC](http://en.wikipedia.org/wiki/Hash-based_message_authentication_code) [SHA-256](http://en.wikipedia.org/wiki/SHA-2), with any key size up to at least 32 bytes.

SHA1 and the other members of the SHA2 family (SHA-224, SHA384 and SHA512) are strongly recommended for KeyMint implementations. Keystore provides them in software if the hardware KeyMint implementation doesn't provide them.

Some primitives are also recommended for interoperability with other systems:

-   Smaller key sizes for RSA
-   Arbitrary public exponents for RSA

## Key access control

Hardware-based keys that can never be extracted from the device don't provide much security if an attacker can use them at will (though they're more secure than keys which _can_ be exfiltrated). Thus, it's crucial that Keystore enforce access controls.

Access controls are defined as an "authorization list" of tag/value pairs. Authorization tags are 32-bit integers and the values are a variety of types. Some tags can be repeated to specify multiple values. Whether a tag can be repeated is specified in the KeyMint HAL interface. When a key is created, the caller specifies an authorization list. The KeyMint implementation underlying Keystore modifies the list to specify some additional information, such as whether the key has rollback protection, and return a "final" authorization list, encoded into the returned key blob. Any attempt to use the key for any cryptographic operation fails if the final authorization list is modified.

For Keymaster 2 and earlier, the set of possible tags is defined in the enumeration `keymaster_authorization_tag_t` and is permanently fixed (though it can be extended). Names were prefixed with `KM_TAG`. The top four bits of tag IDs are used to indicate the type.

Keymaster 3 changed the `KM_TAG` prefix to `Tag::`.

Possible types include:

**`ENUM`:** Many tags' values are defined in enumerations. For example, the possible values of `TAG::PURPOSE` are defined in enum `keymaster_purpose_t`.

**`ENUM_REP`:** Same as `ENUM`, except that the tag can be repeated in an authorization list. Repetition indicates multiple authorized values. For example, an encryption key likely has `KeyPurpose::ENCRYPT` and `KeyPurpose::DECRYPT`.

When KeyMint creates a key, the caller specifies an authorization list for the key. This list is modified by Keystore and KeyMint to add extra constraints, and the underlying KeyMint implementation encodes the final authorization list into the returned keyblob. The encoded authorization list is cryptographically bound into the keyblob, so that any attempt to modify the authorization list (including ordering) results in an invalid keyblob that can't be used for cryptographic operations.

### Hardware versus software enforcement

Not all secure hardware implementations contain the same features. To support a variety of approaches, Keymaster distinguishes between secure and non-secure world access control enforcement, or hardware and software enforcement, respectively.

This is exposed in the KeyMint API with the `securityLevel` field of the [`KeyCharacteristics`](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/interfaces/security/keymint/aidl/android/hardware/security/keymint/KeyCharacteristics.aidl) type. The secure hardware is responsible for placing the authorizations in the `KeyCharacteristics` with the appropriate security level, based on what it can enforce. This information is also exposed in the attestation records for asymmetric keys: key characteristics for `SecurityLevel::TRUSTED_ENVIRONMENT` or `SecurityLevel::STRONGBOX` appear in the `hardwareEnforced` list, and characteristics for `SecurityLevel::SOFTWARE` or `SecurityLevel::KEYSTORE` appear in the `softwareEnforced` list.

For example, constraints on the date and time interval when a key can be used are typically not enforced by the secure environment, because it doesn't have trustworthy access to date and time information. As a result, authorizations like `Tag::ORIGINATION_EXPIRE_DATETIME` are enforced by Keystore in Android, and would have `SecurityLevel::KEYSTORE`.

For more information about determining whether keys and their authorizations are hardware backed, see [Key attestation](/docs/security/features/keystore/attestation).

### Cryptographic message construction authorizations

The following tags are used to define the cryptographic characteristics of operations using the associated key:

-   `Tag::ALGORITHM`
-   `Tag::KEY_SIZE`
-   `Tag::BLOCK_MODE`
-   `Tag::PADDING`
-   `Tag::CALLER_NONCE`
-   `Tag::DIGEST`
-   `Tag::MGF_DIGEST`

The following tags are repeatable, meaning that multiple values can be associated with a single key:

-   `Tag::BLOCK_MODE`
-   `Tag::PADDING`
-   `Tag::DIGEST`
-   `Tag::MGF_DIGEST`

The value to be used is specified at operation time.

### Purpose

Keys have an associated set of purposes, expressed as one or more authorization entries with the `Tag::PURPOSE` tag, which defines how they can be used. The purposes are defined in [`KeyPurpose.aidl`](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/interfaces/security/keymint/aidl/android/hardware/security/keymint/KeyPurpose.aidl).

Note that some combinations of purpose values create security problems. For example, an RSA key that can be used to both encrypt and to sign allows an attacker who can convince the system to decrypt arbitrary data to generate signatures.

### Key import

Keymaster supports export of public keys only, in X.509 format, and import of:

-   Asymmetric key pairs in DER-encoded PKCS#8 format (without password-based encryption)
-   Symmetric keys as raw bytes

To ensure that imported keys can be distinguished from securely generated keys, `Tag::ORIGIN` is included in the appropriate key authorization list. For example, if a key was generated in secure hardware, `Tag::ORIGIN` with value `KeyOrigin::GENERATED` is found in the `hw_enforced` list of the key characteristics, while a key that was imported into secure hardware has the value `KeyOrigin::IMPORTED`.

### User authentication

Secure KeyMint implementations don't implement user authentication, but depend on other trusted apps that do. For the interface that these apps implement, see the [Gatekeeper page](/docs/security/features/authentication/gatekeeper).

User authentication requirements are specified via two sets of tags. The first set indicates which authentication methods allow use of the key:

-   `Tag::USER_SECURE_ID` has a 64-bit numeric value specifying the [secure user ID](/docs/security/features/authentication/gatekeeper#user_sids) that is provided in a secure authentication token to unlock use of the key. If repeated, the key can be used if any of the values is provided in a secure authentication token.

The second set indicates whether and when the user needs to be authenticated. If neither of these tags is present, but `Tag::USER_SECURE_ID` is, authentication is required for every use of the key.

-   `Tag::NO_AUTHENTICATION_REQUIRED` indicates no user authentication is required, although access to the key is still restricted to the owning app (and any apps to which it grants access).
-   `Tag::AUTH_TIMEOUT` is a numeric value specifying, in seconds, how fresh the user authentication needs to be to authorize key usage. Timeouts don't cross reboots; after a reboot, all authentications are invalidated. The timeout can be set to a large value to indicate that authentication is required once per boot (2^32 seconds is ~136 years; presumably Android devices are rebooted more often than that).

### Require an unlocked device

Keys with `Tag::UNLOCKED_DEVICE_REQUIRED` are usable only while the device is unlocked. For the detailed semantics, see [`KeyProtection.Builder#setUnlockedDeviceRequired(boolean)`](https://developer.android.com/reference/android/security/keystore/KeyProtection.Builder#setUnlockedDeviceRequired\(boolean\)).

`UNLOCKED_DEVICE_REQUIRED` is enforced by Keystore, not by KeyMint. However, in Android 12 and higher, Keystore cryptographically protects `UNLOCKED_DEVICE_REQUIRED` keys while the device is locked to ensure that, in most cases, they cannot be used even if Keystore is compromised while the device is locked.

All cryptography and random number generation described in this section uses BoringSSL, except where the use of KeyMint is explicitly mentioned. All secrets are zeroized as soon as they are no longer needed.

#### UnlockedDeviceRequired super keys

To cryptographically protect `UNLOCKED_DEVICE_REQUIRED` keys, Keystore "superencrypts" them before storing them in its database. When possible, it protects the superencryption keys (super keys) while the device is locked in such a way that they can be recovered only by a successful device unlock. (The term "superencryption" is used because this layer of encryption is applied _in addition to_ the layer of encryption that KeyMint already applies to all keys.)

Each [user](/docs/devices/admin/multi-user) (including profiles) has two super keys associated with `UNLOCKED_DEVICE_REQUIRED`:

-   The UnlockedDeviceRequired symmetric super key. This is an AES‑256‑GCM key. It encrypts `UNLOCKED_DEVICE_REQUIRED` keys that are imported, generated, or used while the device is unlocked for the user.
-   The UnlockedDeviceRequired asymmetric super key. This is an ECDH P‑521 key pair. It encrypts `UNLOCKED_DEVICE_REQUIRED` keys that are imported or generated while the device is locked for the user. For more details, see [Storing keys while device is locked](#unlocked_device_storing_keys).

#### Generating and protecting the super keys

When a user is created, Keystore generates the user's UnlockedDeviceRequired super keys and stores them in its database, encrypted (indirectly) by the user's synthetic password:

1.  The system server derives the user's Keystore password from the user's synthetic password using an SP800‑108 KDF.
2.  The system server passes the user's Keystore password to Keystore.
3.  Keystore generates the user's super keys.
4.  For each of the user's super keys:
    1.  Keystore generates a random salt.
    2.  Keystore derives an AES‑256‑GCM key from the user's Keystore password and the salt using HKDF‑SHA256.
    3.  Keystore encrypts the secret part of the super key using this AES‑256‑GCM key.
    4.  Keystore stores the encrypted super key and its salt in its database. If it's an asymmetric key, the public half of the key is also stored unencrypted.

This procedure allows these super keys to be decrypted when the user's synthetic password is known, such as when the user's correct PIN, pattern, or password is entered.

Keystore also caches these super keys in memory, allowing it to operate on `UNLOCKED_DEVICE_REQUIRED` keys. However, it tries to cache the secret parts of these keys only while the device is unlocked for the user. When the device is locked for the user, Keystore zeroizes its cached copy of the secret parts of these super keys, if possible. Specifically, when the device is locked for the user, Keystore selects and applies one of three protection levels for the user's UnlockedDeviceRequired super keys:

-   If the user has only PIN, pattern, or password enabled, then Keystore zeroizes the secret parts of its cached super keys. This makes the super keys recoverable only via the encrypted copy in the database that can be decrypted only by PIN, pattern, or password equivalent.
-   If the user has only [class 3 ("strong") biometrics](/docs/security/features/biometric/measure#biometric-classes) and PIN, pattern, or password enabled, then Keystore arranges for the super keys to be recoverable by any of the user's enrolled class 3 biometrics (commonly fingerprint), as an alternative to PIN, pattern, or password equivalent. To do this, it generates a new AES‑256‑GCM key, encrypts the secret parts of the super keys with it, imports the AES‑256‑GCM key into KeyMint as a biometric-bound key that requires biometric authentication to have succeeded within the last 15 seconds, and zeroizes the plaintext copies of all these keys.
-   If the user has a class 1 ("convenience") biometric, class 2 ("weak") biometric, or active unlock trust agent enabled, then Keystore keeps the super keys cached in plaintext. In this case, cryptographic security for `UNLOCKED_DEVICE_REQUIRED` keys isn't provided. Users can avoid this less secure fallback by not enabling these unlock methods. The most common unlock methods that fall into these categories are face unlock on many devices, and unlock with a paired smartwatch.

When the device is unlocked for the user, Keystore recovers the user's UnlockedDeviceRequired super keys if possible. For PIN, pattern, or password equivalent unlock, it decrypts the copy of these keys that is stored in the database. Otherwise, it checks if it saved a copy of these keys encrypted with a biometric-bound key, and if so tries to decrypt that. This succeeds only if the user has successfully authenticated with a class 3 biometric within the last 15 seconds, enforced by KeyMint (not Keystore).

#### Storing keys while device is locked

Keystore allows users to import and generate `UNLOCKED_DEVICE_REQUIRED` keys while the device is locked. It uses a hybrid encryption scheme to ensure that they can be decrypted only when the device is later unlocked:

-   Encryption (importing or generating an `UNLOCKED_DEVICE_REQUIRED` key while the device is locked):
    1.  Keystore generates a new ephemeral ECDH P‑521 key pair.
    2.  Keystore generates a shared secret by doing ECDH key agreement between the private key of this ephemeral key pair and the public half of the UnlockedDeviceRequired asymmetric super key.
    3.  Keystore generates a random salt.
    4.  Keystore derives an AES‑256‑GCM key from the shared secret and the salt using HKDF‑SHA256.
    5.  Keystore encrypts the `UNLOCKED_DEVICE_REQUIRED` key using this AES‑256‑GCM key.
    6.  Keystore stores the encrypted `UNLOCKED_DEVICE_REQUIRED` key, the salt, and the public half of the ephemeral key pair in its database.
-   Decryption (using the created `UNLOCKED_DEVICE_REQUIRED` key while the device is unlocked):
    1.  Keystore loads the encrypted `UNLOCKED_DEVICE_REQUIRED` key, the salt, and the public half of the ephemeral key pair from its database.
    2.  Keystore generates a shared secret by doing ECDH key agreement between the public half of the ephemeral key pair and the private half of the UnlockedDeviceRequired asymmetric super key. The private key is available because the device is unlocked.
    3.  Keystore derives an AES‑256‑GCM key from the shared secret and the salt using HKDF‑SHA256. This AES‑256‑GCM key is the same as the one that was derived during encryption.
    4.  Keystore decrypts the `UNLOCKED_DEVICE_REQUIRED` key using the AES‑256‑GCM key.
    5.  Keystore reencrypts the `UNLOCKED_DEVICE_REQUIRED` key using the UnlockedDeviceRequired _symmetric_ super key. This doesn't affect the security properties of the key, but it allows it to be accessed more quickly later.

This feature makes it possible for apps to store data while the device is locked, such that it can be decrypted only while the device is unlocked. To do so, apps should follow these steps:

1.  Generate an AES‑256‑GCM key outside of Keystore.
2.  Encrypt data using the AES‑256‑GCM key.
3.  Import the AES‑256‑GCM key into Keystore with the [`setUnlockedDeviceRequired(true)`](https://developer.android.com/reference/android/security/keystore/KeyProtection.Builder#setUnlockedDeviceRequired\(boolean\)) key protection set.
4.  Zeroize the original copy of the key.

To decrypt the data while the device is unlocked, use the key that was imported into Keystore.

### Client binding

Client binding, the association of a key with a particular client app, is done via an optional client ID and some optional client data (`Tag::APPLICATION_ID` and `Tag::APPLICATION_DATA`, respectively). Keystore treats these values as opaque blobs, only ensuring that the same blobs presented during key generation/import are presented for every use and are byte-for-byte identical. The client binding data isn't returned by KeyMint. The caller has to know it in order to use the key.

This feature isn't exposed to apps.

### Expiration

Keystore supports restricting key usage by date. Key start of validity and key expirations can be associated with a key and Keymaster refuses to perform key operations if the current date/time is outside of the valid range. The key validity range is specified with the tags `Tag::ACTIVE_DATETIME`, `Tag::ORIGINATION_EXPIRE_DATETIME`, and `Tag::USAGE_EXPIRE_DATETIME`. The distinction between "origination" and "usage" is based on whether the key is being used to "originate" a new ciphertext/signature/etc., or to "use" an existing ciphertext/signature/etc. Note that this distinction isn't exposed to apps.

The `Tag::ACTIVE_DATETIME`, `Tag::ORIGINATION_EXPIRE_DATETIME`, and `Tag::USAGE_EXPIRE_DATETIME` tags are optional. If the tags are absent, it is assumed that the key in question can always be used to decrypt/verify messages.

Because wall-clock time is provided by the non-secure world, the expiration-related tags are in the software-enforced list.

### Root of trust binding

Keystore requires keys to be bound to a root of trust, which is a bitstring provided to the KeyMint secure hardware during startup, preferably by the bootloader. This bitstring is cryptographically bound to every key managed by KeyMint.

The root of trust consists of the public key used to verify the signature on the boot image and the lock state of the device. If the public key is changed to allow a different system image to be used or if the lock state is changed, then none of the KeyMint-protected keys created by the previous system are usable, unless the previous root of trust is restored and a system that is signed by that key is booted. The goal is to increase the value of the software-enforced key access controls by making it impossible for an attacker-installed operating system to use KeyMint keys.

### Standalone keys

Some KeyMint secure hardware can choose to store key material internally and return handles rather than encrypted key material. Or there might be other cases in which keys cannot be used until some other non-secure or secure world system component is available. The KeyMint HAL allows the caller to request that a key be "standalone," via the `TAG::STANDALONE` tag, meaning that no resources other than the blob and the running KeyMint system are required. The tags associated with a key can be inspected to see whether a key is standalone. At present, only two values are defined:

-   `KeyBlobUsageRequirements::STANDALONE`
-   `KeyBlobUsageRequirements::REQUIRES_FILE_SYSTEM`

This feature isn't exposed to apps.

### Velocity

When it's created, the maximum usage velocity can be specified with `TAG::MIN_SECONDS_BETWEEN_OPS`. TrustZone implementations refuse to perform cryptographic operations with that key if an operation was performed less than `TAG::MIN_SECONDS_BETWEEN_OPS` seconds earlier.

The simple approach to implementing velocity limits is a table of key IDs and last-use timestamps. This table is a limited size, but accommodates at least 16 entries. In the event that the table is full and no entries can be updated or discarded, secure hardware implementations "fail safe," preferring to refuse all velocity-limited key operations until one of the entries expires. It is acceptable for all entries to expire upon reboot.

Keys can also be limited to _n_ uses per boot with `TAG::MAX_USES_PER_BOOT`. This also requires a tracking table, which accommodates at least four keys and also fails safe. Note that apps can't create per-boot limited keys. This feature isn't exposed through Keystore and is reserved for system operations.

This feature isn't exposed to apps.

### Random number generator re-seeding

Because secure hardware generates random numbers for key material and initialization vectors (IVs), and because hardware random number generators might not always be fully trustworthy, the KeyMint HAL provides an interface to allow the client to provide additional entropy, which is mixed into the random numbers generated.

Use a hardware random-number generator as the primary seed source. The seed data provided through the external API can't be the sole source of randomness used for number generation. Further, the mixing operation used needs to ensure that the random output is unpredictable if any one of the seed sources is unpredictable.

## Key and ID attestation

Keystore provides a more secure place to create, store, and use cryptographic keys in a controlled way. When hardware-backed key storage is available and used, key material is more secure against extraction from the device, and KeyMint (previously Keymaster) enforces restrictions that are difficult to subvert.

However, this is true only if the Keystore keys are known to be in hardware-backed storage. In Keymaster 1, there was no way for apps or remote servers to reliably verify if this was the case. The keystore daemon loaded the available Keymaster hardware abstraction layer (HAL) and believed whatever the HAL said with respect to hardware backing of keys.

To remedy this, [key attestation](https://developer.android.com/training/articles/security-key-attestation) was introduced in Android 7.0 (Keymaster 2) and ID attestation was introduced in Android 8.0 (Keymaster 3).

Key attestation aims to provide a way to strongly determine if an asymmetric key pair is hardware-backed, what the properties of the key are, and what constraints are applied to its usage.

ID attestation allows the device to provide proof of its hardware identifiers, such as serial number or IMEI.

**Note:** To support Keymaster 3's transition from the old-style C-structure HAL to the C++ HAL interface generated from a definition in the new Hardware Interface Definition Language (HIDL), tag and method names have changed in Android 8.0. Like all other Keymaster enums, tags are now defined as C++ scoped enums. For example, tags, formerly prefixed with `KM_TAG_`, are now prefixed with `Tag::` and methods are in camel case. The examples below use Keymaster 3 terms, unless specified otherwise.

## Key attestation

To support key attestation, Android 7.0 introduced a set of tags, type, and method to the HAL.

**Tags**

-   `Tag::ATTESTATION_CHALLENGE`
-   `Tag::INCLUDE_UNIQUE_ID`
-   `Tag::RESET_SINCE_ID_ROTATION`

**Type**

**Keymaster 2 and below**

typedef struct {
    keymaster\_blob\_t\* entries;
    size\_t entry\_count;
} keymaster\_cert\_chain\_t;

**`AttestKey` method**

**Keymaster 3**

    attestKey(vec<uint8\_t> keyToAttest, vec<KeyParameter> attestParams)
        generates(ErrorCode error, vec<vec<uint8\_t>> certChain);

**Keymaster 2 and below**

keymaster\_error\_t (\*attest\_key)(const struct keymaster2\_device\* dev,
        const keymaster\_key\_blob\_t\* key\_to\_attest,
        const keymaster\_key\_param\_set\_t\* attest\_params,
        keymaster\_cert\_chain\_t\* cert\_chain);

-   `dev` is the Keymaster device structure.
-   `keyToAttest` is the key blob returned from `generateKey` for which the attestation is created.
-   `attestParams` is a list of any parameters necessary for attestation. This includes `Tag::ATTESTATION_CHALLENGE` and possibly `Tag::RESET_SINCE_ID_ROTATION`, as well as `Tag::APPLICATION_ID` and `Tag::APPLICATION_DATA`. The latter two are necessary to decrypt the key blob if they were specified during key generation.
-   `certChain` is the output parameter, which returns an array of certificates. Entry 0 is the attestation certificate, meaning it certifies the key from `keyToAttest` and contains the attestation extension.

The `attestKey` method is considered a public key operation on the attested key, because it can be called at any time and doesn't need to meet authorization constraints. For example, if the attested key needs user authentication for use, an attestation can be generated without user authentication.

### Attestation certificate

The attestation certificate is a standard X.509 certificate, with an optional attestation extension that contains a description of the attested key. The certificate is signed with a certified [attestation key](#attestation-keys-and-certificates). The attestation key might use a different algorithm than the key being attested.

The attestation certificate contains the fields in the table below and can't contain any additional fields. Some fields specify a fixed field value. CTS tests validate that the certificate content is exactly as defined.

#### Certificate SEQUENCE

| Field name (see [RFC 5280](https://tools.ietf.org/html/rfc5280)) | Value |
| --- | --- |
| [tbsCertificate](https://tools.ietf.org/html/rfc5280#section-4.1.1.1) | [TBSCertificate SEQUENCE](#tbscertificate-sequence) |
| [signatureAlgorithm](https://tools.ietf.org/html/rfc5280#section-4.1.1.2) | AlgorithmIdentifier of algorithm used to sign key:  
ECDSA for EC keys, RSA for RSA keys. |
| [signatureValue](https://tools.ietf.org/html/rfc5280#section-4.1.1.3) | BIT STRING, signature computed on ASN.1 DER-encoded tbsCertificate. |

#### TBSCertificate SEQUENCE

| Field name (see [RFC 5280](https://tools.ietf.org/html/rfc5280)) | Value |
| --- | --- |
| `version` | INTEGER 2 (means v3 certificate) |
| `serialNumber` | INTEGER 1 (fixed value: same on _all_ certs) |
| `signature` | AlgorithmIdentifier of algorithm used to sign key: ECDSA for EC keys, RSA for RSA keys. |
| `issuer` | Same as the subject field of the batch attestation key. |
| `validity` | SEQUENCE of two dates, containing the values of `Tag::ACTIVE_DATETIME` and `Tag::USAGE_EXPIRE_DATETIME`. Those values are in milliseconds since Jan 1, 1970. See [RFC 5280](https://tools.ietf.org/html/rfc5280) for correct date representations in certificates.  
If `Tag::ACTIVE_DATETIME` is not present, use the value of `Tag::CREATION_DATETIME`. If `Tag::USAGE_EXPIRE_DATETIME` is not present, use the expiration date of the batch attestation key certificate. |
| `subject` | CN = "Android Keystore Key" (fixed value: same on _all_ certs) |
| `subjectPublicKeyInfo` | SubjectPublicKeyInfo containing attested public key. |
| `extensions/Key Usage` | digitalSignature: set if key has purpose `KeyPurpose::SIGN` or `KeyPurpose::VERIFY`. All other bits unset. |
| `extensions/CRL Distribution Points` | Value TBD |
| `extensions/"attestation"` | The OID is 1.3.6.1.4.1.11129.2.1.17; the content is defined in the [Attestation extension](#attestation-extension) section below. As with all X.509 certificate extensions, the content is represented as an OCTET\_STRING containing a DER encoding of the attestation SEQUENCE. |

### Attestation extension

The `attestation` extension has OID `1.3.6.1.4.1.11129.2.1.17`. It contains information about the key pair being attested and the state of the device at key generation time.

The Keymaster/KeyMint tag types defined in the [AIDL interface specification](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/interfaces/security/keymint/aidl/android/hardware/security/keymint/TagType.aidl) are translated to ASN.1 types as follows:

| KeyMint or Keymaster type | ASN.1 type | Notes |
| --- | --- | --- |
| `ENUM` | `INTEGER` |
| `ENUM_REP` | `SET of INTEGER` |
| `UINT` | `INTEGER` |
| `UINT_REP` | `SET of INTEGER` |
| `ULONG` | `INTEGER` |
| `ULONG_REP` | `SET of INTEGER` |
| `DATE` | `INTEGER` | Milliseconds since Jan 1, 1970 00:00:00 GMT. |
| `BOOL` | `NULL` | Tag presence means true, absence means false. |
| `BIGNUM` |  | No tags have this type, so no mapping is defined. |
| `BYTES` | `OCTET_STRING` |

#### Schema

The attestation extension content is described by the following ASN.1 schema. The ASN.1 schema for the `AuthorizationList` is also used to [import encrypted keys](https://developer.android.com/privacy-and-security/keystore#ImportingEncryptedKeys). Any fields which will not appear in the attestation extension are noted as such.

### Version 500

KeyDescription ::= SEQUENCE {
    attestationVersion           INTEGER, # Value 500
    attestationSecurityLevel     SecurityLevel,
    keyMintVersion               INTEGER, # Value 500
    keyMintSecurityLevel         SecurityLevel,
    attestationChallenge         OCTET\_STRING,
    uniqueId                     OCTET\_STRING,
    softwareEnforced             AuthorizationList,
    hardwareEnforced             AuthorizationList,
}

SecurityLevel ::= ENUMERATED {
    Software                     (0),
    TrustedEnvironment           (1),
    StrongBox                    (2),
}

AuthorizationList ::= SEQUENCE {
    purpose                      \[1\] EXPLICIT SET OF INTEGER OPTIONAL,
    algorithm                    \[2\] EXPLICIT INTEGER OPTIONAL,
    keySize                      \[3\] EXPLICIT INTEGER OPTIONAL,
    blockMode                    \[4\] EXPLICIT SET OF INTEGER OPTIONAL,
    digest                       \[5\] EXPLICIT SET OF INTEGER OPTIONAL,
    padding                      \[6\] EXPLICIT SET OF INTEGER OPTIONAL,
    callerNonce                  \[7\] EXPLICIT NULL OPTIONAL, # Non-attestation
    minMacLength                 \[8\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    ecCurve                     \[10\] EXPLICIT INTEGER OPTIONAL,
    mlDsaVariant                \[11\] EXPLICIT INTEGER OPTIONAL,
    rsaPublicExponent          \[200\] EXPLICIT INTEGER OPTIONAL,
    mgfDigest                  \[203\] EXPLICIT SET OF INTEGER OPTIONAL,
    rollbackResistance         \[303\] EXPLICIT NULL OPTIONAL,
    earlyBootOnly              \[305\] EXPLICIT NULL OPTIONAL,
    activeDateTime             \[400\] EXPLICIT INTEGER OPTIONAL,
    originationExpireDateTime  \[401\] EXPLICIT INTEGER OPTIONAL,
    usageExpireDateTime        \[402\] EXPLICIT INTEGER OPTIONAL,
    usageCountLimit            \[405\] EXPLICIT INTEGER OPTIONAL,
    userSecureId               \[502\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    noAuthRequired             \[503\] EXPLICIT NULL OPTIONAL,
    userAuthType               \[504\] EXPLICIT INTEGER OPTIONAL,
    authTimeout                \[505\] EXPLICIT INTEGER OPTIONAL,
    allowWhileOnBody           \[506\] EXPLICIT NULL OPTIONAL,
    trustedUserPresenceReq     \[507\] EXPLICIT NULL OPTIONAL,
    trustedConfirmationReq     \[508\] EXPLICIT NULL OPTIONAL,
    unlockedDeviceReq          \[509\] EXPLICIT NULL OPTIONAL,
    creationDateTime           \[701\] EXPLICIT INTEGER OPTIONAL,
    origin                     \[702\] EXPLICIT INTEGER OPTIONAL,
    rootOfTrust                \[704\] EXPLICIT RootOfTrust OPTIONAL,
    osVersion                  \[705\] EXPLICIT INTEGER OPTIONAL,
    osPatchLevel               \[706\] EXPLICIT INTEGER OPTIONAL,
    attestationApplicationId   \[709\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdBrand         \[710\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdDevice        \[711\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdProduct       \[712\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdSerial        \[713\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdImei          \[714\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdMeid          \[715\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdManufacturer  \[716\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdModel         \[717\] EXPLICIT OCTET\_STRING OPTIONAL,
    vendorPatchLevel           \[718\] EXPLICIT INTEGER OPTIONAL,
    bootPatchLevel             \[719\] EXPLICIT INTEGER OPTIONAL,
    deviceUniqueAttestation    \[720\] EXPLICIT NULL OPTIONAL,
    attestationIdSecondImei    \[723\] EXPLICIT OCTET\_STRING OPTIONAL,
    moduleHash                 \[724\] EXPLICIT OCTET\_STRING OPTIONAL,
}

Modules ::= SET OF Module
Module ::= SEQUENCE {
    packageName                OCTET\_STRING,
    version                    INTEGER,
}

RootOfTrust ::= SEQUENCE {
    verifiedBootKey            OCTET\_STRING,
    deviceLocked               BOOLEAN,
    verifiedBootState          VerifiedBootState,
    verifiedBootHash           OCTET\_STRING,
}

VerifiedBootState ::= ENUMERATED {
    Verified                   (0),
    SelfSigned                 (1),
    Unverified                 (2),
    Failed                     (3),
}

### Version 400

KeyDescription ::= SEQUENCE {
    attestationVersion           INTEGER, # Value 400
    attestationSecurityLevel     SecurityLevel,
    keyMintVersion               INTEGER, # Value 400
    keyMintSecurityLevel         SecurityLevel,
    attestationChallenge         OCTET\_STRING,
    uniqueId                     OCTET\_STRING,
    softwareEnforced             AuthorizationList,
    hardwareEnforced             AuthorizationList,
}

SecurityLevel ::= ENUMERATED {
    Software                     (0),
    TrustedEnvironment           (1),
    StrongBox                    (2),
}

AuthorizationList ::= SEQUENCE {
    purpose                      \[1\] EXPLICIT SET OF INTEGER OPTIONAL,
    algorithm                    \[2\] EXPLICIT INTEGER OPTIONAL,
    keySize                      \[3\] EXPLICIT INTEGER OPTIONAL,
    blockMode                    \[4\] EXPLICIT SET OF INTEGER OPTIONAL,
    digest                       \[5\] EXPLICIT SET OF INTEGER OPTIONAL,
    padding                      \[6\] EXPLICIT SET OF INTEGER OPTIONAL,
    callerNonce                  \[7\] EXPLICIT NULL OPTIONAL, # Non-attestation
    minMacLength                 \[8\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    ecCurve                     \[10\] EXPLICIT INTEGER OPTIONAL,
    rsaPublicExponent          \[200\] EXPLICIT INTEGER OPTIONAL,
    mgfDigest                  \[203\] EXPLICIT SET OF INTEGER OPTIONAL,
    rollbackResistance         \[303\] EXPLICIT NULL OPTIONAL,
    earlyBootOnly              \[305\] EXPLICIT NULL OPTIONAL,
    activeDateTime             \[400\] EXPLICIT INTEGER OPTIONAL,
    originationExpireDateTime  \[401\] EXPLICIT INTEGER OPTIONAL,
    usageExpireDateTime        \[402\] EXPLICIT INTEGER OPTIONAL,
    usageCountLimit            \[405\] EXPLICIT INTEGER OPTIONAL,
    userSecureId               \[502\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    noAuthRequired             \[503\] EXPLICIT NULL OPTIONAL,
    userAuthType               \[504\] EXPLICIT INTEGER OPTIONAL,
    authTimeout                \[505\] EXPLICIT INTEGER OPTIONAL,
    allowWhileOnBody           \[506\] EXPLICIT NULL OPTIONAL,
    trustedUserPresenceReq     \[507\] EXPLICIT NULL OPTIONAL,
    trustedConfirmationReq     \[508\] EXPLICIT NULL OPTIONAL,
    unlockedDeviceReq          \[509\] EXPLICIT NULL OPTIONAL,
    creationDateTime           \[701\] EXPLICIT INTEGER OPTIONAL,
    origin                     \[702\] EXPLICIT INTEGER OPTIONAL,
    rootOfTrust                \[704\] EXPLICIT RootOfTrust OPTIONAL,
    osVersion                  \[705\] EXPLICIT INTEGER OPTIONAL,
    osPatchLevel               \[706\] EXPLICIT INTEGER OPTIONAL,
    attestationApplicationId   \[709\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdBrand         \[710\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdDevice        \[711\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdProduct       \[712\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdSerial        \[713\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdImei          \[714\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdMeid          \[715\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdManufacturer  \[716\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdModel         \[717\] EXPLICIT OCTET\_STRING OPTIONAL,
    vendorPatchLevel           \[718\] EXPLICIT INTEGER OPTIONAL,
    bootPatchLevel             \[719\] EXPLICIT INTEGER OPTIONAL,
    deviceUniqueAttestation    \[720\] EXPLICIT NULL OPTIONAL,
    attestationIdSecondImei    \[723\] EXPLICIT OCTET\_STRING OPTIONAL,
    moduleHash                 \[724\] EXPLICIT OCTET\_STRING OPTIONAL,
}

Modules ::= SET OF Module
Module ::= SEQUENCE {
    packageName                OCTET\_STRING,
    version                    INTEGER,
}

RootOfTrust ::= SEQUENCE {
    verifiedBootKey            OCTET\_STRING,
    deviceLocked               BOOLEAN,
    verifiedBootState          VerifiedBootState,
    verifiedBootHash           OCTET\_STRING,
}

VerifiedBootState ::= ENUMERATED {
    Verified                   (0),
    SelfSigned                 (1),
    Unverified                 (2),
    Failed                     (3),
}

### Version 300

KeyDescription ::= SEQUENCE {
    attestationVersion           INTEGER, # Value 300
    attestationSecurityLevel     SecurityLevel,
    keyMintVersion               INTEGER, # Value 300
    keymintSecurityLevel         SecurityLevel,
    attestationChallenge         OCTET\_STRING,
    uniqueId                     OCTET\_STRING,
    softwareEnforced             AuthorizationList,
    hardwareEnforced             AuthorizationList,
}

SecurityLevel ::= ENUMERATED {
    Software                     (0),
    TrustedEnvironment           (1),
    StrongBox                    (2),
}

AuthorizationList ::= SEQUENCE {
    purpose                      \[1\] EXPLICIT SET OF INTEGER OPTIONAL,
    algorithm                    \[2\] EXPLICIT INTEGER OPTIONAL,
    keySize                      \[3\] EXPLICIT INTEGER OPTIONAL,
    blockMode                    \[4\] EXPLICIT SET OF INTEGER OPTIONAL,
    digest                       \[5\] EXPLICIT SET OF INTEGER OPTIONAL,
    padding                      \[6\] EXPLICIT SET OF INTEGER OPTIONAL,
    callerNonce                  \[7\] EXPLICIT NULL OPTIONAL, # Non-attestation
    minMacLength                 \[8\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    ecCurve                     \[10\] EXPLICIT INTEGER OPTIONAL,
    rsaPublicExponent          \[200\] EXPLICIT INTEGER OPTIONAL,
    mgfDigest                  \[203\] EXPLICIT SET OF INTEGER OPTIONAL,
    rollbackResistance         \[303\] EXPLICIT NULL OPTIONAL,
    earlyBootOnly              \[305\] EXPLICIT NULL OPTIONAL,
    activeDateTime             \[400\] EXPLICIT INTEGER OPTIONAL,
    originationExpireDateTime  \[401\] EXPLICIT INTEGER OPTIONAL,
    usageExpireDateTime        \[402\] EXPLICIT INTEGER OPTIONAL,
    usageCountLimit            \[405\] EXPLICIT INTEGER OPTIONAL,
    userSecureId               \[502\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    noAuthRequired             \[503\] EXPLICIT NULL OPTIONAL,
    userAuthType               \[504\] EXPLICIT INTEGER OPTIONAL,
    authTimeout                \[505\] EXPLICIT INTEGER OPTIONAL,
    allowWhileOnBody           \[506\] EXPLICIT NULL OPTIONAL,
    trustedUserPresenceReq     \[507\] EXPLICIT NULL OPTIONAL,
    trustedConfirmationReq     \[508\] EXPLICIT NULL OPTIONAL,
    unlockedDeviceReq          \[509\] EXPLICIT NULL OPTIONAL,
    creationDateTime           \[701\] EXPLICIT INTEGER OPTIONAL,
    origin                     \[702\] EXPLICIT INTEGER OPTIONAL,
    rootOfTrust                \[704\] EXPLICIT RootOfTrust OPTIONAL,
    osVersion                  \[705\] EXPLICIT INTEGER OPTIONAL,
    osPatchLevel               \[706\] EXPLICIT INTEGER OPTIONAL,
    attestationApplicationId   \[709\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdBrand         \[710\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdDevice        \[711\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdProduct       \[712\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdSerial        \[713\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdImei          \[714\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdMeid          \[715\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdManufacturer  \[716\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdModel         \[717\] EXPLICIT OCTET\_STRING OPTIONAL,
    vendorPatchLevel           \[718\] EXPLICIT INTEGER OPTIONAL,
    bootPatchLevel             \[719\] EXPLICIT INTEGER OPTIONAL,
    deviceUniqueAttestation    \[720\] EXPLICIT NULL OPTIONAL,
    attestationIdSecondImei    \[723\] EXPLICIT OCTET\_STRING OPTIONAL,
}

RootOfTrust ::= SEQUENCE {
    verifiedBootKey            OCTET\_STRING,
    deviceLocked               BOOLEAN,
    verifiedBootState          VerifiedBootState,
    verifiedBootHash           OCTET\_STRING,
}

VerifiedBootState ::= ENUMERATED {
    Verified                   (0),
    SelfSigned                 (1),
    Unverified                 (2),
    Failed                     (3),
}

### Version 200

KeyDescription ::= SEQUENCE {
    attestationVersion           INTEGER, # Value 200
    attestationSecurityLevel     SecurityLevel,
    keyMintVersion               INTEGER, # Value 200
    keymintSecurityLevel         SecurityLevel,
    attestationChallenge         OCTET\_STRING,
    uniqueId                     OCTET\_STRING,
    softwareEnforced             AuthorizationList,
    hardwareEnforced             AuthorizationList,
}

SecurityLevel ::= ENUMERATED {
    Software                     (0),
    TrustedEnvironment           (1),
    StrongBox                    (2),
}

AuthorizationList ::= SEQUENCE {
    purpose                      \[1\] EXPLICIT SET OF INTEGER OPTIONAL,
    algorithm                    \[2\] EXPLICIT INTEGER OPTIONAL,
    keySize                      \[3\] EXPLICIT INTEGER OPTIONAL,
    blockMode                    \[4\] EXPLICIT SET OF INTEGER OPTIONAL,
    digest                       \[5\] EXPLICIT SET OF INTEGER OPTIONAL,
    padding                      \[6\] EXPLICIT SET OF INTEGER OPTIONAL,
    callerNonce                  \[7\] EXPLICIT NULL OPTIONAL, # Non-attestation
    minMacLength                 \[8\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    ecCurve                     \[10\] EXPLICIT INTEGER OPTIONAL,
    rsaPublicExponent          \[200\] EXPLICIT INTEGER OPTIONAL,
    mgfDigest                  \[203\] EXPLICIT SET OF INTEGER OPTIONAL,
    rollbackResistance         \[303\] EXPLICIT NULL OPTIONAL,
    earlyBootOnly              \[305\] EXPLICIT NULL OPTIONAL,
    activeDateTime             \[400\] EXPLICIT INTEGER OPTIONAL,
    originationExpireDateTime  \[401\] EXPLICIT INTEGER OPTIONAL,
    usageExpireDateTime        \[402\] EXPLICIT INTEGER OPTIONAL,
    usageCountLimit            \[405\] EXPLICIT INTEGER OPTIONAL,
    userSecureId               \[502\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    noAuthRequired             \[503\] EXPLICIT NULL OPTIONAL,
    userAuthType               \[504\] EXPLICIT INTEGER OPTIONAL,
    authTimeout                \[505\] EXPLICIT INTEGER OPTIONAL,
    allowWhileOnBody           \[506\] EXPLICIT NULL OPTIONAL,
    trustedUserPresenceReq     \[507\] EXPLICIT NULL OPTIONAL,
    trustedConfirmationReq     \[508\] EXPLICIT NULL OPTIONAL,
    unlockedDeviceReq          \[509\] EXPLICIT NULL OPTIONAL,
    creationDateTime           \[701\] EXPLICIT INTEGER OPTIONAL,
    origin                     \[702\] EXPLICIT INTEGER OPTIONAL,
    rootOfTrust                \[704\] EXPLICIT RootOfTrust OPTIONAL,
    osVersion                  \[705\] EXPLICIT INTEGER OPTIONAL,
    osPatchLevel               \[706\] EXPLICIT INTEGER OPTIONAL,
    attestationApplicationId   \[709\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdBrand         \[710\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdDevice        \[711\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdProduct       \[712\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdSerial        \[713\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdImei          \[714\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdMeid          \[715\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdManufacturer  \[716\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdModel         \[717\] EXPLICIT OCTET\_STRING OPTIONAL,
    vendorPatchLevel           \[718\] EXPLICIT INTEGER OPTIONAL,
    bootPatchLevel             \[719\] EXPLICIT INTEGER OPTIONAL,
    deviceUniqueAttestation    \[720\] EXPLICIT NULL OPTIONAL,
}

RootOfTrust ::= SEQUENCE {
    verifiedBootKey            OCTET\_STRING,
    deviceLocked               BOOLEAN,
    verifiedBootState          VerifiedBootState,
    verifiedBootHash           OCTET\_STRING,
}

VerifiedBootState ::= ENUMERATED {
    Verified                   (0),
    SelfSigned                 (1),
    Unverified                 (2),
    Failed                     (3),
}

### Version 100

KeyDescription ::= SEQUENCE {
    attestationVersion           INTEGER, # Value 100
    attestationSecurityLevel     SecurityLevel,
    keyMintVersion               INTEGER, # Value 100
    keymintSecurityLevel         SecurityLevel,
    attestationChallenge         OCTET\_STRING,
    uniqueId                     OCTET\_STRING,
    softwareEnforced             AuthorizationList,
    hardwareEnforced             AuthorizationList,
}

SecurityLevel ::= ENUMERATED {
    Software                     (0),
    TrustedEnvironment           (1),
    StrongBox                    (2),
}

AuthorizationList ::= SEQUENCE {
    purpose                      \[1\] EXPLICIT SET OF INTEGER OPTIONAL,
    algorithm                    \[2\] EXPLICIT INTEGER OPTIONAL,
    keySize                      \[3\] EXPLICIT INTEGER OPTIONAL,
    digest                       \[5\] EXPLICIT SET OF INTEGER OPTIONAL,
    padding                      \[6\] EXPLICIT SET OF INTEGER OPTIONAL,
    callerNonce                  \[7\] EXPLICIT NULL OPTIONAL, # Non-attestation
    minMacLength                 \[8\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    ecCurve                     \[10\] EXPLICIT INTEGER OPTIONAL,
    rsaPublicExponent          \[200\] EXPLICIT INTEGER OPTIONAL,
    mgfDigest                  \[203\] EXPLICIT SET OF INTEGER OPTIONAL,
    rollbackResistance         \[303\] EXPLICIT NULL OPTIONAL,
    earlyBootOnly              \[305\] EXPLICIT NULL OPTIONAL,
    activeDateTime             \[400\] EXPLICIT INTEGER OPTIONAL,
    originationExpireDateTime  \[401\] EXPLICIT INTEGER OPTIONAL,
    usageExpireDateTime        \[402\] EXPLICIT INTEGER OPTIONAL,
    usageCountLimit            \[405\] EXPLICIT INTEGER OPTIONAL,
    userSecureId               \[502\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    noAuthRequired             \[503\] EXPLICIT NULL OPTIONAL,
    userAuthType               \[504\] EXPLICIT INTEGER OPTIONAL,
    authTimeout                \[505\] EXPLICIT INTEGER OPTIONAL,
    allowWhileOnBody           \[506\] EXPLICIT NULL OPTIONAL,
    trustedUserPresenceReq     \[507\] EXPLICIT NULL OPTIONAL,
    trustedConfirmationReq     \[508\] EXPLICIT NULL OPTIONAL,
    unlockedDeviceReq          \[509\] EXPLICIT NULL OPTIONAL,
    creationDateTime           \[701\] EXPLICIT INTEGER OPTIONAL,
    origin                     \[702\] EXPLICIT INTEGER OPTIONAL,
    rootOfTrust                \[704\] EXPLICIT RootOfTrust OPTIONAL,
    osVersion                  \[705\] EXPLICIT INTEGER OPTIONAL,
    osPatchLevel               \[706\] EXPLICIT INTEGER OPTIONAL,
    attestationApplicationId   \[709\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdBrand         \[710\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdDevice        \[711\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdProduct       \[712\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdSerial        \[713\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdImei          \[714\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdMeid          \[715\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdManufacturer  \[716\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdModel         \[717\] EXPLICIT OCTET\_STRING OPTIONAL,
    vendorPatchLevel           \[718\] EXPLICIT INTEGER OPTIONAL,
    bootPatchLevel             \[719\] EXPLICIT INTEGER OPTIONAL,
    deviceUniqueAttestation    \[720\] EXPLICIT NULL OPTIONAL,
}

RootOfTrust ::= SEQUENCE {
    verifiedBootKey            OCTET\_STRING,
    deviceLocked               BOOLEAN,
    verifiedBootState          VerifiedBootState,
    verifiedBootHash           OCTET\_STRING,
}

VerifiedBootState ::= ENUMERATED {
    Verified                   (0),
    SelfSigned                 (1),
    Unverified                 (2),
    Failed                     (3),
}

### Version 4

KeyDescription ::= SEQUENCE {
    attestationVersion           INTEGER, # Value 4
    attestationSecurityLevel     SecurityLevel,
    keymasterVersion             INTEGER, # Value 41
    keymasterSecurityLevel       SecurityLevel,
    attestationChallenge         OCTET\_STRING,
    uniqueId                     OCTET\_STRING,
    softwareEnforced             AuthorizationList,
    hardwareEnforced             AuthorizationList,
}

SecurityLevel ::= ENUMERATED {
    Software                     (0),
    TrustedEnvironment           (1),
    StrongBox                    (2),
}

AuthorizationList ::= SEQUENCE {
    purpose                      \[1\] EXPLICIT SET OF INTEGER OPTIONAL,
    algorithm                    \[2\] EXPLICIT INTEGER OPTIONAL,
    keySize                      \[3\] EXPLICIT INTEGER OPTIONAL,
    blockMode                    \[4\] EXPLICIT SET OF INTEGER OPTIONAL,
    digest                       \[5\] EXPLICIT SET OF INTEGER OPTIONAL,
    padding                      \[6\] EXPLICIT SET OF INTEGER OPTIONAL,
    callerNonce                  \[7\] EXPLICIT NULL OPTIONAL, # Non-attestation
    minMacLength                 \[8\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    ecCurve                     \[10\] EXPLICIT INTEGER OPTIONAL,
    rsaPublicExponent          \[200\] EXPLICIT INTEGER OPTIONAL,
    rollbackResistance         \[303\] EXPLICIT NULL OPTIONAL,
    activeDateTime             \[400\] EXPLICIT INTEGER OPTIONAL,
    originationExpireDateTime  \[401\] EXPLICIT INTEGER OPTIONAL,
    usageExpireDateTime        \[402\] EXPLICIT INTEGER OPTIONAL,
    userSecureId               \[502\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    noAuthRequired             \[503\] EXPLICIT NULL OPTIONAL,
    userAuthType               \[504\] EXPLICIT INTEGER OPTIONAL,
    authTimeout                \[505\] EXPLICIT INTEGER OPTIONAL,
    allowWhileOnBody           \[506\] EXPLICIT NULL OPTIONAL,
    trustedUserPresenceReq     \[507\] EXPLICIT NULL OPTIONAL,
    trustedConfirmationReq     \[508\] EXPLICIT NULL OPTIONAL,
    unlockedDeviceReq          \[509\] EXPLICIT NULL OPTIONAL,
    creationDateTime           \[701\] EXPLICIT INTEGER OPTIONAL,
    origin                     \[702\] EXPLICIT INTEGER OPTIONAL,
    rootOfTrust                \[704\] EXPLICIT RootOfTrust OPTIONAL,
    osVersion                  \[705\] EXPLICIT INTEGER OPTIONAL,
    osPatchLevel               \[706\] EXPLICIT INTEGER OPTIONAL,
    attestationApplicationId   \[709\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdBrand         \[710\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdDevice        \[711\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdProduct       \[712\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdSerial        \[713\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdImei          \[714\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdMeid          \[715\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdManufacturer  \[716\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdModel         \[717\] EXPLICIT OCTET\_STRING OPTIONAL,
    vendorPatchLevel           \[718\] EXPLICIT INTEGER OPTIONAL,
    bootPatchLevel             \[719\] EXPLICIT INTEGER OPTIONAL,
    deviceUniqueAttestation    \[720\] EXPLICIT NULL OPTIONAL,
}

RootOfTrust ::= SEQUENCE {
    verifiedBootKey            OCTET\_STRING,
    deviceLocked               BOOLEAN,
    verifiedBootState          VerifiedBootState,
    verifiedBootHash           OCTET\_STRING,
}

VerifiedBootState ::= ENUMERATED {
    Verified                   (0),
    SelfSigned                 (1),
    Unverified                 (2),
    Failed                     (3),
}

### Version 3

KeyDescription ::= SEQUENCE {
    attestationVersion           INTEGER, # Value 3
    attestationSecurityLevel     SecurityLevel,
    keymasterVersion             INTEGER, # Value 4
    keymasterSecurityLevel       SecurityLevel,
    attestationChallenge         OCTET\_STRING,
    uniqueId                     OCTET\_STRING,
    softwareEnforced             AuthorizationList,
    hardwareEnforced             AuthorizationList,
}

SecurityLevel ::= ENUMERATED {
    Software                     (0),
    TrustedEnvironment           (1),
    StrongBox                    (2),
}

AuthorizationList ::= SEQUENCE {
    purpose                      \[1\] EXPLICIT SET OF INTEGER OPTIONAL,
    algorithm                    \[2\] EXPLICIT INTEGER OPTIONAL,
    keySize                      \[3\] EXPLICIT INTEGER OPTIONAL,
    blockMode                    \[4\] EXPLICIT SET OF INTEGER OPTIONAL,
    digest                       \[5\] EXPLICIT SET OF INTEGER OPTIONAL,
    padding                      \[6\] EXPLICIT SET OF INTEGER OPTIONAL,
    callerNonce                  \[7\] EXPLICIT NULL OPTIONAL, # Non-attestation
    minMacLength                 \[8\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    ecCurve                     \[10\] EXPLICIT INTEGER OPTIONAL,
    rsaPublicExponent          \[200\] EXPLICIT INTEGER OPTIONAL,
    rollbackResistance         \[303\] EXPLICIT NULL OPTIONAL,
    activeDateTime             \[400\] EXPLICIT INTEGER OPTIONAL,
    originationExpireDateTime  \[401\] EXPLICIT INTEGER OPTIONAL,
    usageExpireDateTime        \[402\] EXPLICIT INTEGER OPTIONAL,
    userSecureId               \[502\] EXPLICIT INTEGER OPTIONAL, # Non-attestation
    noAuthRequired             \[503\] EXPLICIT NULL OPTIONAL,
    userAuthType               \[504\] EXPLICIT INTEGER OPTIONAL,
    authTimeout                \[505\] EXPLICIT INTEGER OPTIONAL,
    allowWhileOnBody           \[506\] EXPLICIT NULL OPTIONAL,
    trustedUserPresenceReq     \[507\] EXPLICIT NULL OPTIONAL,
    trustedConfirmationReq     \[508\] EXPLICIT NULL OPTIONAL,
    unlockedDeviceReq          \[509\] EXPLICIT NULL OPTIONAL,
    creationDateTime           \[701\] EXPLICIT INTEGER OPTIONAL,
    origin                     \[702\] EXPLICIT INTEGER OPTIONAL,
    rootOfTrust                \[704\] EXPLICIT RootOfTrust OPTIONAL,
    osVersion                  \[705\] EXPLICIT INTEGER OPTIONAL,
    osPatchLevel               \[706\] EXPLICIT INTEGER OPTIONAL,
    attestationApplicationId   \[709\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdBrand         \[710\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdDevice        \[711\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdProduct       \[712\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdSerial        \[713\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdImei          \[714\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdMeid          \[715\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdManufacturer  \[716\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdModel         \[717\] EXPLICIT OCTET\_STRING OPTIONAL,
    vendorPatchLevel           \[718\] EXPLICIT INTEGER OPTIONAL,
    bootPatchLevel             \[719\] EXPLICIT INTEGER OPTIONAL,
}

RootOfTrust ::= SEQUENCE {
    verifiedBootKey            OCTET\_STRING,
    deviceLocked               BOOLEAN,
    verifiedBootState          VerifiedBootState,
    verifiedBootHash           OCTET\_STRING,
}

VerifiedBootState ::= ENUMERATED {
    Verified                   (0),
    SelfSigned                 (1),
    Unverified                 (2),
    Failed                     (3),
}

### Version 2

KeyDescription ::= SEQUENCE {
    attestationVersion           INTEGER, # Value 2
    attestationSecurityLevel     SecurityLevel,
    keymasterVersion             INTEGER, # Value 3
    keymasterSecurityLevel       SecurityLevel,
    attestationChallenge         OCTET\_STRING,
    uniqueId                     OCTET\_STRING,
    softwareEnforced             AuthorizationList,
    hardwareEnforced             AuthorizationList,
}

SecurityLevel ::= ENUMERATED {
    Software                     (0),
    TrustedEnvironment           (1),
}

AuthorizationList ::= SEQUENCE {
    purpose                      \[1\] EXPLICIT SET OF INTEGER OPTIONAL,
    algorithm                    \[2\] EXPLICIT INTEGER OPTIONAL,
    keySize                      \[3\] EXPLICIT INTEGER OPTIONAL,
    digest                       \[5\] EXPLICIT SET OF INTEGER OPTIONAL,
    padding                      \[6\] EXPLICIT SET OF INTEGER OPTIONAL,
    ecCurve                     \[10\] EXPLICIT INTEGER OPTIONAL,
    rsaPublicExponent          \[200\] EXPLICIT INTEGER OPTIONAL,
    activeDateTime             \[400\] EXPLICIT INTEGER OPTIONAL,
    originationExpireDateTime  \[401\] EXPLICIT INTEGER OPTIONAL,
    usageExpireDateTime        \[402\] EXPLICIT INTEGER OPTIONAL,
    noAuthRequired             \[503\] EXPLICIT NULL OPTIONAL,
    userAuthType               \[504\] EXPLICIT INTEGER OPTIONAL,
    authTimeout                \[505\] EXPLICIT INTEGER OPTIONAL,
    allowWhileOnBody           \[506\] EXPLICIT NULL OPTIONAL,
    allApplications            \[600\] EXPLICIT NULL OPTIONAL,
    creationDateTime           \[701\] EXPLICIT INTEGER OPTIONAL,
    origin                     \[702\] EXPLICIT INTEGER OPTIONAL,
    rollbackResistant          \[703\] EXPLICIT NULL OPTIONAL,
    rootOfTrust                \[704\] EXPLICIT RootOfTrust OPTIONAL,
    osVersion                  \[705\] EXPLICIT INTEGER OPTIONAL,
    osPatchLevel               \[706\] EXPLICIT INTEGER OPTIONAL,
    attestationApplicationId   \[709\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdBrand         \[710\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdDevice        \[711\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdProduct       \[712\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdSerial        \[713\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdImei          \[714\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdMeid          \[715\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdManufacturer  \[716\] EXPLICIT OCTET\_STRING OPTIONAL,
    attestationIdModel         \[717\] EXPLICIT OCTET\_STRING OPTIONAL,
}

RootOfTrust ::= SEQUENCE {
    verifiedBootKey           OCTET\_STRING,
    deviceLocked              BOOLEAN,
    verifiedBootState         VerifiedBootState,
}

VerifiedBootState ::= ENUMERATED {
    Verified                  (0),
    SelfSigned                (1),
    Unverified                (2),
    Failed                    (3),
}

### Version 1

KeyDescription ::= SEQUENCE {
    attestationVersion           INTEGER, # Value 1
    attestationSecurityLevel     SecurityLevel,
    keymasterVersion             INTEGER, # Value 2
    keymasterSecurityLevel       SecurityLevel,
    attestationChallenge         OCTET\_STRING,
    uniqueId                     OCTET\_STRING,
    softwareEnforced             AuthorizationList,
    hardwareEnforced             AuthorizationList,
}

SecurityLevel ::= ENUMERATED {
    Software                     (0),
    TrustedEnvironment           (1),
}

AuthorizationList ::= SEQUENCE {
    purpose                      \[1\] EXPLICIT SET OF INTEGER OPTIONAL,
    algorithm                    \[2\] EXPLICIT INTEGER OPTIONAL,
    keySize                      \[3\] EXPLICIT INTEGER OPTIONAL,
    digest                       \[5\] EXPLICIT SET OF INTEGER OPTIONAL,
    padding                      \[6\] EXPLICIT SET OF INTEGER OPTIONAL,
    ecCurve                     \[10\] EXPLICIT INTEGER OPTIONAL,
    rsaPublicExponent          \[200\] EXPLICIT INTEGER OPTIONAL,
    activeDateTime             \[400\] EXPLICIT INTEGER OPTIONAL,
    originationExpireDateTime  \[401\] EXPLICIT INTEGER OPTIONAL,
    usageExpireDateTime        \[402\] EXPLICIT INTEGER OPTIONAL,
    noAuthRequired             \[503\] EXPLICIT NULL OPTIONAL,
    userAuthType               \[504\] EXPLICIT INTEGER OPTIONAL,
    authTimeout                \[505\] EXPLICIT INTEGER OPTIONAL,
    allowWhileOnBody           \[506\] EXPLICIT NULL OPTIONAL,
    allApplications            \[600\] EXPLICIT NULL OPTIONAL,
    creationDateTime           \[701\] EXPLICIT INTEGER OPTIONAL,
    origin                     \[702\] EXPLICIT INTEGER OPTIONAL,
    rollbackResistant          \[703\] EXPLICIT NULL OPTIONAL,
    rootOfTrust                \[704\] EXPLICIT RootOfTrust OPTIONAL,
    osVersion                  \[705\] EXPLICIT INTEGER OPTIONAL,
    osPatchLevel               \[706\] EXPLICIT INTEGER OPTIONAL,
}

RootOfTrust ::= SEQUENCE {
    verifiedBootKey            OCTET\_STRING,
    deviceLocked               BOOLEAN,
    verifiedBootState          VerifiedBootState,
}

VerifiedBootState ::= ENUMERATED {
    Verified                   (0),
    SelfSigned                 (1),
    Unverified                 (2),
    Failed                     (3),
}

#### KeyDescription fields

`attestationVersion`

The ASN.1 schema version.  

| Value | KeyMint or Keymaster version |
| --- | --- |
| 1 | Keymaster version 2.0 |
| 2 | Keymaster version 3.0 |
| 3 | Keymaster version 4.0 |
| 4 | Keymaster version 4.1 |
| 100 | KeyMint version 1.0 |
| 200 | KeyMint version 2.0 |
| 300 | KeyMint version 3.0 |
| 400 | KeyMint version 4.0 |
| 500 | KeyMint version 5.0 |

`attestationSecurityLevel`

The [security level](#securitylevel-values) of the location where the attested key is stored.

`keymasterVersion` / `keyMintVersion`

The version of the KeyMint or Keymaster HAL implementation.  

| Value | KeyMint or Keymaster version |
| --- | --- |
| 2 | Keymaster version 2.0 |
| 3 | Keymaster version 3.0 |
| 4 | Keymaster version 4.0 |
| 41 | Keymaster version 4.1 |
| 100 | KeyMint version 1.0 |
| 200 | KeyMint version 2.0 |
| 300 | KeyMint version 3.0 |
| 400 | KeyMint version 4.0 |
| 500 | KeyMint version 5.0 |

`keymasterSecurityLevel` / `keyMintSecurityLevel`

The [security level](#securitylevel-values) of the KeyMint or Keymaster implementation.

`attestationChallenge`

The challenge provided at key generation time.

`uniqueId`

A privacy-sensitive device identifier that system apps can request at key generation time. If the unique ID is not requested, this field is empty. For details, see the [Unique ID](#unique-id) section.

`softwareEnforced`

The KeyMint or Keymaster [authorization list](#authorizationlist-fields) that is enforced by the Android system. This information is collected or generated by code in the platform. It can be trusted as long as the device is running an operating system that complies with the [Android Platform Security Model](https://arxiv.org/pdf/1904.05572) (that is, the device's bootloader is locked and the [`verifiedBootState`](#verifiedbootstate-values) is `Verified`).

`hardwareEnforced`

The KeyMint or Keymaster [authorization list](#authorizationlist-fields) that is enforced by the device's Trusted Execution Environment (TEE) or [StrongBox](https://developer.android.com/privacy-and-security/keystore#HardwareSecurityModule). This information is collected or generated by code in the secure hardware and is not controlled by the platform. For example, information can come from the bootloader or through a secure communication channel that does not involve trusting the platform.

#### SecurityLevel values

The `SecurityLevel` value indicates the extent to which a Keystore-related element (for example, key pair and attestation) is resilient to attack.

| Value | Meaning |
| --- | --- |
| `Software` | Secure as long as the device's Android system complies with the [Android Platform Security Model](https://arxiv.org/pdf/1904.05572) (that is, the device's bootloader is locked and the [`verifiedBootState`](#verifiedbootstate-values) is `Verified`). |
| `TrustedEnvironment` | Secure as long as the TEE is not compromised. The isolation requirements for TEEs are defined in [sections 9.11 \[C-1-1\] through \[C-1-4\]](/docs/compatibility/android-cdd#911_keys_and_credentials) of the Android Compatibility Definition Document. TEEs are highly resistant to remote compromise and moderately resistant to compromise by direct hardware attack. |
| `StrongBox` | Secure as long as StrongBox is not compromised. StrongBox is implemented in a secure element similar to a hardware security module. The implementation requirements for StrongBox are defined in [section 9.11.2](/docs/compatibility/15/android-15-cdd#9112_strongbox) of the Android Compatibility Definition Document. StrongBox is highly resistant to remote compromise and compromise by direct hardware attack (for example, physical tampering and side-channel attacks). |

#### AuthorizationList fields

Each field corresponds to a Keymaster/KeyMint authorization tag from the [AIDL interface specification](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/interfaces/security/keymint/aidl/android/hardware/security/keymint/Tag.aidl). The specification is the source of truth about authorization tags: their meaning, the format of their contents, whether they are expected to appear in the `softwareEnforced` or `hardwareEnforced` fields in the `KeyDescription` object, whether they are mutually exclusive with other tags, etc. All `AuthorizationList` fields are optional.

**Note:** Some tags listed in the specification don't appear in the ASN.1 schema for the attestation extension because, for example, they are not applicable to asymmetric keys (for example, `Tag::MIN_MAC_LENGTH`, `Tag::CALLER_NONCE`) or have no meaning off-device (for example, `Tag::USER_SECURE_ID`).

Each field has an `EXPLICIT` context-specific tag equal to the KeyMint or Keymaster tag number, which enables a more compact representation of the data in the `AuthorizationList`. The ASN.1 parser must therefore know the expected data type for each context-specific tag. For example, `Tag::USER_AUTH_TYPE` is defined as `ENUM | 504`. In the attestation extension schema, the `purpose` field in the `AuthorizationList` is specified as `userAuthType [504] EXPLICIT INTEGER OPTIONAL`. Its ASN.1 encoding will therefore contain the context-specific tag `504` instead of the `UNIVERSAL` class tag for the ASN.1 type `INTEGER`, which is `10`.

The following fields are present in attestations generated by KeyMint 5:

`purpose`

Corresponds to the `Tag::PURPOSE` authorization tag, which uses a tag ID value of 1.

`algorithm`

Corresponds to the `Tag::ALGORITHM` authorization tag, which uses a tag ID value of 2.

In an attestation `AuthorizationList` object, the algorithm value is always `RSA`, `EC`, or `ML_DSA`.

`keySize`

Corresponds to the `Tag::KEY_SIZE` authorization tag, which uses a tag ID value of 3.

`blockMode`

Corresponds to the `Tag::BLOCK_MODE` authorization tag, which uses a tag ID value of 4.

`digest`

Corresponds to the `Tag::DIGEST` authorization tag, which uses a tag ID value of 5.

`padding`

Corresponds to the `Tag::PADDING` authorization tag, which uses a tag ID value of 6.

`callerNonce`

Corresponds to the `Tag::CALLER_NONCE` authorization tag, which uses a tag ID value of 7. This tag is never present in attestations.

`minMacLength`

Corresponds to the `Tag::MIN_MAC_LENGTH` authorization tag, which uses a tag ID value of 8. This tag is never present in attestations.

`ecCurve`

Corresponds to the `Tag::EC_CURVE` authorization tag, which uses a tag ID value of 10.

The set of parameters used to generate an elliptic curve (EC) key pair, which uses ECDSA for signing and verification, within the Android system keystore.

`mlDsaVariant`

_Present only in key attestation version >= 500._

Corresponds to the `Tag::ML_DSA_VARIANT` authorization tag, which uses a tag ID value of 11.

`rsaPublicExponent`

Corresponds to the `Tag::RSA_PUBLIC_EXPONENT` authorization tag, which uses a tag ID value of 200.

`mgfDigest`

_Present only in key attestation version >= 100._

Corresponds to the `Tag::RSA_OAEP_MGF_DIGEST` KeyMint authorization tag, which uses a tag ID value of 203.

`rollbackResistance`

_Present only in key attestation version >= 3._

Corresponds to the `Tag::ROLLBACK_RESISTANCE` authorization tag, which uses a tag ID value of 303.

`earlyBootOnly`

_Present only in key attestation version >= 4._

Corresponds to the `Tag::EARLY_BOOT_ONLY` authorization tag, which uses a tag ID value of 305.

`activeDateTime`

Corresponds to the `Tag::ACTIVE_DATETIME` authorization tag, which uses a tag ID value of 400.

`originationExpireDateTime`

Corresponds to the `Tag::ORIGINATION_EXPIRE_DATETIME` authorization tag, which uses a tag ID value of 401.

`usageExpireDateTime`

Corresponds to the `Tag::USAGE_EXPIRE_DATETIME` authorization tag, which uses a tag ID value of 402.

`usageCountLimit`

Corresponds to the `Tag::USAGE_COUNT_LIMIT` authorization tag, which uses a tag ID value of 405.

`userSecureId`

Corresponds to the `Tag::USER_SECURE_ID` authorization tag, which uses a tag ID value of 502. This tag is never present in attestations.

`noAuthRequired`

Corresponds to the `Tag::NO_AUTH_REQUIRED` authorization tag, which uses a tag ID value of 503.

`userAuthType`

Corresponds to the `Tag::USER_AUTH_TYPE` authorization tag, which uses a tag ID value of 504.

`authTimeout`

Corresponds to the `Tag::AUTH_TIMEOUT` authorization tag, which uses a tag ID value of 505.

`allowWhileOnBody`

Corresponds to the `Tag::ALLOW_WHILE_ON_BODY` authorization tag, which uses a tag ID value of 506.

Allows the key to be used after its authentication timeout period if the user is still wearing the device on their body. Note that a secure on-body sensor determines whether the device is being worn on the user's body.

`trustedUserPresenceReq`

_Present only in key attestation version >= 3._

Corresponds to the `Tag::TRUSTED_USER_PRESENCE_REQUIRED` authorization tag, which uses a tag ID value of 507.

Specifies that this key is usable only if the user has provided proof of physical presence. Several examples include the following:

-   For a StrongBox key, a hardware button hardwired to a pin on the StrongBox device.
-   For a TEE key, fingerprint authentication provides proof of presence as long as the TEE has exclusive control of the scanner and performs the fingerprint matching process.

`trustedConfirmationReq`

_Present only in key attestation version >= 3._

Corresponds to the `Tag::TRUSTED_CONFIRMATION_REQUIRED` authorization tag, which uses a tag ID value of 508.

Specifies that the key is usable only if the user provides confirmation of the data to be signed using an approval token. For more information about how to obtain user confirmation, see [Android Protected Confirmation](https://developer.android.com/privacy-and-security/security-android-protected-confirmation).

**Note:** This tag is only applicable to keys that use the `SIGN` purpose.

`unlockedDeviceReq`

_Present only in key attestation version >= 3._

Corresponds to the `Tag::UNLOCKED_DEVICE_REQUIRED` authorization tag, which uses a tag ID value of 509.

`creationDateTime`

Corresponds to the `Tag::CREATION_DATETIME` authorization tag, which uses a tag ID value of 701.

`origin`

Corresponds to the `Tag::ORIGIN` authorization tag, which uses a tag ID value of 702.

`rootOfTrust`

Corresponds to the `Tag::ROOT_OF_TRUST` authorization tag, which uses a tag ID value of 704.

For more details, see the section describing the [RootOfTrust](#rootoftrust-fields) data structure.

`osVersion`

Corresponds to the `Tag::OS_VERSION` authorization tag, which uses a tag ID value of 705.

The version of the Android operating system associated with the Keymaster, specified as a six-digit integer. For example, version 8.1.0 is represented as 080100.

Only Keymaster version 1.0 or higher includes this value in the authorization list.

`osPatchLevel`

Corresponds to the `Tag::OS_PATCHLEVEL` authorization tag, which uses a tag ID value of 706.

The month and year associated with the security patch that is being used within KeyMint (previously Keymaster), specified as a six-digit integer. For example, the August 2018 patch is represented as 201808.

Prefer using this field over `vendorPatchLevel` or `bootPatchLevel` for checking whether a device has been recently patched.

Only Keymaster version 1.0 or higher includes this value in the authorization list.

`attestationApplicationId`

_Present only in key attestation versions >= 2._

Corresponds to the `Tag::ATTESTATION_APPLICATION_ID` authorization tag, which uses a tag ID value of 709.

For more details, see the section describing the [AttestationApplicationId](#attestationapplicationid-schema) data structure.

`attestationIdBrand`

_Present only in key attestation versions >= 2._

Corresponds to the `Tag::ATTESTATION_ID_BRAND` authorization tag, which uses a tag ID value of 710.

`attestationIdDevice`

_Present only in key attestation versions >= 2._

Corresponds to the `Tag::ATTESTATION_ID_DEVICE` authorization tag, which uses a tag ID value of 711.

`attestationIdProduct`

_Present only in key attestation versions >= 2._

Corresponds to the `Tag::ATTESTATION_ID_PRODUCT` authorization tag, which uses a tag ID value of 712.

`attestationIdSerial`

_Present only in key attestation versions >= 2._

Corresponds to the `Tag::ATTESTATION_ID_SERIAL` authorization tag, which uses a tag ID value of 713.

`attestationIdImei`

_Present only in key attestation versions >= 2._

Corresponds to the `Tag::ATTESTATION_ID_IMEI` authorization tag, which uses a tag ID value of 714.

`attestationIdMeid`

_Present only in key attestation versions >= 2._

Corresponds to the `Tag::ATTESTATION_ID_MEID` authorization tag, which uses a tag ID value of 715.

`attestationIdManufacturer`

_Present only in key attestation versions >= 2._

Corresponds to the `Tag::ATTESTATION_ID_MANUFACTURER` authorization tag, which uses a tag ID value of 716.

`attestationIdModel`

_Present only in key attestation versions >= 2._

Corresponds to the `Tag::ATTESTATION_ID_MODEL` authorization tag, which uses a tag ID value of 717.

`vendorPatchLevel`

_Present only in key attestation versions >= 3._

Corresponds to the `Tag::VENDOR_PATCHLEVEL` authorization tag, which uses a tag ID value of 718.

Specifies the _vendor image_ security patch level that must be installed on the device for this key to be used. The value is the integer formed by taking the security patch level and removing the dashes. For example, if a key were generated on an Android device with the vendor's 2018-08-05 security patch installed, this value would be 20180805.

`bootPatchLevel`

_Present only in key attestation versions >= 3._

Corresponds to the `Tag::BOOT_PATCHLEVEL` authorization tag, which uses a tag ID value of 719.

Specifies the _kernel image_ security patch level that must be installed on the device for this key to be used. The value is the integer formed by taking the security patch level and removing the dashes. For example, if a key were generated on an Android device with the kernel's 2018-08-05 security patch installed, this value would be 20180805.

`deviceUniqueAttestation`

_Present only in key attestation versions >= 4._

Corresponds to the `Tag::DEVICE_UNIQUE_ATTESTATION` authorization tag, which uses a tag ID value of 720.

`attestationIdSecondImei`

_Present only in key attestation versions >= 300._

Corresponds to the `Tag::ATTESTATION_ID_SECOND_IMEI` authorization tag, which uses a tag ID value of 723.

`moduleHash`

_Present only in key attestation versions >= 400._

Corresponds to the `Tag::MODULE_HASH` authorization tag, which uses a tag ID value of 724.

#### RootOfTrust fields

`verifiedBootKey`

A secure hash of the public key used to verify the integrity and authenticity of all code that executes during device boot up as part of [Verified Boot](/docs/security/features/verifiedboot). SHA-256 is recommended.

`deviceLocked`

Whether the device's bootloader is locked. `true` means that the device booted a signed image that was successfully verified by [Verified Boot](/docs/security/features/verifiedboot).

`verifiedBootState`

The device's [Verified Boot state](#verifiedbootstate-values).

`verifiedBootHash`

A digest of all data protected by [Verified Boot](/docs/security/features/verifiedboot). For devices that use the Android Verified Boot reference implementation, this field contains the [VBMeta digest](https://android.googlesource.com/platform/external/avb/+/android17-release/README.md#the-vbmeta-digest).

#### VerifiedBootState values

**Note:** `Verified` might also mean that the OS is being run on an approved test device with an unlocked bootloader. These devices can report the `Verified` state only if an authorized user is logged in.

| Value | Corresponding [boot state](/docs/security/features/verifiedboot/boot-flow) | Meaning |
| --- | --- | --- |
| `Verified` | `GREEN` | A full chain of trust extends from a hardware-protected root of trust to the bootloader and all partitions verified by [Verified Boot](/docs/security/features/verifiedboot). In this state, the `verifiedBootKey` field contains the hash of the [embedded root of trust](/docs/security/features/verifiedboot/device-state#root-of-trust), which is the certificate embedded in the device's ROM by the device manufacturer in the factory. |
| `SelfSigned` | `YELLOW` | Same as `Verified`, except that the verification was done using a [root of trust configured by the user](/docs/security/features/verifiedboot/device-state#user-settable-root-of-trust) instead of the root of trust embedded by the manufacturer in the factory. In this state, the `verifiedBootKey` field contains the hash of the public key configured by the user. |
| `Unverified` | `ORANGE` | The device's bootloader is unlocked, so a chain of trust cannot be established. The device can be freely modified, so the device's integrity must be verified by the user out-of-band. In this state the `verifiedBootKey` field contains 32 bytes of zeroes. |
| `Failed` | `RED` | The device failed verification. In this state, there are no guarantees about the contents of the other `RootOfTrust` fields. |

#### AttestationApplicationId

This field reflects the Android platform's belief as to which apps are allowed to use the secret key material under attestation. It can contain multiple packages if and only if multiple packages share the same UID. The `AttestationApplicationId` field in `AuthorizationList` is of type `OCTET_STRING` and is formatted according to the following ASN.1 schema:

AttestationApplicationId ::= SEQUENCE {
    package\_infos  SET OF AttestationPackageInfo,
    signature\_digests  SET OF OCTET\_STRING,
}

AttestationPackageInfo ::= SEQUENCE {
    package\_name  OCTET\_STRING,
    version  INTEGER,
}

`package_infos`

A set of `AttestationPackageInfo` objects, each providing a package's name and version number.

`signature_digests`

A set of SHA-256 digests of the app's signing certificates. An app can have multiple signing key certificate chains. For each, the "leaf" certificate is digested and placed in the `signature_digests` field. The field name is misleading, since the digested data is the app's signing certificates, not the app signatures, because it is named for the `[Signature](https://developer.android.com/reference/android/content/pm/Signature)` class returned by a call to `[getPackageInfo()](https://developer.android.com/reference/android/content/pm/PackageManager.html#getPackageInfo\(android.content.pm.VersionedPackage,%20int\))`. The following code snippet shows an example set:

{SHA256(PackageInfo.signature\[0\]), SHA256(PackageInfo.signature\[1\]), ...}
    

### Provisioning information extension

The provisioning information extension has OID `1.3.6.1.4.1.11129.2.1.30`. The extension provides information that's known about the device by the provisioning server.

#### Schema

The extension value consists of [Concise Binary Object Representation (CBOR)](https://datatracker.ietf.org/doc/html/rfc8742) data that conforms to this [Concise Data Definition Language (CDDL)](https://tools.ietf.org/html/rfc8610) schema:

  {
        1 : int,       ; certificates issued
        4 : string,    ; validated attested entity (STRONG\_BOX/TEE)
      ? 6 : bool,      ; is lost device
  }

The map is unversioned and new optional fields may be added.

`certs_issued`

An approximate number of certificates issued to the device in the last 30 days. This value can be used as a signal for potential abuse if the value is greater than average by some orders of magnitude.

`validated_attested_entity`

A string indicating the certified origin of the attested key, directly vouched for by the chipset manufacturer. For example, `STRONG_BOX` or `TEE`.

`is_lost_device`

A boolean indicating whether the device has been reported as lost. If present and true, the certificate was provisioned for a device currently marked as lost.

### Attestation keys

Two keys, one RSA and one ECDSA, and the corresponding certificate chains, are securely provisioned into the device.

Android 12 introduces [Remote Key Provisioning](/docs/core/ota/modular-system/remote-key-provisioning). This feature provides devices in the field with per-app ECDSA P-256 attestation certificates, which are shorter-lived than factory-provisioned certificates.

### Unique ID

The Unique ID is a 128-bit value that identifies the device, but only for a limited period of time. The value is computed with:

HMAC\_SHA256(T || C || R, HBK)

Where:

-   `T` is the "temporal counter value", computed by dividing the value of `Tag::CREATION_DATETIME` by 2592000000, dropping any remainder. `T` changes every 30 days (2592000000 = 30 \* 24 \* 60 \* 60 \* 1000).
-   `C` is the value of `Tag::APPLICATION_ID`
-   `R` is 1 if `Tag::RESET_SINCE_ID_ROTATION` is present in the attest\_params parameter to the attest\_key call, or 0 if the tag is not present.
-   `HBK` is a unique hardware-bound secret known to the Trusted Execution Environment and never revealed by it. The secret contains at least 128 bits of entropy and is unique to the individual device (probabilistic uniqueness is acceptable given the 128 bits of entropy). HBK should be derived from fused key material via HMAC or AES\_CMAC.

Truncate the HMAC\_SHA256 output to 128 bits.

### Multiple IMEIs

Android 14 adds support for multiple IMEIs in the Android Key Attestation record. OEMs can implement this feature by adding a KeyMint tag for a second IMEI. It is becoming increasingly common for devices to have multiple cellular radios and OEMs can now support devices with two IMEIs.

OEMs are required to have a secondary IMEI, if present on their devices, to be provisioned to the KeyMint implementation(s) so that those implementations can attest to it in the same way they attest to the first IMEI.

## ID attestation

Android 8.0 includes optional support for ID attestation for devices with Keymaster 3. ID attestation allows the device to provide proof of its hardware identifiers, such as serial number or IMEI. Although an optional feature, it is highly recommended that all Keymaster 3 implementations provide support for it because being able to prove the device's identity enables use cases such as true zero-touch remote configuration to be more secure (because the remote side can be certain it is talking to the right device, not a device spoofing its identity).

**Note:** Because ID attestation is an optional feature, developers can check if it is supported on the device before requesting it. You can do this programmatically by calling `PackageManager.hasSystemFeature()` with the `PackageManager.FEATURE_DEVICE_ID_ATTESTATION` feature string ("android.software.device\_id\_attestation").

ID attestation works by creating copies of the device's hardware identifiers that only the TEE can access before the device leaves the factory. A user can unlock the device's bootloader and change the system software and the identifiers reported by the Android frameworks. The copies of the identifiers held by the TEE cannot be manipulated in this way, ensuring that device ID attestation only attests to the device's original hardware identifiers, thereby thwarting spoofing attempts.

The main API surface for ID attestation builds on top of the existing key attestation mechanism introduced with Keymaster 2. When requesting an attestation certificate for a key held by Keymaster, the caller can request that the device's hardware identifiers be included in the attestation certificate's metadata. If the key is held in the TEE, the certificate chains back to a known root of trust. The recipient of such a certificate can verify that the certificate and its contents, including the hardware identifiers, were written by the TEE. When asked to include hardware identifiers in the attestation certificate, the TEE attests only to the identifiers held in its storage, as populated on the factory floor.

### Storage properties

The storage that holds the device's identifiers needs to have these properties:

-   The values derived from the device's original identifiers are copied to the storage before the device leaves the factory.
-   The `destroyAttestationIds()` method can permanently destroy this copy of the identifier-derived data. Permanent destruction means the data is completely removed so neither a factory reset nor any other procedure performed on the device can restore it. This is especially important for devices where a user has unlocked the bootloader and changed the system software and modified the identifiers returned by Android frameworks.
-   RMA facilities should have the ability to generate fresh copies of the hardware identifier-derived data. This way, a device that passes through RMA can perform ID attestation again. The mechanism used by RMA facilities must be protected so that users cannot invoke it themselves, as that would allow them to obtain attestations of spoofed IDs.
-   No code other than Keymaster trusted app in the TEE is able to read the identifier-derived data kept in the storage.
-   The storage is tamper-evident: If the content of the storage has been modified, the TEE treats it the same as if the copies of the content had been destroyed and refuses all ID attestation attempts. This is implemented by signing or MACing the storage [as described below](#construction).
-   The storage does not hold the original identifiers. Because ID attestation involves a challenge, the caller always supplies the identifiers to be attested. The TEE only needs to verify that these match the values they originally had. Storing secure hashes of the original values rather than the values enables this verification.

### Construction

To create an implementation that has the properties listed above, store the ID-derived values in the following construction S. Do not store other copies of the ID values, excepting the normal places in the system, which a device owner can modify by rooting:

S = D || HMAC(HBK, D)

where:

-   `D = HMAC(HBK, ID1) || HMAC(HBK, ID2) || ... || HMAC(HBK, IDn)`
-   `HMAC` is the HMAC construction with an appropriate secure hash (SHA-256 recommended)
-   `HBK` is a hardware-bound key not used for any other purpose
-   `ID1...IDn` are the original ID values; association of a particular value to a particular index is implementation-dependent, as different devices have different numbers of identifiers
-   `||` represents concatenation

Because the HMAC outputs are fixed size, no headers or other structure are required to be able to find individual ID hashes, or the HMAC of D. In addition to checking provided values to perform attestation, implementations need to validate S by extracting D from S, computing HMAC(HBK, D) and comparing it to the value in S to verify that no individual IDs were modified/corrupted. Also, implementations must use constant-time comparisons for all individual ID elements and the validation of S. Comparison time must be constant regardless of the number of IDs provided and the correct matching of any part of the test.

### Hardware identifiers

ID attestation supports the following hardware identifiers:

1.  Brand name, as returned by `Build.BRAND` in Android
2.  Device name, as returned by `Build.DEVICE` in Android
3.  Product name, as returned by `Build.PRODUCT` in Android
4.  Manufacturer name, as returned by `Build.MANUFACTURER` in Android
5.  Model name, as returned by `Build.MODEL` in Android
6.  Serial number
7.  IMEIs of all radios
8.  MEIDs of all radios

To support device ID attestation, a device attests to these identifiers. All devices running Android have the first six and they are necessary for this feature to work. If the device has any integrated cellular radios, the device must also support attestation for the IMEIs and/or MEIDs of the radios.

ID attestation is requested by performing a key attestation and including the device identifiers to attest in the request. The identifiers are tagged as:

-   `ATTESTATION_ID_BRAND`
-   `ATTESTATION_ID_DEVICE`
-   `ATTESTATION_ID_PRODUCT`
-   `ATTESTATION_ID_MANUFACTURER`
-   `ATTESTATION_ID_MODEL`
-   `ATTESTATION_ID_SERIAL`
-   `ATTESTATION_ID_IMEI`
-   `ATTESTATION_ID_MEID`

The identifier to attest is a UTF-8 encoded byte string. This format applies to numerical identifiers, as well. Each identifier to attest is expressed as a UTF-8 encoded string.

If the device does not support ID attestation (or `destroyAttestationIds()` was previously called and the device can no longer attest its IDs), any key attestation request that includes one or more of these tags fails with `ErrorCode::CANNOT_ATTEST_IDS`.

If the device supports ID attestation and one or more of the above tags have been included in a key attestation request, the TEE verifies the identifier supplied with each of the tags matches its copy of the hardware identifiers. If one or more identifiers do not match, the entire attestation fails with `ErrorCode::CANNOT_ATTEST_IDS`. It is valid for the same tag to be supplied multiple times. This can be useful, for example, when attesting IMEIs: A device can have multiple radios with multiple IMEIs. An attestation request is valid if the value supplied with each `ATTESTATION_ID_IMEI` matches one of the device's radios. The same applies to all other tags.

If attestation is successful, the attested IDs is added to the [attestation extension](#attestation-extension) (OID 1.3.6.1.4.1.11129.2.1.17) of the issued attestation certificate, using the [schema from above](#schema). Changes from the Keymaster 2 attestation schema are **bolded**, with comments.

## Java API

This section is informational only. Keymaster implementers neither implement nor use the Java API. This is provided to help implementers understand how the feature is used by apps. System components might use it differently, which is why it's crucial this section not be treated as normative.

## Version binding

In Keymaster 1, all Keymaster keys were cryptographically bound to the device _Root of Trust_, or the Verified Boot key. In Keymaster 2 and 3, all keys are also bound to the operating system and patch level of the system image. This ensures that an attacker who discovers a weakness in an old version of system or TEE software cannot roll a device back to the vulnerable version and use keys created with the newer version. In addition, when a key with a given version and patch level is used on a device that has been upgraded to a newer version or patch level, the key is upgraded before it can be used, and the previous version of the key invalidated. In this way, as the device is upgraded, the keys \*ratchet\* forward along with the device, but any reversion of the device to a previous release causes the keys to be unusable.

To support Treble's modular structure and break the binding of system.img to boot.img, Keymaster 4 changed the key version binding model to have separate patch levels for each partition. This allows each partition to be updated independently, while still providing rollback protection.

To implement this version binding, the KeyMint trusted app (TA) needs a way to securely receive the current OS version and patch levels, and to ensure that the information it receives matches all the information about the running system.

-   Devices with Android Verified Boot (AVB) can put all of the patch levels and the system version in vbmeta, so the bootloader can provide them to Keymaster. For chained partitions, the version info for the partition is in the chained vbmeta. In general, version information should be in the `vbmeta struct` that contains the verification data (hash or hashtree) for a given partition.
-   On devices without AVB:
    -   Verified Boot implementations need to provide a hash of the version metadata to bootloader, so that bootloader can provide the hash to Keymaster.
    -   `boot.img` can continue storing patch level in the header
    -   `system.img` can continue storing patch level and OS version in read-only properties
    -   `vendor.img` stores the patch level in the read-only property `ro.vendor.build.version.security_patch`.
    -   The bootloader can provide a hash of all data validated by Verified Boot to Keymaster.
-   In Android 9, use the following tags to supply version information for the following partitions:
    -   `VENDOR_PATCH_LEVEL`: `vendor` partition
    -   `BOOT_PATCH_LEVEL`: `boot` partition
    -   `OS_PATCH_LEVEL` and `OS_VERSION`: `system` partition. (`OS_VERSION` is removed from the `boot.img` header.
-   Keymaster implementations should treat all patch levels independently. Keys are usable if all version info matches the values associated with a key, and `IKeymaster::upgradeDevice()` rolls to a higher patch level if needed.

## HAL changes

To support version binding and version attestation, Android 7.1 added the tags `Tag::OS_VERSION` and `Tag::OS_PATCHLEVEL` and the methods `configure` and `upgradeKey`. The version tags are automatically added by Keymaster 2+ implementations to all newly generated (or updated) keys. Further, any attempt to use a key that doesn't have an OS version or patch level matching the current system OS version or patch level, respectively, is rejected with `ErrorCode::KEY_REQUIRES_UPGRADE`.

`Tag::OS_VERSION` is a `UINT` value that represents the major, minor, and sub-minor portions of an Android system version as MMmmss, where MM is the major version, mm is the minor version and ss is the sub-minor version. For example 6.1.2 would be represented as 060102.

`Tag::OS_PATCHLEVEL` is a `UINT` value that represents the year and month of the last update to the system as YYYYMM, where YYYY is the four-digit year and MM is the two-digit month. For example, March 2016 would be represented as 201603.

### UpgradeKey

To allow keys to be upgraded to the new OS version and patch level of the system image, Android 7.1 added the `upgradeKey` method to the HAL:

**Keymaster 3**

    upgradeKey(vec keyBlobToUpgrade, vec upgradeParams)
        generates(ErrorCode error, vec upgradedKeyBlob);

**Keymaster 2**

keymaster\_error\_t (\*upgrade\_key)(const struct keymaster2\_device\* dev,
    const keymaster\_key\_blob\_t\* key\_to\_upgrade,
    const keymaster\_key\_param\_set\_t\* upgrade\_params,
    keymaster\_key\_blob\_t\* upgraded\_key);

-   `dev` is the device structure
-   `keyBlobToUpgrade` is the key which needs to be upgraded
-   `upgradeParams` are parameters needed to upgrade the key. These include `Tag::APPLICATION_ID` and `Tag::APPLICATION_DATA`, which are necessary to decrypt the key blob, if they were provided during generation.
-   `upgradedKeyBlob` is the output parameter, used to return the new key blob.

If `upgradeKey` is called with a key blob that cannot be parsed or is otherwise invalid, it returns `ErrorCode::INVALID_KEY_BLOB`. If it is called with a key whose patch level is greater than the current system value, it returns `ErrorCode::INVALID_ARGUMENT`. If it is called with a key whose OS version is greater than the current system value, and the system value is non-zero, it returns `ErrorCode::INVALID_ARGUMENT`. OS version upgrades from non-zero to zero are allowed. In the event of errors communicating with the secure world, it returns an appropriate error value (for example, `ErrorCode::SECURE_HW_ACCESS_DENIED`, `ErrorCode::SECURE_HW_BUSY`). Otherwise, it returns `ErrorCode::OK` and returns a new key blob in `upgradedKeyBlob`.

`keyBlobToUpgrade` remains valid after the `upgradeKey` call, and could theoretically be used again if the device were downgraded. In practice, keystore generally calls `deleteKey` on the `keyBlobToUpgrade` blob shortly after the call to `upgradeKey`. If `keyBlobToUpgrade` had tag `Tag::ROLLBACK_RESISTANT`, then `upgradedKeyBlob` should have it as well (and should be rollback resistant).

## Secure configuration

**Note:** Keymaster 3 removed the Keymaster 2 method `configure`. The information previously provided to Keymaster HALs through `configure` is available in system properties files, and manufacturer implementations read those files during startup.

To implement version binding, the Keymaster TA needs a way to securely receive the current OS version and patch level (version information), and to ensure that the information it receives strongly matches the information about the running system.

To support secure delivery of version information to the TA, an [`OS_VERSION` field](https://android.googlesource.com/platform/system/core/+/android-4.4_r1/mkbootimg/bootimg.h#48) has been added to the boot image header. The boot image build script automatically populates this field. OEMs and Keymaster TA implementers need to work together to modify device bootloaders to extract the version information from the boot image and pass it to the TA before the non-secure system is booted. This ensures that attackers cannot interfere with provisioning of version information to the TA.

It is also necessary to ensure that the system image has the same version information as the boot image. To that end, the configure method has been added to the Keymaster HAL:

keymaster\_error\_t (\*configure)(const struct keymaster2\_device\* dev,
  const keymaster\_key\_param\_set\_t\* params);

The `params` argument contains `Tag::OS_VERSION` and `Tag::OS_PATCHLEVEL`. This method is called by keymaster2 clients after opening the HAL, but before calling any other methods. If any other method is called before configure, the TA returns `ErrorCode::KEYMASTER_NOT_CONFIGURED`.

The first time `configure` is called after the device boots, it should verify that the version information provided matches what was provided by the bootloader. If the version information doesn't match, `configure` returns `ErrorCode::INVALID_ARGUMENT`, and all other Keymaster methods continue returning `ErrorCode::KEYMASTER_NOT_CONFIGURED`. If the information matches, `configure` returns `ErrorCode::OK`, and other Keymaster methods begin functioning normally.

Subsequent calls to `configure` return the same value returned by the first call, and don't change the state of Keymaster.

**Note:** This process requires that all OTAs update both system and boot images; they can't be updated separately to keep the version information in sync.

Because `configure` is called by the system whose contents it is intended to validate, there is a narrow window of opportunity for an attacker to compromise the system image and force it to provide version information that matches the boot image, but which isn't the actual version of the system. The combination of boot image verification, dm-verity validation of the system image contents, and the fact that `configure` is called very early in the system boot should make this window of opportunity difficult to exploit.

## Authorization tags

The KeyMint (previously Keymaster) API makes extensive use of _authorization tags_, which are name-value pairs. Each possible tag has:

-   An enum name with associated value
-   An associated type (for example, integer, bytes, date, enum), which includes an indication of whether multiple values are allowed

For example, the tag with name [`Tag::BLOCK_MODE`](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/interfaces/security/keymint/aidl/android/hardware/security/keymint/Tag.aidl?q=BLOCK_MODE) has a base enum value of `4` and a `TagType::ENUM_REP` type marker that indicates that the associated value is a repeatable enum (in this case, `BlockMode`).

Tags perform a dual function on the API:

-   As parameters for an operation performed on the API, for example, the `Tag::MAC_LENGTH` on an HMAC signing operation indicates the requested HMAC length.
-   As _key characteristics_, values that are permanently bound to a particular key (that is, included in the key blob), for example, the `Tag::EC_CURVE` indicates which elliptic curve a key is for. Each key characteristic is associated with a security level that indicates which part of the system polices the attribute:
    -   A key characteristic with security level `TRUSTED_ENVIRONMENT` or `STRONGBOX` is enforced in the secure hardware.
    -   A key characteristic with security level `SOFTWARE` or `KEYSTORE` is enforced only by the `keystore2` system service (and so such a characteristic isn't resilient to OS compromise).

Many tags act as both key characteristics _and_ parameters:

-   The key characteristics indicate the set of allowed parameters for a key, for example:
    -   The `Tag::PURPOSE` of an ECDSA key might include both `SIGN` and `AGREE_KEY`.
    -   The `Tag::BLOCK_MODE` for an AES key might include ECB, CBC, and CTR modes.
-   A `begin()` request then includes a specific parameter value for the operation, for example:
    -   `begin()` has an explicit purpose parameter that must match one of the key characteristics' `Tag::PURPOSE` values.
    -   `begin()` for an AES operation needs to include a single value for `Tag::BLOCK_MODE` in the `params` field, which must match one of the values in the key characteristics.

This dual function is particularly relevant for the collection of tags passed as `keyParams` on a key generation or import operation.

-   Some of the tags act as parameters for the key generation operation itself. For example, the `Tag::CERTIFICATE_SUBJECT` tag affects only the (asymmetric) key generation process, by controlling a field in the returned X.509 certificate.
-   Other tags are bound to the newly generated key as key characteristics, and are encapsulated in the returned keyblob so that they're permanently associated with the key.

Detailed information about tag values can be found in the following HAL interface specifications:

-   KeyMint — All tags are defined in [`Tag.aidl`](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/interfaces/security/keymint/aidl/android/hardware/security/keymint/Tag.aidl) on the relevant Android release branch.
-   Keymaster — Tags are defined in `platform/hardware/interfaces/keymaster/keymaster-version/types.hal` for each respective `keymaster-version`, such as [`3.0/types.hal`](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/interfaces/keymaster/3.0/types.hal) for Keymaster 3 and [`4.0/types.hal`](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/interfaces/keymaster/4.0/types.hal) for Keymaster 4. For Keymaster 2 and below, tags are defined in [`platform/hardware/libhardware/include/hardware/keymaster_defs.h`](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/libhardware/include_all/hardware/keymaster_defs.h).

## KeyMint functions

This page provides additional details and guidelines to assist implementers of the KeyMint hardware abstraction layer (HAL). The primary documentation for the HAL is the [AIDL interface specification](https://cs.android.com/android/platform/superproject/+/android-latest-release:hardware/interfaces/security/keymint/aidl/android/hardware/security/keymint/IKeyMintDevice.aidl).

## API misuse

Callers can create KeyMint keys with authorizations that are valid as API parameters, but that make the resulting keys insecure or unusable. KeyMint implementations aren't required to fail in such cases or issue a diagnostic. Use of too-small keys, specification of irrelevant input parameters, reuse of IVs or nonces, generation of keys with no purposes (hence useless), and the like shouldn't be diagnosed by implementations.

**Caution:** KeyMint implementations should attempt to diagnose serious errors, such as omission of required parameters, specification of invalid required parameters, and similar errors that compromise the integrity of the KeyMint implementation.

It's the responsibility of apps, the framework, and Android Keystore to ensure that the calls to KeyMint modules are sensible and useful.

## addRngEntropy entry point

The `addRngEntropy` entry point adds caller-provided entropy to the pool used by the KeyMint implementation for generating random numbers, for keys and IVs.

KeyMint implementations need to securely mix the provided entropy into their pool, which also must contain internally generated entropy from a hardware random number generator. Mixing should be handled so that an attacker who has complete control of either the `addRngEntropy`\-provided bits or the hardware-generated bits (but not both) doesn't have a significant advantage in predicting the bits generated from the entropy pool.

## Key characteristics

Each of the mechanisms (`generateKey`, `importKey`, and `importWrappedKey`) that create KeyMint keys returns the newly created key's characteristics, divided appropriately into the security levels that enforce each characteristic. The returned characteristics include all of the parameters specified for key creation, except `Tag::APPLICATION_ID` and `Tag::APPLICATION_DATA`. If these tags are included in the key parameters, they're removed from the returned characteristics so that it isn't possible to find their values by examining the returned keyblob. However, they're cryptographically bound to the keyblob, so that if the correct values aren't provided when the key is used, usage fails. Similarly, `Tag::ROOT_OF_TRUST` is cryptographically bound to the key, but it can't be specified during key creation or import and is never returned.

In addition to the provided tags, the KeyMint implementation also adds `Tag::ORIGIN`, indicating the manner in which the key was created (`KeyOrigin::GENERATED`, `KeyOrigin::IMPORTED`, or `KeyOrigin::SECURELY_IMPORTED`).

## Rollback resistance

Rollback resistance is indicated by `Tag::ROLLBACK_RESISTANCE`, and means that once a key is deleted with `deleteKey` or `deleteAllKeys`, the secure hardware ensures it's never usable again.

KeyMint implementations return generated or imported key material to the caller as a keyblob, an encrypted and authenticated form. When Keystore deletes the keyblob, the key is gone, but an attacker who has previously managed to retrieve the key material could potentially restore it to the device.

A key is rollback resistant if the secure hardware ensures that deleted keys can't be restored later. This is generally done by storing additional key metadata in a trusted location that can't be manipulated by an attacker. On mobile devices, the mechanism used for this is usually replay protected memory blocks (RPMB). Because the number of keys that can be created is essentially unbounded and the trusted storage used for rollback resistance might be limited in size, the implementation can fail requests to create rollback-resistant keys when the storage is full.

## begin

The `begin()` entry point begins a cryptographic operation using the specified key, for the specified purpose, with the specified parameters (as appropriate). It returns a new `IKeyMintOperation` Binder object that is used to complete the operation. In addition, a challenge value is returned that is used as part of the authentication token in authenticated operations.

A KeyMint implementation supports at least 16 concurrent operations. Keystore uses up to 15, leaving one for `vold` to use for password encryption. When Keystore has 15 operations in progress (`begin()` has been called, but `finish` or `abort` haven't been called) and it receives a request to begin a 16th, it calls `abort()` on the least-recently used operation to reduce the number of active operations to 14 before calling `begin()` to start the newly requested operation.

If `Tag::APPLICATION_ID` or `Tag::APPLICATION_DATA` were specified during key generation or import, calls to `begin()` must include those tags with the originally specified values in the `params` argument to this method.

## Error handling

If a method on a `IKeyMintOperation` returns an error code other than `ErrorCode::OK`, the operation is aborted and the operation Binder object is invalidated. Any future use of the object returns `ErrorCode::INVALID_OPERATION_HANDLE`.

## Authorization enforcement

Key authorization enforcement is performed primarily in `begin()`. The one exception is the case where the key has one or more `Tag::USER_SECURE_ID` values, and doesn't have a `Tag::AUTH_TIMEOUT` value.

In this case, the key requires an authorization per operation, and the `update()` or `finish()` methods receive an auth token in the `authToken` argument. To ensure that the token is valid, the KeyMint implementation:

-   Verifies the HMAC signature on the auth token.
-   Checks that the token contains a secure user ID matching one associated with the key.
-   Checks that the token's auth type matches the key's `Tag::USER_AUTH_TYPE`.
-   Checks that the token contains the challenge value for the current operation in the challenge field.

If these conditions aren't met, KeyMint returns `ErrorCode::KEY_USER_NOT_AUTHENTICATED`.

The caller provides the authentication token to every call to `update()` and `finish()`. The implementation can validate the token only once.
