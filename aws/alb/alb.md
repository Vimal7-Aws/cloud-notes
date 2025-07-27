Okay, here are 100 questions and answers about AWS Elastic Load Balancer (ELB) Application Load Balancer (ALB).

**General Concepts**

1.  **What is an Application Load Balancer (ALB)?**
    An ALB is a type of load balancer offered by AWS that operates at the application layer (Layer 7 of the OSI model). It routes traffic to targets (like EC2 instances, containers, and Lambda functions) based on the content of the request, such as HTTP headers, hostnames, and paths.

2.  **What are the key benefits of using an ALB?**
    Key benefits include content-based routing, support for microservices and containerization, advanced request routing, TLS termination, integrated health checks, scalability, and high availability.

3.  **At which layer of the OSI model does an ALB operate?**
    An ALB operates at Layer 7, the application layer.

4.  **What types of targets can an ALB route traffic to?**
    ALBs can route traffic to EC2 instances, containers (using ECS or EKS), IP addresses, and Lambda functions.

5.  **What is a listener in the context of an ALB?**
    A listener is a process that checks for connection requests. You configure it with a protocol and a port. When the ALB receives a request on the configured port and protocol, the listener forwards the request to a target group.

6.  **What is a target group in the context of an ALB?**
    A target group is a collection of targets (EC2 instances, containers, etc.) that receive traffic from one or more listeners. You define health checks for each target group.

7.  **What is content-based routing? How does ALB achieve this?**
    Content-based routing allows the load balancer to forward requests to different backends based on the content of the HTTP request, such as the URL path or the Host header. ALB achieves this using listener rules.

8.  **What are listener rules in an ALB?**
    Listener rules define how the ALB routes requests to different target groups based on conditions such as the path of the URL, the Host header, HTTP request methods, query parameters, and source IP addresses.

9.  **What is the default routing rule in an ALB listener?**
    Each listener must have a default rule that specifies a target group to which requests are forwarded if none of the other rules match.

10. **How does an ALB handle multiple listeners?**
    An ALB can have multiple listeners, each configured with a different port and protocol, allowing it to handle various types of traffic simultaneously.

**Routing and Traffic Management**

11. **How can you route traffic based on the hostname using an ALB?**
    You can create a listener rule with a "Host header" condition that forwards requests to a specific target group if the Host header in the HTTP request matches the configured value. For example, routing `api.example.com` to one set of instances and `website.example.com` to another.

12. **How can you route traffic based on the URL path using an ALB?**
    You can create a listener rule with a "Path pattern" condition that forwards requests to a specific target group if the URL path in the HTTP request matches the configured pattern. For example, routing `/api/*` to an API backend and `/images/*` to an image server.

13. **Can an ALB route traffic based on HTTP headers other than Host?**
    Yes, ALB allows routing based on custom HTTP headers using the "HTTP header" condition in listener rules.

14. **Can an ALB route traffic based on HTTP request methods (GET, POST, etc.)?**
    Yes, you can create listener rules with an "HTTP request method" condition to route traffic based on the HTTP method.

15. **Can an ALB route traffic based on query parameters?**
    Yes, ALB allows routing based on query parameters using the "Query parameter" condition in listener rules.

16. **Can an ALB route traffic based on the source IP address?**
    Yes, ALB allows routing based on the source IP address using the "Source IP" condition in listener rules.

17. **What is sticky sessions (session affinity) in an ALB? How is it configured?**
    Sticky sessions ensure that requests from the same client are consistently routed to the same target within a target group. It's configured at the target group level by enabling "Stickiness" and setting a cookie duration.

18. **How does ALB handle slow clients?**
    ALB has idle timeout settings for both the client and the backend connections. You can configure these timeouts to manage resources effectively when dealing with slow clients.

19. **What is the purpose of cross-zone load balancing in an ALB?**
    Cross-zone load balancing distributes traffic evenly across all enabled Availability Zones, ensuring better fault tolerance and preventing overloading instances in a single zone. It is enabled at the ALB level.

20. **How does ALB handle traffic during deployment updates of backend instances?**
    ALB, combined with proper health checks, ensures that traffic is only routed to healthy instances. During deployment updates, unhealthy instances are taken out of service, and traffic is directed to the remaining healthy ones.

**Health Checks**

