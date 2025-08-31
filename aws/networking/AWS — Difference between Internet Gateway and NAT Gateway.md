# AWS — Difference between Internet Gateway and NAT Gateway


# AWS — Internet Gateway vs NAT Gateway

| Feature | 🌐 Internet Gateway (IGW) | 🔒 NAT Gateway (NGW) |
|---------|---------------------------|---------------------|
| **Purpose** | Allows instances **with public IPs** to access the internet | Allows instances **without public IPs** (private subnet) to access the internet |
| **Directionality** | Bi-directional: Internet ↔ VPC | Uni-directional: VPC → Internet |
| **IP Support** | IPv4 & IPv6 | IPv4 & IPv6 |
| **Protocols Supported** | All internet protocols | TCP, UDP, ICMP |
| **Subnet Type** | Public subnet | Private subnet |
| **Availability Zone Specific?** | ❌ No, global to VPC | ✅ Yes, per AZ |
| **Redundancy** | Horizontally scaled & redundant | Redundant only **within the AZ** |
| **Elastic IP Required?** | ❌ No | ✅ Yes (for public NAT gateway) |
| **Charges** | Free | Charged per hour + data processed |
| **Source IP Behavior** | Does **not** change instance IP | Replaces instance source IP with NAT gateway IP |
| **Use Case** | Direct internet access for public instances | Private instances access internet for updates, API calls, etc. |





Yes, a **NAT Gateway does require an Internet Gateway** to function, but indirectly. Here’s the breakdown:

* A **NAT Gateway** is placed in a **public subnet** so that it can communicate with the internet.
* The **public subnet** must have a route to an **Internet Gateway (IGW)** for internet-bound traffic.
* Therefore, the NAT Gateway itself does **not replace the IGW** — it depends on the IGW to actually send traffic out to the internet.

**In short:**

* **IGW** → connects a VPC (or public subnet) to the internet.
* **NAT Gateway** → allows private subnet instances to access the internet **through the IGW** in a public subnet.

💡 **Analogy:** The IGW is the “door to the internet,” and the NAT Gateway is the “messenger” that private instances use to go through that door.

# Multi-AZ VPC Internet Access Architecture

          Internet
             ⬆️
             |
        🌐 Internet Gateway (IGW)
         /                 \
        /                   \
  Public Subnet-A       Public Subnet-B
      🔹 NAT-A              🔹 NAT-B
       ⬆️                     ⬆️
       |                     |
Private Subnet-A        Private Subnet-B
  🏠 EC2-1               🏠 EC2-2
  🏠 EC2-3               🏠 EC2-4

Legend:
- 🌐 IGW = Internet Gateway
- 🔹 NAT = NAT Gateway (one per AZ for HA)
- 🏠 EC2 = Private EC2 Instances



## TL;DR
- **Internet Gateway (IGW)** allows instances with public IPs to access the internet.  
- **NAT Gateway (NGW)** allows instances with no public IPs to access the internet.  

---

## Internet Gateway

- Internet Gateway (IGW) is a horizontally scaled, redundant, and highly available VPC component that allows communication between your VPC and the internet.  
- Internet Gateway enables resources (like EC2 instances) in public subnets to connect to the internet. Similarly, resources on the internet can initiate a connection to resources in your subnet using the public IP.  
- If a VPC does not have an Internet Gateway, then the resources in the VPC cannot be accessed from the Internet (unless the traffic flows via a Corporate Network and VPN/Direct Connect).  
- Internet Gateway supports IPv4 and IPv6 traffic.  
- Internet Gateway does not cause availability risks or bandwidth constraints on your network traffic.  
- In order to make a subnet public, add a route to your subnet’s route table that directs internet-bound traffic to the internet gateway.  
- You can associate exactly **one Internet Gateway** with a VPC.  
- Internet Gateway is **not Availability Zone specific**.  
- There’s **no additional charge** for having an internet gateway in your account.  

---

## NAT Gateway

- NAT Gateway (NGW) is a managed Network Address Translation (NAT) service.  
- NAT Gateway does something similar to Internet Gateway (IGW), but it only works **one way**: Instances in a private subnet can connect to services outside your VPC, but external services **cannot** initiate a connection with those instances.  
- NAT gateways are supported for IPv4 or IPv6 traffic.  
- NAT gateway supports the following protocols: **TCP, UDP, and ICMP**.  
- Each NAT gateway is created in a specific **Availability Zone** and implemented with redundancy in that zone.  
- If you have resources in multiple Availability Zones and they share one NAT gateway, and if the NAT gateway’s Availability Zone is down, resources in the other Availability Zones lose internet access.  
- To create an Availability Zone-independent architecture, create a NAT gateway in each Availability Zone.  
- You can associate exactly **one Elastic IP address** with a public NAT gateway.  
- You are charged for **each hour** that your NAT gateway is available and **each Gigabyte of data** that it processes.  
- NAT gateway replaces the **source IP address** of the instances with the IP address of the NAT gateway.
