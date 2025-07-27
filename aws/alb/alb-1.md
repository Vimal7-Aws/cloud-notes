An **AWS ALB (Application Load Balancer)** is a Layer 7 load balancer provided by **Amazon Elastic Load Balancing (ELB)**. It is designed to handle advanced routing and traffic distribution for modern application architectures, especially **microservices** and **container-based** workloads (e.g., running in ECS, EKS, or Kubernetes).

---

## 🔹 What is AWS ALB?

An **Application Load Balancer (ALB)** routes HTTP/HTTPS traffic based on content — such as URL path, host, headers, query strings, etc. It is ideal for web applications and services that need **intelligent request routing**.

It is one of the 3 main types of ELBs:

* **ALB** – Application Load Balancer (Layer 7)
* **NLB** – Network Load Balancer (Layer 4, TCP/UDP)
* **CLB** – Classic Load Balancer (Legacy, both Layer 4 & 7)

---

## 🔹 Key Features of ALB

### 1. **Content-Based Routing**

* Route traffic based on:

  * URL paths (e.g., `/api/*` vs `/admin/*`)
  * Host headers (e.g., `api.example.com`)
  * HTTP headers or query parameters
* Enables **path-based** and **host-based** routing.

### 2. **Support for Microservices & Containers**

* Works seamlessly with **Amazon ECS, EKS**, or Kubernetes.
* Supports **dynamic port mapping**, useful when multiple containers on the same host expose different ports.

### 3. **Target Groups**

* ALB routes traffic to **target groups**.
* Targets can be:

  * EC2 instances
  * IP addresses
  * Lambda functions
* Health checks are done at the **target group** level.

### 4. **Native HTTP/2 and WebSocket Support**

* Improves performance and allows persistent, low-latency connections.

### 5. **SSL Termination (HTTPS Support)**

* Terminate SSL at the load balancer and forward unencrypted traffic to backends.
* Integration with **AWS Certificate Manager (ACM)** for free SSL/TLS certificates.

### 6. **Authentication & Authorization**

* Supports **OIDC** and **Cognito-based user authentication** directly at the ALB layer.
* Enforce user login before allowing access to services.

### 7. **WAF Integration**

* AWS Web Application Firewall (WAF) can be integrated directly with ALB for DDoS protection and rule-based filtering.

### 8. **Access Logs**

* Stores detailed access logs in S3 for security and monitoring.

### 9. **Sticky Sessions (Session Affinity)**

* ALB supports session stickiness using **load balancer-generated cookies**.

### 10. **Redirects & Fixed Responses**

* Redirect HTTP to HTTPS.
* Return static responses (e.g., 403, 404) directly from the ALB.

---

## 🔹 Common Use Cases

* Hosting multiple microservices behind a single domain.
* Handling HTTP-based traffic for web and mobile applications.
* Enforcing HTTPS using redirect rules.
* Integrating OAuth or SAML-based auth at the edge (via Cognito).
* Load balancing traffic to containers using ECS or Kubernetes.

---

Let me know if you want a visual diagram or an example configuration (like ALB ingress for EKS)!