21. **What are health checks in the context of an ALB?**
    Health checks are used by the ALB to monitor the health of the registered targets in a target group. Unhealthy targets are prevented from receiving traffic.

22. **What parameters can you configure for ALB health checks?**
    Configurable parameters include the protocol, port, path, timeout, interval, healthy threshold, and unhealthy threshold.

23. **What are the possible health check status codes that an ALB considers healthy by default for HTTP/HTTPS?**
    By default, an ALB considers HTTP status codes 200-399 as healthy.

24. **Can you customize the healthy status codes for ALB health checks?**
    Yes, you can configure the list of HTTP status codes that the ALB considers healthy.

25. **How does ALB determine if a target is unhealthy?**
    A target is considered unhealthy if it fails to respond successfully to the configured number of consecutive health checks (unhealthy threshold).

26. **What happens to traffic destined for an unhealthy target?**
    ALB stops routing traffic to targets that are marked as unhealthy.

27. **How can you use health checks to perform blue/green deployments?**
    By registering the new (blue or green) instances with a new target group and updating the listener rules to point to this new group after the instances pass health checks, you can perform a seamless cutover.

28. **Can you configure different health check paths for different target groups behind the same ALB?**
    Yes, each target group associated with an ALB can have its own independent health check configuration, including the path.

29. **What are the implications of aggressive vs. relaxed health check settings?**
    Aggressive settings (shorter interval, lower healthy threshold) can lead to targets being marked unhealthy more quickly, potentially causing unnecessary traffic shifts. Relaxed settings might delay the detection of unhealthy instances, leading to service disruptions.

30. **Does ALB support health checks for targets using protocols other than HTTP/HTTPS?**
    Yes, ALB also supports TCP and gRPC health checks.

**Security**

31. **How does ALB handle TLS/SSL encryption?**
    ALB supports TLS termination at the load balancer. You can upload SSL/TLS certificates to the ALB, and it will handle the encryption and decryption of traffic between clients and the load balancer. Traffic between the ALB and the backend instances can be either HTTP or HTTPS.

32. **What is an SSL/TLS certificate and how is it associated with an ALB?**
    An SSL/TLS certificate verifies the identity of a website and encrypts traffic. You associate certificates with an ALB's HTTPS listener. You can use AWS Certificate Manager (ACM) to provision and manage these certificates.

33. **What are SSL policies in the context of an ALB?**
    SSL policies define the protocols and ciphers that the ALB uses for TLS negotiation with clients. You can choose from predefined security policies or create custom ones.

34. **Can you enforce HTTPS-only traffic to your application using an ALB?**
    Yes, you can configure an HTTP listener to redirect all incoming HTTP requests to the HTTPS listener.

35. **How can you control access to your application at the ALB level?**
    You can use Security Groups associated with the ALB to control the inbound and outbound traffic at the network level. For more granular control, you can integrate with AWS WAF (Web Application Firewall).

36. **What is AWS WAF and how does it integrate with an ALB?**
    AWS WAF is a web application firewall that helps protect your web applications from common web exploits. You can associate WAF rules with your ALB to filter malicious traffic before it reaches your backend instances.

37. **Does ALB support mutual TLS (mTLS)?**
    Yes, ALB supports mutual TLS, allowing the load balancer to authenticate client certificates. You can configure a trust store with the CA certificates and associate it with an HTTPS listener.

38. **How can you implement certificate-based authentication for clients accessing your application through an ALB?**
    By configuring mutual TLS on the ALB, you can require clients to present a valid certificate that is trusted by the ALB before their requests are forwarded to the backend.

39. **What are the considerations when using HTTPS between the ALB and backend instances?**
    You need to ensure that your backend instances are configured with SSL/TLS certificates and that the ALB is configured to trust these certificates (if required for end-to-end TLS). This adds overhead but provides end-to-end encryption.

40. **How do security groups on the ALB and backend instances interact?**
    The security group on the ALB controls inbound traffic to the ALB, while the security groups on the backend instances control inbound traffic to those instances from the ALB (typically on the target group port). You need to ensure that the ALB's security group allows traffic on the listener ports and that the backend instance's security group allows traffic from the ALB's IP addresses (or the security group of the ALB itself).

**Scalability and Availability**

