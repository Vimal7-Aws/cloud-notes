Here’s your content converted into **Markdown** with symbols, emblems, and tables for clarity and readability:

---

# AWS WAF & Route 53 (DNS)

📌 **Why AWS WAF Does NOT Support Route 53**

AWS WAF is a **Layer 7 (Application Layer) Web Application Firewall**, which means:

* 🌐 It works with HTTP/HTTPS requests.
* 🛡️ It filters web traffic based on headers, URIs, body, methods, IPs, etc.
* 📛 DNS (Route 53) works at **Layer 3/4 (Network & Transport layers)** → only resolves domain names to IP addresses. No HTTP traffic is involved at this point.

---

## ✅ AWS WAF Supported Services (as of 2025)

| Service                         | WAF Supported? | Notes                                             |
| ------------------------------- | -------------- | ------------------------------------------------- |
| CloudFront                      | ✅ Yes          | Edge-based HTTP protection, ideal for global apps |
| Application Load Balancer (ALB) | ✅ Yes          | Regional HTTP/S protection                        |
| Amazon API Gateway              | ✅ Yes          | Protect RESTful APIs                              |
| AWS App Runner                  | ✅ Yes          | Application layer support                         |
| AWS Verified Access             | ✅ Yes          | Secure access to apps                             |
| AWS Lambda Function URLs        | ✅ Yes          | Protect direct HTTP calls to Lambda               |

---

## ❌ Not Supported by AWS WAF

| Service                            | Reason                                                                   |
| ---------------------------------- | ------------------------------------------------------------------------ |
| Route 53 (DNS)                     | Just a domain name resolution service, no HTTP layer to inspect          |
| Network Load Balancer (NLB)        | Works at Layer 4 (TCP/UDP), no HTTP-level visibility                     |
| S3 static websites (direct access) | Not supported unless fronted by CloudFront                               |
| EC2 Public IPs                     | WAF can’t attach to raw IPs, needs HTTP interface like ALB or CloudFront |

🔁 **TL;DR:**
AWS WAF cannot inspect or protect Route 53 (DNS) directly because DNS traffic is **not HTTP-based**, and WAF only operates on **application layer web traffic**.

---

# AWS WAF Application Protection

AWS WAF can be applied to:

* **Amazon CloudFront Distributions:** Protect web apps & APIs at edge locations → reduces latency.
* **Application Load Balancers (ALB):** Protect apps served via ALBs with Elastic Load Balancing.
* **Amazon API Gateway:** Secure APIs against common exploits and bots.
* **AWS App Runner:** Protect containerized web apps & APIs, fully managed service.

---

# 📊 CloudWatch Metrics for AWS Application Load Balancer (ALB)

| Metric Name                            | Description                                 |
| -------------------------------------- | ------------------------------------------- |
| ActiveConnectionCount                  | Total number of active connections          |
| ClientTLSNegotiationErrorCount         | TLS connections not established by client   |
| ConsumedLCUs                           | Load balancer capacity units used           |
| HealthyHostCount                       | Number of healthy targets in a target group |
| HTTP_Fixed_Response_Count              | Successful fixed-response actions           |
| HTTP_Redirect_Count                    | Successful redirect actions                 |
| HTTP_Redirect_Url_Limit_Exceeded_Count | Redirects failed due to URL length limit    |
| HTTPCode_ELB_3XX_Count                 | HTTP 3XX codes by ALB                       |
| HTTPCode_ELB_4XX_Count                 | HTTP 4XX codes by ALB                       |
| HTTPCode_ELB_5XX_Count                 | HTTP 5XX codes by ALB                       |
| HTTPCode_Target_2XX_Count              | HTTP 2XX codes by targets                   |
| HTTPCode_Target_3XX_Count              | HTTP 3XX codes by targets                   |
| HTTPCode_Target_4XX_Count              | HTTP 4XX codes by targets                   |
| HTTPCode_Target_5XX_Count              | HTTP 5XX codes by targets                   |
| IPv6ProcessedBytes                     | Bytes processed for IPv6 requests           |
| IPv6RequestCount                       | IPv6 requests received                      |
| NewConnectionCount                     | New connections established                 |
| ProcessedBytes                         | Total bytes processed                       |
| RejectedConnectionCount                | Connections rejected                        |
| RequestCount                           | Requests received                           |
| RuleEvaluations                        | Rules processed                             |
| TargetConnectionErrorCount             | Failed connections to targets               |
| TargetResponseTime                     | Time for target response                    |
| TargetTLSNegotiationErrorCount         | TLS connections not established with target |
| UnHealthyHostCount                     | Unhealthy targets in a target group         |

---

---

# 📊 CloudWatch Metrics for AWS WAF & DDoS (ALB)

