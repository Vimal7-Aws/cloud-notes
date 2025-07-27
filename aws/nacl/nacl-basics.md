An **NACL** (Network Access Control List) in AWS is a **stateless firewall** for controlling **inbound and outbound traffic at the subnet level** in a VPC. It's an extra layer of security that complements **security groups** (which operate at the instance level).

---

## 🔹 Key Concepts of NACLs

| Feature                     | Description                                                                                             |
| --------------------------- | ------------------------------------------------------------------------------------------------------- |
| **Stateless**               | Responses to allowed inbound traffic **must be explicitly allowed** in outbound rules (and vice versa). |
| **Applies at Subnet Level** | Controls traffic **entering and leaving subnets** (not individual instances).                           |
| **Rule Order**              | Rules are **evaluated in number order**, starting from the lowest.                                      |
| **Default Deny**            | If no rule matches, traffic is denied by default.                                                       |
| **Default NACL**            | Every VPC has a default NACL that allows all inbound and outbound traffic.                              |

---

## 🔸 NACL vs. Security Group

| Feature             | NACL                             | Security Group                        |
| ------------------- | -------------------------------- | ------------------------------------- |
| **Level**           | Subnet-level                     | Instance-level                        |
| **Stateful?**       | ❌ Stateless                      | ✅ Stateful                            |
| **Rules evaluated** | In order (by rule number)        | All rules evaluated together          |
| **Allow/Deny**      | Can explicitly allow or deny     | Only allows (no deny rules)           |
| **Applies to**      | All traffic in/out of the subnet | Only to the instance it's attached to |

---

## 🔹 Rule Structure

Each rule has:

* **Rule number** (lower numbers have higher priority)
* **Protocol** (e.g., TCP, UDP, ICMP, or ALL)
* **Port range** (e.g., 80 for HTTP)
* **CIDR block** (source/destination IP)
* **Allow** or **Deny**

### Example:

Allow HTTP traffic from anywhere:

```text
Rule #: 100
Type:   HTTP
Protocol: TCP
Port Range: 80
Source: 0.0.0.0/0
Allow/Deny: ALLOW
```

---

## 🔹 Default vs. Custom NACL

* The **default NACL** allows all inbound/outbound traffic.
* You can create **custom NACLs** with tighter rules and associate them with one or more subnets.

> ⚠️ A subnet **can only be associated with one NACL** at a time.

---

## 🔹 Example Use Cases

1. **Block a specific IP address** at the subnet level.
2. **Restrict subnet** to only allow specific traffic (e.g., HTTP, SSH).
3. Create **isolated DMZ zones** for public-facing applications.

---

## 🔹 Terraform Example: Custom NACL

```hcl
resource "aws_network_acl" "example" {
  vpc_id = aws_vpc.main.id
  subnet_ids = [aws_subnet.public.id]

  ingress {
    rule_no    = 100
    protocol   = "tcp"
    rule_action = "allow"
    cidr_block = "0.0.0.0/0"
    from_port  = 80
    to_port    = 80
  }

  egress {
    rule_no    = 100
    protocol   = "tcp"
    rule_action = "allow"
    cidr_block = "0.0.0.0/0"
    from_port  = 80
    to_port    = 80
  }
}
```


![image](https://github.com/user-attachments/assets/62dc1ae5-95e1-4895-b9be-5a7a56aafd13)

Here’s a clear side-by-side comparison of **NACLs** (Network ACLs) and **Security Groups** in AWS:

---

## 🔸 NACL vs. Security Group in AWS

| Feature                     | **NACL (Network ACL)**                                  | **Security Group**                                |
| --------------------------- | ------------------------------------------------------- | ------------------------------------------------- |
| **Scope/Applies to**        | Subnet-level                                            | Instance-level (ENI-level)                        |
| **Stateful?**               | ❌ No (Stateless)                                        | ✅ Yes (Stateful)                                  |
| **Traffic Direction Rules** | Separate rules for inbound and outbound                 | One set for both—responses allowed automatically  |
| **Rule Evaluation Order**   | In order (lowest rule number first)                     | All rules evaluated together (implicit deny last) |
| **Allow/Deny Rules**        | Can explicitly **allow or deny**                        | Can only **allow**                                |
| **Default Behavior**        | Default NACL allows all traffic                         | Default SG allows no inbound, all outbound        |
| **Applies to**              | All traffic to/from subnet                              | Specific EC2 instances or ENIs                    |
| **Use Case**                | Broad subnet-level filtering (e.g., deny IP range)      | Fine-grained access control per instance          |
| **Logging**                 | VPC Flow Logs (via CloudWatch)                          | No direct logs; use VPC Flow Logs at ENI level    |
| **Common Use**              | Block specific IPs, open/close ports at subnet boundary | Allow specific ports/services on instances        |

---

## 🔑 Quick Analogy:

* **NACL** = Door at the **subnet boundary** — doesn’t remember state; checks each request individually.
* **Security Group** = Door on the **individual EC2 instance** — remembers established connections (stateful).

---

## 🔧 Example Use Cases:

* Use **NACLs** to block a malicious IP address at the subnet level.
* Use **Security Groups** to allow only port 22 (SSH) or 443 (HTTPS) access to specific EC2 instances.

---

Would you like a PDF or cheat sheet version of this for interview prep?



---

