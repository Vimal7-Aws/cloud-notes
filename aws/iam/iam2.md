Great question!

### ✅ **Why is IAM (Identity and Access Management) Global in AWS?**

**AWS IAM is global** because it is designed to manage **access at the account level**, not at the regional level. Here's why:

---

### 🔑 1. **Centralized Identity Management**

IAM resources—such as:

* Users
* Groups
* Roles
* Policies

...are created **once** and apply **across all AWS regions** in the account. This allows you to **centrally manage** who can do what, without duplicating user or policy definitions in each region.

---

### 🌐 2. **Consistent Access Control**

A global IAM means:

* A user with S3 read permissions can read **any S3 bucket**, in **any region**, unless you restrict them further (e.g., via resource or condition-level policies).
* Roles and permissions behave the same, regardless of the region where they're used.

This reduces complexity when deploying applications and services across multiple regions.

---

### ⚙️ 3. **Security and Policy Uniformity**

IAM being global:

* Ensures **consistent policy enforcement**
* Simplifies **auditing** and **compliance**
* Prevents misconfiguration of access in multi-region environments

---

### 📌 What’s Regional Instead?

* **STS tokens (e.g., AssumeRoleWithWebIdentity)**: These are regional.
* **Permissions boundaries** apply globally but are evaluated in regional contexts.

---

### Summary

| Feature                  | Global / Regional |
| ------------------------ | ----------------- |
| IAM Users/Roles/Policies | 🌐 Global         |
| IAM Service Itself       | 🌐 Global         |
| STS Token Endpoints      | ✅ Regional        |

---

Let me know if you’d like a diagram or want to see IAM behavior in a real example (e.g., cross-region Lambda execution with IAM roles).
