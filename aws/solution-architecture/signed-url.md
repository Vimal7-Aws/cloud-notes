A **signed URL** is a secure way to **grant temporary, restricted access** to a private resource (like a file, API, or video) without exposing it to the public.

---

## 🔐 What Is a Signed URL?

A **signed URL** includes:

* The **resource URL** (e.g., an image or API endpoint)
* An **expiration time** (after which the link stops working)
* A **signature** (cryptographic hash) that proves the request is authorized
* Optional: IP restrictions, access policies, or headers

**Only users with the signed URL can access the protected resource**, and only **for a limited time**.

---

## 📦 Common Use Cases

| Use Case                  | Example                                                   |
| ------------------------- | --------------------------------------------------------- |
| ⏳ Expiring download link  | Send a 15-min link to a PDF                               |
| 📹 Secure video streaming | Allow 1-hour access to a video                            |
| 📂 Private file hosting   | Serve private files via CloudFront                        |
| 🔐 API access control     | Let client call an endpoint only once or for a short time |

---

## 🔁 How It Works (Example with Amazon CloudFront)

### ✅ Scenario

You want to serve `https://cdn.example.com/private/video.mp4` to a user for 10 minutes.

1. **CloudFront distribution** is configured to:

   * Require **signed URLs** for protected paths like `/private/*`

2. You generate a signed URL like:

```
https://cdn.example.com/private/video.mp4
?Expires=1722458400
&Signature=abcdef123456...
&Key-Pair-Id=APKAIEXAMPLE
```

3. CloudFront:

   * Validates that the **signature is correct**, **URL hasn’t expired**, and (optionally) that request is from allowed IP.
   * If valid → forwards to origin
   * If not → returns `403 Forbidden`

---

## 🔧 What Makes a Signed URL “Signed”?

You use a **private key** to sign the URL, and CloudFront (or your origin app) uses the **corresponding public key** to verify it.

### In AWS:

* Use a **CloudFront key pair**:

  * **Private key**: used by your backend/server to sign URLs
  * **Public key**: configured in CloudFront to validate signatures

---

## ✍️ Example (Signed URL Structure)

```text
https://cdn.example.com/private/image.png
?Expires=1722458400
&Signature=EyJhbGciOiJIUzI1NiIsIn...
&Key-Pair-Id=K2YOEXAMPLEKEY
```

* `Expires`: UNIX timestamp when link becomes invalid
* `Signature`: HMAC or RSA hash of the URL + expiry
* `Key-Pair-Id`: identifies the public key used by CloudFront

---

## 🧠 Security Benefits

| Feature                  | Benefit                                   |
| ------------------------ | ----------------------------------------- |
| Time-bound               | Prevents reuse of URL forever             |
| IP restriction           | Prevents sharing across networks          |
| Signature                | Tamper-proof (users can’t modify the URL) |
| No authentication needed | You don’t need to expose IAM or passwords |

---

## 🔄 Comparison: Signed URL vs. Signed Cookie

| Feature   | Signed URL     | Signed Cookie                                   |
| --------- | -------------- | ----------------------------------------------- |
| Scoped to | 1 file         | Multiple files (e.g., all under `/protected/*`) |
| Sent via  | URL parameter  | HTTP cookie                                     |
| Use case  | One-off access | Logged-in session access                        |

---

## 🔧 AWS: Services That Use Signed URLs

| Service                           | Description                                 |
| --------------------------------- | ------------------------------------------- |
| **CloudFront**                    | Protects CDN content (videos, files)        |
| **Amazon S3**                     | Pre-signed URLs to download/upload objects  |
| **API Gateway** (via Lambda auth) | Can implement token-based signed URL access |
| **MediaStore / MediaPackage**     | Secure video delivery                       |

---

## ✅ TL;DR: Signed URL

| Key Idea | Description                                         |
| -------- | --------------------------------------------------- |
| What?    | URL with a time-limited, cryptographic signature    |
| Why?     | Secure temporary access without exposing backend    |
| Where?   | CloudFront, S3, API Gateway, custom services        |
| How?     | Generate with private key, validate with public key |

---

Would you like a real code example (e.g., signed URL generation in Python or Node.js)? Or how to do it for CloudFront + S3?