41. **How does ALB scale to handle increasing traffic?**
    ALB is a fully managed service and scales automatically based on the incoming traffic. AWS handles the scaling infrastructure transparently.

42. **What is the role of Availability Zones (AZs) in ALB's high availability?**
    You can enable an ALB across multiple Availability Zones. This ensures that if one AZ becomes unavailable, the ALB can still route traffic to healthy instances in other AZs.

43. **How do you ensure high availability for your application using an ALB?**
    By deploying your backend instances across multiple AZs and registering them with the ALB, and by ensuring the ALB itself is enabled across multiple AZs, you can achieve high availability.

44. **What happens if an Availability Zone where some of your backend instances are running fails?**
    If the ALB is configured across multiple AZs, it will continue to route traffic to the healthy instances in the remaining available AZs.

45. **Is there a limit to the number of requests an ALB can handle?**
    ALB is designed to handle a very large number of requests and scales automatically. While there might be soft limits, they are generally very high and can be increased by contacting AWS support.

46. **How does ALB handle sudden spikes in traffic?**
    ALB is designed to handle sudden increases in traffic by automatically scaling its capacity.

47. **Can you manually scale an ALB?**
    No, ALB is automatically scaled by AWS and does not offer manual scaling options.

48. **What are the best practices for ensuring the scalability and availability of applications behind an ALB?**
    Distribute instances across multiple AZs, configure robust health checks, enable cross-zone load balancing, and design your application to be stateless where possible to facilitate scaling.

49. **How does ALB contribute to disaster recovery strategies?**
    By enabling ALBs in multiple regions and using services like Route 53 for DNS-based failover, you can implement disaster recovery strategies where traffic can be routed to a backup environment in a different region if the primary region fails.

50. **What are the cost implications of running an ALB across multiple Availability Zones?**
    You are charged for the ALB itself per hour and for the amount of data processed by the ALB. Running across multiple AZs doesn't change the hourly cost of the ALB but might slightly increase data transfer costs if your targets are in different AZs than the clients. However, the increased availability is generally worth the potential cost difference.

**Integration with Other AWS Services**

51. **How does ALB integrate with EC2?**
    You can register EC2 instances as targets in a target group associated with an ALB. The ALB will then route traffic to these instances based on the configured listener rules and health check status.

52. **How does ALB integrate with ECS (Elastic Container Service)?**
    You can register ECS services as targets in an ALB target group. ALB can route traffic to the containers based on dynamic port mapping.

53. **How does ALB integrate with EKS (Elastic Kubernetes Service)?**
    You can use the AWS Load Balancer Controller in EKS to provision ALBs that route traffic to your Kubernetes pods.

54. **How does ALB integrate with Lambda?**
    You can register Lambda functions as targets for an ALB, allowing you to trigger serverless functions via HTTP/HTTPS requests.

55. **How does ALB integrate with ACM (AWS Certificate Manager)?**
    ACM is the recommended way to provision and manage SSL/TLS certificates for use with ALBs. You can easily associate certificates stored in ACM with your ALB listeners.

56. **How does ALB integrate with CloudWatch?**
    ALB automatically provides metrics to CloudWatch, such as request counts, latency, error counts, and connection counts, allowing you to monitor the performance and health of your load balancer.

57. **How does ALB integrate with CloudTrail?**
    CloudTrail logs API calls made to ALB, providing an audit trail of configuration changes.

58. **How does ALB integrate with Route 53?**
    You typically use a DNS name provided by Route 53 to point to the DNS name of your ALB, providing a stable and user-friendly endpoint for your application.

59. **How does ALB integrate with Auto Scaling?**
    You can associate target groups with Auto Scaling groups. When Auto Scaling launches new instances, they are automatically registered with the ALB; when instances are terminated, they are automatically deregistered.

60. **How does ALB integrate with Global Accelerator?**
    You can configure AWS Global Accelerator to direct traffic to your ALB in one or more AWS Regions, improving the performance and availability for global users.

**Monitoring and Logging**

61. **What metrics does ALB provide to CloudWatch?**
    Key metrics include `RequestCount`, `HTTPCode_ELB_4XX_Count`, `HTTPCode_ELB_5XX_Count`, `HTTPCode_Target_4XX_Count`, `HTTPCode_Target_5XX_Count`, `TargetResponseTime`, `HealthyHostCount`, `UnHealthyHostCount`, `ClientTLSNegotiationErrorCount`, and more.