| Metric Name           | Description                              |
| --------------------- | ---------------------------------------- |
| AllowedRequests       | Requests allowed by WAF rules            |
| BlockedRequests       | Requests blocked by WAF rules            |
| CountedRequests       | Requests logged by WAF rules             |
| PassedRequests        | Requests passed without triggering rules |
| WAFAllowedRequests    | Requests matched ALLOW rules             |
| WAFBlockedRequests    | Requests matched BLOCK rules             |
| WAFCountedRequests    | Requests matched COUNT rules             |
| WAFRequestCount       | Total requests inspected                 |
| AWSShieldDetected     | Events detected by AWS Shield            |
| AWSShieldMitigated    | DDoS events mitigated                    |
| AWSShieldDiagnostic   | DDoS diagnostics                         |
| AWSShieldAttackVolume | DDoS attack traffic volume               |
| AWSShieldEventRate    | Events/sec during attack                 |

---

# ⚡ Steps to Determine Threshold Value

1. **Calculate Transactions Per Minute:**
   2 crore (20 million) transactions/day → calculate per second.

2. **Consider Peak Load:**
   Peak traffic often = 2× average.

3. **Set Threshold:**
   Slightly above peak to avoid false positives.
   **Recommended:** 30,000 transactions / 60 sec

| Evaluation Window | Recommended Threshold |
| ----------------- | --------------------- |
| 60 seconds        | 30,000 transactions   |

---

# 🛡️ AWS WAF: ACLs vs Rules vs Conditions

| Feature     | Web ACLs                                | Rules                       | Conditions                          |
| ----------- | --------------------------------------- | --------------------------- | ----------------------------------- |
| Definition  | Collection of rules controlling traffic | Defines inspection logic    | Criteria for inspecting requests    |
| Purpose     | Apply security policies                 | Actions based on inspection | Detailed request evaluation         |
| Components  | Multiple rules & rule groups            | One or more conditions      | Match types: IP, string, size, etc. |
| Actions     | Allow, Block, Count                     | Allow, Block, Count         | Evaluate request attributes         |
| Scope       | Highest hierarchy                       | Intermediate                | Lowest level                        |
| Association | CloudFront, ALB, API Gateway            | Within Web ACL              | Within Rules                        |
| Examples    | MyWebACL                                | BlockSQLInjection           | SQL Injection match condition       |
| Management  | Organizes rules                         | Defines inspection logic    | Specifies rule criteria             |
| Types       | N/A                                     | Managed & Custom            | Various match conditions            |

---

# 🛡️ Recommended AWS WAF Values to Avoid DDoS

1. **IP Rate-based Rules:** Limit 2,000 requests / 5 min per IP
2. **IP Block/Allow Lists:** Trusted IPs ✅, Blocked IPs ❌
3. **Geo-Blocking:** Block unexpected countries 🌍
4. **SQL Injection Rule:** Block SQL payloads
5. **XSS Rule:** Block cross-site scripting payloads
6. **Size Constraint Rule:** Headers ≤10 KB, Bodies ≤100 KB
7. **Request Rate Limit:** 1,000 requests / 5 min
8. **Regex Pattern Sets:** Custom rules to detect malicious patterns
9. **Managed Rule Groups:** AWSManagedRulesCommonRuleSet, SQLi, Linux, KnownBadInputs
10. **Custom Rule Groups:** Tailored to application
11. **URI Rate Limit:** Example: 100 requests/min per IP for login/search pages
12. **Header Constraint Rule:** Header ≤8 KB, fields ≤512 bytes
13. **JSON/XML Body Parsing:** Inspect payloads
14. **Automation & Monitoring:** AWS Shield Advanced + CloudWatch
15. **Logging & Analysis:** Enable WAF logging

**Example Configuration Summary:**

* Rate Limit/IP: 2,000 requests / 5 min
* Request Size Limit: Headers 10 KB, Bodies 100 KB
* Rate-based Rule: 1,000 requests / 5 min
* URI-specific Rate Limit: 100 requests/min/IP

---

# 🛡️ AWS WAF Rule Actions

| Action    | Description                              | Usage                      | Example                                   |
| --------- | ---------------------------------------- | -------------------------- | ----------------------------------------- |
| Allow     | Permits requests matching rule           | Whitelist trusted traffic  | Allow traffic from trusted IPs            |
| Block     | Denies requests matching rule (HTTP 403) | Prevent malicious requests | Block IP ranges or SQLi attempts          |
| Count     | Logs requests without blocking           | Monitor traffic patterns   | Count requests for analysis               |
| CAPTCHA   | Presents CAPTCHA to verify human         | Mitigate bot traffic       | Challenge repeated login attempts         |
| Challenge | Additional verification step             | Verify suspicious requests | JavaScript execution for unusual requests |

---

If you want, I can **also create a fully visual version** with **emojis, icons, and colored callouts** for Markdown so it looks like a professional AWS guide.

Do you want me to do that?
