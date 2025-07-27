Here's a full **Java example** using **AWS KMS** for **digital signing and verification** with **asymmetric keys**.

---

## ✅ Goal

* Create or use an existing **KMS asymmetric key** with `SIGN_VERIFY` usage.
* Use `KmsClient.sign()` to **sign** a message digest.
* Use `KmsClient.verify()` to **verify** the signature.

---

## 📦 Maven Dependencies

Add the AWS SDK v2:

```xml
<dependencies>
    <dependency>
        <groupId>software.amazon.awssdk</groupId>
        <artifactId>kms</artifactId>
        <version>2.25.17</version>
    </dependency>
    <dependency>
        <groupId>software.amazon.awssdk</groupId>
        <artifactId>core</artifactId>
        <version>2.25.17</version>
    </dependency>
</dependencies>
```

---

## 🔐 Java Code: Sign and Verify

```java
import software.amazon.awssdk.services.kms.KmsClient;
import software.amazon.awssdk.services.kms.model.*;
import software.amazon.awssdk.core.SdkBytes;

import java.nio.charset.StandardCharsets;
import java.security.MessageDigest;

public class KmsSignVerify {

    // Replace with your actual KMS Key ID or ARN (must be asymmetric and SIGN_VERIFY)
    private static final String KEY_ID = "arn:aws:kms:us-west-2:123456789012:key/your-key-id";

    public static void main(String[] args) throws Exception {
        KmsClient kmsClient = KmsClient.create();

        String message = "Hello, AWS KMS!";
        byte[] digest = sha256(message);

        // Sign the message digest
        SignResponse signResponse = kmsClient.sign(SignRequest.builder()
                .keyId(KEY_ID)
                .message(SdkBytes.fromByteArray(digest))
                .messageType(MessageType.DIGEST)
                .signingAlgorithm(SigningAlgorithmSpec.RSASSA_PKCS1_V1_5_SHA_256)
                .build());

        byte[] signature = signResponse.signature().asByteArray();
        System.out.println("Signature (Base64): " + java.util.Base64.getEncoder().encodeToString(signature));

        // Verify the signature
        VerifyResponse verifyResponse = kmsClient.verify(VerifyRequest.builder()
                .keyId(KEY_ID)
                .message(SdkBytes.fromByteArray(digest))
                .messageType(MessageType.DIGEST)
                .signingAlgorithm(SigningAlgorithmSpec.RSASSA_PKCS1_V1_5_SHA_256)
                .signature(SdkBytes.fromByteArray(signature))
                .build());

        System.out.println("Is the signature valid? " + verifyResponse.signatureValid());

        kmsClient.close();
    }

    private static byte[] sha256(String message) throws Exception {
        MessageDigest md = MessageDigest.getInstance("SHA-256");
        return md.digest(message.getBytes(StandardCharsets.UTF_8));
    }
}
```

---

## 🔧 Notes

* The key must be an **asymmetric KMS key** with:

  * `KeyUsage: SIGN_VERIFY`
  * `KeySpec`: e.g., `RSA_2048`, `ECC_NIST_P256`, etc.
* `messageType = DIGEST`: You must **hash the message yourself** (as shown).
* Choose the **correct signing algorithm**:

  * RSA: `RSASSA_PKCS1_V1_5_SHA_256`, `RSASSA_PSS_SHA_256`
  * ECC: `ECDSA_SHA_256`, etc.

---

Would you like a version using **ECC** keys instead of RSA, or an example of **how to download the public key and verify outside AWS**?