62. **How can you monitor the health of your backend instances using ALB?**
    You can monitor the `HealthyHostCount` and `UnHealthyHostCount` metrics in CloudWatch for your target groups. You can also set up alarms based on these metrics.

63. **What are access logs in the context of an ALB?**
    Access logs provide detailed information about requests made to your ALB, including the time the request was received, client IP address, request path, response codes, and latency.

64. **Where can you store ALB access logs?**
    ALB access logs can be stored in an Amazon S3 bucket that you specify.

65. **What information is contained in an ALB access log entry?**
    An access log entry typically includes fields like client IP address, request time, method, URI, HTTP version, response code, user agent, and more.

66. **What are request tracing logs in the context of an ALB?**
    ALB supports request tracing, which adds a unique identifier (`X-Amzn-Trace-Id` header) to requests, allowing you to track requests across different services.

67. **How can you enable and use request tracing with an ALB?**
    Request tracing is automatically enabled. The ALB adds the `X-Amzn-Trace-Id` header to requests. Your backend applications can log this header for end-to-end tracing.

68. **What are the best practices for monitoring an ALB?**
    Monitor key CloudWatch metrics, enable and analyze access logs, set up alarms for critical metrics, and consider using request tracing for debugging and performance analysis.

69. **How can you troubleshoot issues using ALB logs and metrics?**
    CloudWatch metrics can help identify performance bottlenecks or health check failures. Access logs can provide insights into client behavior and error patterns. Request tracing can help pinpoint issues in distributed applications.

70. **What are the different types of errors you might see in ALB logs and metrics?**
    You might see HTTP 4xx and 5xx errors originating from the ALB or the backend targets. ELB-specific 4xx/5xx errors can indicate issues with the load balancer itself.

**Advanced Configurations**

71. **What are fixed responses in ALB listener rules?**
    Fixed responses allow the ALB to return a predefined HTTP response to the client without forwarding the request to a backend target. This can be useful for serving static content or implementing redirects.

72. **How can you configure redirects using an ALB?**
    You can create a listener rule with a "Redirect" action to redirect incoming requests to a different URL, protocol, or port.

73. **What are the different types of redirects supported by ALB?**
    ALB supports HTTP redirects (301, 302) and HTTPS redirects. You can configure the redirect status code, protocol, hostname, path, and query parameters.

74. **What are target group attributes in an ALB? Can you provide some examples?**
    Target group attributes configure various settings for the target group. Examples include stickiness settings (duration, cookie name), deregistration delay, slow start mode, and load balancing algorithm type (round robin, least outstanding requests).

75. **What is the deregistration delay for targets in a target group? Why is it important?**
    The deregistration delay is the time period during which the ALB waits for in-flight requests to complete when a target is deregistered or marked as unhealthy before forcibly closing connections. This helps prevent request failures.

76. **What is slow start mode in an ALB target group? When might you use it?**
    Slow start mode allows newly registered or healthy targets to gradually receive a larger share of traffic. This helps prevent overwhelming new instances with a sudden surge of requests. It's useful during deployments or scaling events.

77. **What are the different load balancing algorithms supported by ALB?**
    ALB primarily uses a round robin algorithm by default but also supports a "least outstanding requests" algorithm.

78. **How can you modify the load balancing algorithm for an ALB target group?**
    You can configure the load balancing algorithm type as a target group attribute.

79. **What are the use cases for IP address as a target type in an ALB?**
    Using IP addresses as targets allows you to load balance applications running on non-EC2 instances that are reachable via IP and port, such as on-premises servers connected via VPN or Direct Connect.

80. **What are the use cases for Lambda functions as targets in an ALB?**
    This allows you to build serverless web applications where HTTP/HTTPS requests are directly handled by Lambda functions.

**Troubleshooting**

81. **What are some common issues that can cause targets to be marked as unhealthy by an ALB?**
    Common issues include application errors, network connectivity problems, firewall rules blocking health check traffic, and resource exhaustion on the instances.

82. **How can you troubleshoot unhealthy targets behind an ALB?**
    Check the health check configuration, verify application logs on the instances, ensure network connectivity between the ALB and instances, and review security group rules.

