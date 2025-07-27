In AWS KMS, the **KeySpec** and **KeyUsage** are crucial properties you define when creating a KMS key. They determine the type of cryptographic key material and the intended cryptographic operations for that key. You **cannot** change these properties after the KMS key is created.

### KeySpec (Key Specification)

The `KeySpec` parameter specifies the type of key material in the KMS key. It determines whether the key is symmetric or asymmetric and the specific algorithm and key size. Here's a breakdown of common `KeySpec` values:

**Symmetric Keys:**

* **`SYMMETRIC_DEFAULT`**: (Default) Represents a 256-bit AES-GCM symmetric encryption key. This is the most common type for encrypting data.

**Asymmetric Keys:**

* **RSA Key Specs (for ENCRYPT\_DECRYPT or SIGN\_VERIFY):**
    * `RSA_2048`: RSA key pair with a 2048-bit modulus.
    * `RSA_3072`: RSA key pair with a 3072-bit modulus.
    * `RSA_4096`: RSA key pair with a 4096-bit modulus.
* **ECC Key Specs (for SIGN\_VERIFY or KEY\_AGREEMENT):**
    * `ECC_NIST_P256` (secp256r1): NIST-recommended elliptic curve key pair.
    * `ECC_NIST_P384` (secp384r1): NIST-recommended elliptic curve key pair.
    * `ECC_NIST_P521` (secp521r1): NIST-recommended elliptic curve key pair.
    * `ECC_SECG_P256K1` (secp256k1): Elliptic curve key pair commonly used for cryptocurrencies (SIGN\_VERIFY only).
* **SM2 Key Spec (China Regions only):**
    * `SM2`: SM2 key pair for encryption/decryption, signing/verification, or deriving shared secrets.

**HMAC Keys (for GENERATE\_VERIFY\_MAC):**

* `HMAC_224`: 224-bit HMAC key.
* `HMAC_256`: 256-bit HMAC key.
* `HMAC_384`: 384-bit HMAC key.
* `HMAC_512`: 512-bit HMAC key.

### KeyUsage

The `KeyUsage` parameter determines the cryptographic operations that the KMS key can perform. Each KMS key can have only one `KeyUsage`.

* **`ENCRYPT_DECRYPT`**: The key can be used for both encryption and decryption operations. This is typical for symmetric keys and some asymmetric (RSA, SM2) keys.
* **`SIGN_VERIFY`**: The key can be used for generating digital signatures and verifying them. This is common for asymmetric keys (RSA, ECC, SM2) and HMAC keys.
* **`GENERATE_VERIFY_MAC`**: The key can be used for generating and verifying Hash-based Message Authentication Codes (HMACs). This is the sole `KeyUsage` for HMAC KMS keys.
* **`KEY_AGREEMENT`**: The key can be used for key agreement protocols, allowing parties to establish a shared secret. This applies to specific asymmetric key types (some ECC, SM2).

**Important Relationships:**

* The `KeySpec` you choose will limit the available options for `KeyUsage`. For example, you cannot set the `KeyUsage` to `SIGN_VERIFY` for a `SYMMETRIC_DEFAULT` key.
* For asymmetric RSA and SM2 keys, you must choose either `ENCRYPT_DECRYPT` or `SIGN_VERIFY`. They cannot do both.
* For asymmetric NIST-recommended ECC and SM2 keys, you must choose between `SIGN_VERIFY` or `KEY_AGREEMENT`.
* `ECC_SECG_P256K1` keys only support `SIGN_VERIFY`.
* HMAC keys must have a `KeyUsage` of `GENERATE_VERIFY_MAC`.

Understanding the different `KeySpec` values and their compatible `KeyUsage` is essential when creating KMS keys to ensure they meet your specific security and cryptographic requirements. Choose these properties carefully during key creation, as they cannot be changed later.
