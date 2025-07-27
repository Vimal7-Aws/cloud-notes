Great! Let's walk through how to **secure Amazon S3 content behind CloudFront using signed URLs** — step by step.

---

## 🎯 Goal

You want to:

* Serve **private content from S3** (e.g., `/documents/report.pdf`)
* Use **CloudFront** as the CDN to cache and deliver it
* **Restrict access** using **signed URLs** (temporary, secure)

---

## ✅ Overview of Steps

1. 🔒 Make your S3 bucket **private**
2. 🌐 Create a **CloudFront distribution** with the S3 bucket as the origin
3. 🔑 Create a CloudFront **key pair** (or use **trusted key group**)
4. 🧾 Configure CloudFront to require **signed URLs** for protected paths
5. 🧮 Generate **signed URLs** in your app (e.g., Python, Node.js)

---

## 🧰 Step-by-Step Setup

### 1. 🔒 Make S3 Bucket Private

Ensure the S3 bucket is **not publicly accessible**:

* Block all public access in the S3 bucket settings
* Remove any public policies
* Disable static website hosting

---

### 2. 🌐 Create CloudFront Distribution

* Origin type: **S3 Bucket (not public)**
* Restrict Bucket Access: ✅ Yes

  * Choose: **"Yes, use OAI (Origin Access Identity)"** or OAC (Origin Access Control)
  * This allows only CloudFront to access the bucket
* Cache Behavior:

  * Path Pattern: `/private/*` (or `/` for all paths)
  * Restrict Viewer Access (Signed URLs or Cookies): ✅ Yes

---

### 3. 🔑 Create Key Pair (Legacy Method) or Key Group (Recommended)

#### Option A: **Trusted Key Group (Recommended)**

1. In **CloudFront Console**, go to **Key Management**
2. Create a **public key** (paste your RSA public key)
3. Create a **key group** and add the public key
4. In CloudFront behavior, **restrict access to that key group**

> You’ll use the **corresponding private key** in your backend to generate signed URLs.

#### Option B: **Legacy Key Pair**

* Create an **Access Key Pair for CloudFront signed URLs** in the [Security Credentials](https://console.aws.amazon.com/iam/home?#security_credential)
* Use the private key for signing URLs
* Use `Key-Pair-Id` in your signed URLs

---

### 4. 🧾 Generate a Signed URL

You need:

* CloudFront domain (e.g., `d123abc.cloudfront.net`)
* Path to your object (e.g., `/private/report.pdf`)
* Key pair ID or Key Group ID
* Private key (PEM format)

---

### 📜 Example: Signed URL in **Python**

```python
import datetime
from urllib.parse import quote_plus
from cryptography.hazmat.primitives import serialization, hashes
from cryptography.hazmat.primitives.asymmetric import padding
import base64

# Inputs
url = "https://d123abc.cloudfront.net/private/report.pdf"
key_pair_id = "K2XXXXXXXXXXXXXXXXXX"  # from CloudFront
private_key_file = "private_key.pem"
expires = int((datetime.datetime.utcnow() + datetime.timedelta(minutes=15)).timestamp())

# Build the string to sign
policy = f"{url}\n{expires}"

# Load private key
with open(private_key_file, 'rb') as f:
    private_key = serialization.load_pem_private_key(f.read(), password=None)

# Sign the policy
signature = private_key.sign(
    policy.encode(),
    padding.PKCS1v15(),
    hashes.SHA1()
)
encoded_sig = base64.b64encode(signature).decode('utf-8')
encoded_sig = quote_plus(encoded_sig)

# Final signed URL
signed_url = f"{url}?Expires={expires}&Signature={encoded_sig}&Key-Pair-Id={key_pair_id}"
print(signed_url)
```

---

### 🧪 Testing

* Paste the **signed URL** in your browser
* It should download or show the file
* After 15 minutes, it returns **403 Forbidden**

---

## 🧠 Best Practices

| Best Practice                             | Why?                                           |
| ----------------------------------------- | ---------------------------------------------- |
| Use CloudFront, not direct S3 signed URLs | Better performance, security, geo-distribution |
| Use **trusted key groups**                | Preferred over legacy key pairs                |
| Rotate keys regularly                     | Better security hygiene                        |
| Short expiration time                     | Reduces misuse if URL is leaked                |
| Monitor access logs                       | Detect abuse or scraping                       |

---

## 🗺️ Summary

| Component            | Role                           |
| -------------------- | ------------------------------ |
| S3                   | Stores private files           |
| CloudFront           | Secures + accelerates delivery |
| Key Group / Key Pair | Enables URL signing            |
| Signed URL           | Grants temporary access        |
| App                  | Generates signed URLs securely |

---

Would you like:

* A Node.js or Go version of the signed URL generator?
* Terraform or CloudFormation code for setting this all up?

Let me know!