83. **What does an HTTP 503 error from an ALB typically indicate?**
    An HTTP 503 Service Unavailable error from the ALB often indicates that there are no healthy targets in the target group to serve the request.

84. **What does an HTTP 400 error from an ALB typically indicate?**
    An HTTP 400 Bad Request error from the ALB usually indicates an issue with the client's request, such as a malformed header or an invalid request.

85. **What does an HTTP 504 error (Gateway Timeout) from an ALB typically indicate?**
    An HTTP 504 Gateway Timeout error from the ALB usually means that the ALB did not receive a response from a healthy target within the idle timeout period.

86. **If you are seeing connection timeouts to your ALB, what could be the potential causes?**
    Potential causes include security group rules blocking traffic to the ALB, network connectivity issues, or problems with the listener configuration.

87. **How can you diagnose high latency for requests going through an ALB?**
    Check the `TargetResponseTime` metric in CloudWatch, review application performance on the backend instances, and examine network latency between the ALB and the targets.

88. **What steps can you take if your ALB is not routing traffic as expected?**
    Review the listener rules, verify the target group registrations, check the health check status of the targets, and examine the ALB's security group rules.

89. **How can you verify that sticky sessions are working correctly on your ALB?**
    You can inspect the cookies in the client's browser or the request headers being sent to the backend instances to see if the ALB is maintaining session affinity using the configured cookie.

90. **What are some limitations of an ALB compared to a Network Load Balancer (NLB)?**
    ALB operates at Layer 7 and does not support protocols other than HTTP, HTTPS, and gRPC. It also has higher latency than NLB, which operates at Layer 4. NLB can handle very high throughput and offers static IP addresses per Availability Zone.

**Cost and Management**

91. **How is the cost of an ALB calculated?**
    ALB costs are based on two components: the hourly fee for each hour (or partial hour) that the load balancer is running, and the Load Balancer Capacity Units (LCUs) consumed. LCUs are calculated based on the number of new connections, active connections, data processed, and rule evaluations.

92. **What are Load Balancer Capacity Units (LCUs)?**
    LCUs are a metric used to calculate the consumption of your Application Load Balancer. One LCU contains one of the following (whichever is highest): 25 new connections per second, 3,000 active connections per minute, 1 GB of data processed per hour, or 1,000 rule evaluations per second.

93. **How can you estimate the cost of running an ALB for your application?**
    You need to estimate your application's traffic patterns, including the number of connections, data transfer, and the complexity of your routing rules, and then use the AWS pricing calculator.

94. **What are the AWS CLI commands to create, manage, and delete an ALB?**
    Commands include `aws elbv2 create-load-balancer`, `aws elbv2 create-listener`, `aws elbv2 create-target-group`, `aws elbv2 register-targets`, `aws elbv2 describe-load-balancers`, `aws elbv2 describe-listeners`, `aws elbv2 delete-load-balancer`, and more.

95. **How can you manage ALBs using the AWS Management Console?**
    You can use the Elastic Load Balancing section of the AWS Management Console to create, configure, monitor, and manage your ALBs, listeners, and target groups through a graphical interface.

96. **How can you manage ALBs using Infrastructure as Code (IaC) tools like AWS CloudFormation or Terraform?**
    You can define your ALB resources (load balancer, listeners, target groups, rules) in CloudFormation templates or Terraform configurations and deploy and manage them programmatically.

97. **What are the best practices for managing the lifecycle of an ALB?**
    Use IaC for provisioning and management, implement proper monitoring and logging, regularly review and update security configurations, and plan for scaling and high availability.

98. **Can you have multiple target groups associated with a single listener?**
    Yes, a listener can have multiple rules, each forwarding traffic to a different target group based on the rule conditions. A default rule also points to a target group.

99. **Can you share an ALB across multiple AWS accounts?**
    Direct sharing of an ALB across accounts is not directly supported. However, you can use techniques like peering VPCs and configuring security groups appropriately to allow traffic flow across accounts via the ALB.

100. **What is the future roadmap for AWS ALB? Where can you find information about new features and updates?**
    AWS continuously adds new features and improvements to ALB. You can stay updated by following the AWS What's New page, the AWS Elastic Load Balancing documentation, and the AWS Application Load Balancer release notes. Attending AWS re:Invent and other AWS events can also provide insights into future directions.
