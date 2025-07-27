# Kubernetes Security Best Practices

Security in general refers to the set of technologies, policies, controls, and services designed to protect:

* Data
* Application
* Users
* Infrastructure

Many people mistakenly believe that applications are automatically secure once they're deployed in the cloud. However, cloud security follows a shared responsibility model, meaning you must secure your applications in the cloud just as diligently as you would in an on-premises environment.

Kubernetes security is a bit complicated because security is in multiple layers:

* Infrastructure
* Kubernetes Platform
* Application

The challenge is that an attacker only needs to exploit one weak point to cause significant damage, whereas security engineers must ensure every layer and endpoint is secured to protect the entire system.

Some of the things that can be done to protect a Kubernetes cluster:

## Build a Secure Image (Image Scanning)

Container images are built in layers, with each step such as installing dependencies, copying files, and creating users adding to the final image. It's important to understand what happens at each stage to avoid introducing vulnerabilities.

To ensure secure images, you can follow these best practices:

* Avoid installing Code from untrusted registries
* Update to the latest version of the dependencies
* Eliminate unnecessary dependencies (Using leaner or smaller images)

Tools that are used in scanning images: snyk, sysdig

## Run Container as a Non-Root User

Running a container as root is not advisable because of the security risk involved.

**Privilege Escalation:**
If an attacker breaks out of the container, they inherit root-level access on the host machine; this can lead to full system compromise.

**Misconfigurations Are More Risky:**
Small mistakes (e.g., mounting host directories or using `--privileged`) become critical vulnerabilities when the container runs as root.

**Violation of Least Privilege Principle:**
Root containers violate one of the core tenets of security: "Only give the access necessary to perform the task."

## Network Policies

Network Policies in Kubernetes are a critical security feature used to control traffic flow between pods, essentially acting as virtual firewalls at the pod level. By default, every pod in Kubernetes can communicate with each other, which is not the case in the real world. You can define a backend pod to only accept traffic coming from the frontend. Network policies are defined by plugins like calico or cilium or service mesh.

**Why Network Policies Matter:**

* Minimize Lateral Movement
* Enforce Zero Trust Networking
* Segment Workloads
* Compliance Requirements

## Encryption of Communication Within the Cluster

Encryption within a Kubernetes (K8s) cluster is crucial for protecting sensitive data, both at rest and in transit. Without proper encryption, secrets, configuration data, and network traffic could be exposed to attackers or misused by compromised components. If this data is not encrypted, it becomes a prime target for attackers.

**Encryption at Rest:**
This refers to encrypting data stored on disk (e.g., in etcd or volumes).
There are 2 ways you can do this:

1.  Using Kubernetes `EncryptionConfiguration`. This means that you have to manage the encryption key yourself.
2.  Use of 3rd party secret management solution like HashiCorp Vault or AWS KMS.

**Encryption in Transit:**
This refers to encrypting data as it moves between components. Kubernetes uses TLS by default for communication between major components (e.g., kubelet, kube-apiserver).

## Protection of Application Data

Protecting application data in Kubernetes (or any cloud-native environment) is fundamental to securing user privacy, system integrity, and regulatory compliance. Whether it's sensitive customer data, business logic, or logs, if not protected properly, it becomes a target for breaches, ransomware, or insider abuse. Different ways to protect your data:

**Data at Rest:**

* Encrypt storage volumes using cloud provider options (e.g., AWS EBS, GCP PD encryption).
* Use database-level encryption (e.g., TDE for PostgreSQL, MySQL).
* Store sensitive files in secure, encrypted volumes (e.g., CSI with KMS support).

**Data in Transit:**

* Enforce TLS (HTTPS) for all external and internal communication.
* Use Mutual TLS (mTLS) between services with service meshes (e.g., Istio, Linkerd).
* Avoid sending sensitive data over unencrypted channels (e.g., HTTP, plain TCP).

## Kubernetes RBAC (Role-Based Access Control)

Kubernetes RBAC is a powerful and flexible access control mechanism used to define who can do what within your cluster. It helps enforce the principle of least privilege, which is essential for both security and compliance.

**Why RBAC Matters:**

* Prevents privilege escalation (e.g., not everyone should be able to delete pods or secrets).
* Ensures multi-tenancy and namespace isolation.
* Supports auditing and accountability.

A `Role` contains a set of permissions (rules) that specify what actions (verbs) can be performed on which resources in a namespace, while a `RoleBinding` assigns a `Role` to users, groups, or service accounts in the same namespace. Example of role and role binding.

A `ClusterRole` is like a `Role`, but it applies to:

* Cluster-scoped resources (e.g., nodes, persistent volumes)
* All namespaces
* Non-namespaced resources like CRDs, StorageClasses, etc.

While a `ClusterRoleBinding` assigns a `ClusterRole` to a user, group, or service account across the entire cluster. Example of cluster and cluster role binding.

A Service Account in Kubernetes is a special type of identity used by pods and workloads to interact with the Kubernetes API securely and non-interactively. While human users are authenticated via external systems (like OIDC or certificates), service accounts are used by applications running in your cluster such as a deployment, cron job, or controller.

## Backup and Restore

Backup and restore are critical components of any secure and resilient Kubernetes strategy. While Kubernetes is designed for self-healing, it doesn’t automatically protect you from data loss, accidental deletions, cluster failures, or ransomware attacks.

**Why Backup and Restore Matter in Kubernetes:**

* Protection from Human Error
* Disaster Recovery (DR)
* Compliance and Business Continuity
* Migration & Testing
```
