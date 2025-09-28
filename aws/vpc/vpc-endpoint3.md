A **VPC Endpoint** is an **entry point inside your VPC** that lets your resources (EC2, Lambda, ECS, etc.) connect to supported AWS services **without using a public IP** and **without traffic going over the public Internet**.
Think of it as a “private door” from your VPC directly into AWS services or another VPC/service.

---

### 🔹 Why VPC Endpoints exist

Normally, if an EC2 instance in a VPC wants to call S3 or DynamoDB:

* It leaves the VPC.
* It travels over the Internet or through a NAT Gateway / Internet Gateway.
* It uses public IP addresses.

With a **VPC Endpoint**, traffic stays **inside the AWS network**:

* No public IP needed.
* More secure.
* Often cheaper (no NAT Gateway data processing).

---

### 🔹 Types of VPC Endpoints

AWS offers **two main types** (and one less-used):

| Type                                              | Use Case                                                           | How It Works                                                                                                                                                             |
| ------------------------------------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Gateway Endpoint**                              | For **S3** and **DynamoDB** only                                   | Adds a route in your VPC route table pointing to S3/DynamoDB via the AWS backbone. No ENIs created. Cheaper.                                                             |
| **Interface Endpoint**                            | For most AWS services, PrivateLink services, and your own services | Creates an **Elastic Network Interface (ENI)** in your subnet with a private IP. Your traffic goes through that ENI directly to the service. Powered by **PrivateLink**. |
| **Gateway Load Balancer Endpoint** (GLB Endpoint) | For 3rd-party virtual appliances (firewalls, IDS)                  | Lets you insert appliances into the traffic path at scale.                                                                                                               |

---

### 🔹 How it looks visually

```
Your VPC (private subnet)
 ┌───────────────┐
 │  EC2 / Lambda │
 └───────┬───────┘
         │ Private
         ▼
   VPC Endpoint (ENI or Route)
         │
         ▼
 AWS Service (S3, DynamoDB, or other)
```

* No NAT Gateway
* No Internet Gateway
* No public IP

---

### 🔹 Benefits

* **Security**: Traffic stays within AWS network, no Internet exposure.
* **Simplified architecture**: No NAT Gateway required for supported services.
* **Cost savings**: No NAT data processing charges.
* **Scalable**: Highly available and managed by AWS.

---

Would you like me to give you a **quick table comparing Gateway vs Interface Endpoints** (with costs, IP usage, and supported services)? (It makes this super easy to remember.)
