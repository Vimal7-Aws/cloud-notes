

![image](https://github.com/user-attachments/assets/cf1c85a6-b4fd-4664-9e67-b1a5079c79c6)







![image](https://github.com/user-attachments/assets/0946fd62-9446-4979-99a1-ebb8e58aa152)







![image](https://github.com/user-attachments/assets/cf52a062-e379-43be-83ec-52435c38c87b)






* Interface endpoint uses ENI
* Gateway endpoint uses Routetables



Sure! Let's dive deep into **AWS VPC Endpoints**—a key concept for securely connecting your Virtual Private Cloud (VPC) to AWS services **without traversing the public internet**.

---

## 🔹 What is a VPC Endpoint?

An **AWS VPC Endpoint** allows you to privately connect your VPC to supported AWS services and VPC endpoint services **powered by PrivateLink**, without requiring:

* Internet Gateway
* NAT device
* VPN connection
* AWS Direct Connect

The traffic between your VPC and the service **stays within the Amazon network**.

---

## 🔸 Types of VPC Endpoints

There are **two main types** of VPC endpoints:

### 1. **Interface Endpoint**

* **Most common type**
* It creates an **Elastic Network Interface (ENI)** in your subnet with a **private IP**.
* Supports services like **S3**, **DynamoDB**, **SNS**, **SQS**, **ECR**, etc.
* Uses **AWS PrivateLink** under the hood.

**Example:**
You access S3 using `https://bucket.s3.us-east-1.amazonaws.com` via a private IP in your subnet.

🔧 **Key Points**:

* You must manage **DNS resolution** for the endpoint (AWS can do this automatically).
* You can use **Security Groups** to control access.
* Billing includes **hourly** and **data processing** charges.

---

### 2. **Gateway Endpoint**

* **Only supports two services**: **Amazon S3** and **DynamoDB**.
* Adds a **route** to your route table.
* **No cost to use**.
* No ENI is created.

🔧 **Key Points**:

* Must be attached to a specific route table.
* Very efficient and cost-effective for S3/DynamoDB access.

---

## 🔸 When to Use Which?

| Feature                    | Interface Endpoint         | Gateway Endpoint     |
| -------------------------- | -------------------------- | -------------------- |
| Supports most AWS services | ✅ Yes                      | ❌ Only S3 & DynamoDB |
| PrivateLink-based          | ✅ Yes                      | ❌ No                 |
| Cost                       | 💲 Extra charges for usage | 🆓 Free              |
| Security group control     | ✅ Yes (via ENI)            | ❌ No                 |
| Route table required       | ❌ No                       | ✅ Yes                |

---

## 🔹 Use Cases

* **Access AWS services securely** from within your VPC.
* **Connect to 3rd-party SaaS services** using PrivateLink.
* Improve **security posture** by removing need for NAT Gateways/public IPs.
* Ensure **data sovereignty** or compliance with **security policies**.

---

## 🔹 Key Considerations

* **Interface endpoints** use ENIs—so they **consume IPs** in your subnet.
* You should **enable Private DNS** if you want to use standard service URLs (e.g., `s3.amazonaws.com`).
* With **endpoint policies**, you can restrict access to specific resources (like S3 buckets).

---

Great! Here's how to **create VPC endpoints** using **Terraform**, **AWS CLI**, and **CloudFormation**—for both **Gateway** (S3) and **Interface** (e.g., SSM) endpoints.

---

## ✅ 1. **Terraform Examples**

### 🔹 Gateway Endpoint for S3

```hcl
resource "aws_vpc_endpoint" "s3" {
  vpc_id       = "vpc-abc123"
  service_name = "com.amazonaws.us-east-1.s3"
  vpc_endpoint_type = "Gateway"
  route_table_ids = ["rtb-abc123"]
}
```

### 🔹 Interface Endpoint for SSM

```hcl
resource "aws_vpc_endpoint" "ssm" {
  vpc_id            = "vpc-abc123"
  service_name      = "com.amazonaws.us-east-1.ssm"
  vpc_endpoint_type = "Interface"
  subnet_ids        = ["subnet-abc123"]
  security_group_ids = ["sg-abc123"]
  private_dns_enabled = true
}
```

Great! Let’s now look at how to **restrict access using endpoint policies** and how to **tie VPC endpoints to specific S3 buckets using bucket policies** for security and control.

---

## 🔹 1. VPC **Endpoint Policy** (IAM-style JSON)

VPC Endpoint policies let you control **which AWS principals can access which resources** through the endpoint.

### ✅ Example: Restrict Gateway Endpoint to a Specific S3 Bucket

```json
{
  "Statement": [
    {
      "Principal": "*",
      "Action": "s3:*",
      "Effect": "Allow",
      "Resource": [
        "arn:aws:s3:::my-secure-bucket",
        "arn:aws:s3:::my-secure-bucket/*"
      ]
    }
  ]
}
```

Apply this in Terraform, CLI, or CloudFormation under the `policy` or `PolicyDocument` field for the endpoint.

---

## 🔹 2. S3 **Bucket Policy** for VPC Endpoint Restriction

Even if you restrict via endpoint policy, **S3 bucket policies** are the best way to **enforce access from specific endpoints**.

### ✅ Example: Allow only VPC Endpoint `vpce-abc123` to access the bucket

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::my-secure-bucket",
        "arn:aws:s3:::my-secure-bucket/*"
      ],
      "Condition": {
        "StringNotEquals": {
          "aws:SourceVpce": "vpce-abc123"
        }
      }
    }
  ]
}
```

🔐 This **denies access** to anyone not using the specified VPC endpoint—even if they have valid credentials.

---

## 🔹 3. Use Both Together

* **Endpoint policy** → Controls what services/resources can be accessed *via* the endpoint.
* **Bucket policy** → Ensures that access *must go through* the endpoint.

👉 **Best practice** is to use both.

---

Perfect! Here's a **full working Terraform example** that:

1. Creates a **VPC** and **subnets**
2. Creates a **Gateway VPC Endpoint** for S3
3. Attaches a **custom endpoint policy**
4. Creates an **S3 bucket**
5. Applies a **bucket policy** that only allows access via the VPC endpoint

---

## ✅ Full Terraform Example: VPC Gateway Endpoint with Secure S3 Access

```hcl
provider "aws" {
  region = "us-east-1"
}

# VPC and Subnet
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "main" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
}

resource "aws_route_table" "main" {
  vpc_id = aws_vpc.main.id
}

resource "aws_route_table_association" "a" {
  subnet_id      = aws_subnet.main.id
  route_table_id = aws_route_table.main.id
}

# Gateway VPC Endpoint for S3
resource "aws_vpc_endpoint" "s3_gateway" {
  vpc_id            = aws_vpc.main.id
  service_name      = "com.amazonaws.us-east-1.s3"
  vpc_endpoint_type = "Gateway"
  route_table_ids   = [aws_route_table.main.id]

  policy = jsonencode({
    Statement = [{
      Effect = "Allow"
      Principal = "*"
      Action = "s3:*"
      Resource = [
        "arn:aws:s3:::secure-bucket-123456",
        "arn:aws:s3:::secure-bucket-123456/*"
      ]
    }]
  })
}

# S3 Bucket
resource "aws_s3_bucket" "secure_bucket" {
  bucket = "secure-bucket-123456"
  force_destroy = true
}

# S3 Bucket Policy
resource "aws_s3_bucket_policy" "secure_policy" {
  bucket = aws_s3_bucket.secure_bucket.id

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid       = "DenyNonVPCE"
        Effect    = "Deny"
        Principal = "*"
        Action    = "s3:*"
        Resource = [
          "arn:aws:s3:::secure-bucket-123456",
          "arn:aws:s3:::secure-bucket-123456/*"
        ]
        Condition = {
          StringNotEquals = {
            "aws:SourceVpce" = aws_vpc_endpoint.s3_gateway.id
          }
        }
      }
    ]
  })
}
```

---

### 🧪 How to Test It

After deployment:

* Try accessing the bucket from an **EC2 inside the VPC** → ✅ Allowed
* Try accessing from a public IP or non-VPCE route → ❌ Denied

---


