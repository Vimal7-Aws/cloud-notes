# Kubernetes Pod Key Features

Here are some key features associated with Kubernetes Pods:

* **Resource Requests and Limits:** Define the amount of CPU and memory each container within the pod is guaranteed to have (requests) and the maximum amount they can consume (limits). This helps with resource allocation and prevents a single pod from monopolizing node resources.

* **Labels:** Key-value pairs attached to Pods (and other Kubernetes objects) to categorize and organize them. Labels are arbitrary and user-defined, allowing for flexible grouping and selection.

* **Selectors:** Mechanisms to target and select groups of Pods (or other Kubernetes objects) based on their labels. Selectors are used by controllers (like Deployments and ReplicaSets) and Services to manage and discover Pods.

* **Liveness, Readiness, and Startup Probes:** Health checks defined for containers within a Pod.
    * **Liveness Probes:** Determine if a container is running and healthy. If a liveness probe fails, Kubernetes will restart the container.
    * **Readiness Probes:** Determine if a container is ready to serve traffic. Pods with failing readiness probes are removed from service endpoints.
    * **Startup Probes:** Used for slow-starting applications to indicate when they have initialized. Liveness and readiness probes are not executed until the startup probe succeeds.

* **ConfigMaps:** API objects used to store non-confidential configuration data as key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume.

* **Secrets:** API objects used to store sensitive information, such as passwords, API tokens, and SSH keys. Secrets are similar to ConfigMaps but are specifically designed for confidential data and are stored more securely.

* **Volumes:** Provide persistent storage for containers within a Pod. Volumes have a lifecycle that is tied to the Pod and can be backed by various storage options, including
   *  local storage
   *  network-attached storage
   *  and cloud provider storage.

* **Init Containers:** Specialized containers that run before the main application containers in a Pod. Init containers can perform setup tasks, such as downloading dependencies or configuring settings, before the application containers start.

* **Ephemeral Containers:** Temporary containers that can be added to a running Pod for debugging or troubleshooting purposes. Ephemeral containers share the Pod's namespaces and resources, allowing for inspection of the running environment.

* **Service Account:** Provides an identity for processes that run in a Pod. Service accounts are used by applications within the Pod to authenticate to the Kubernetes API server and access cluster resources, with permissions controlled by Role-Based Access Control (RBAC).

* **SecurityContext:** Defines privilege and access control settings for Pods and their containers. This includes settings like running as a non-root user, granting specific Linux capabilities, and controlling access to host resources.

* **Affinity and Anti-Affinity Rules:** Control how Pods are scheduled across nodes in the cluster.
    * **Affinity:** Rules that attract Pods to specific nodes based on labels.
    * **Anti-Affinity:** Rules that prevent Pods from being scheduled on specific nodes based on labels, enhancing availability and fault tolerance.

* **Pod Preemption & Priority:** Mechanisms for prioritizing Pods. Higher-priority Pods may preempt (evict) lower-priority Pods from nodes if there are insufficient resources.

* **Pod Disruption Budget (PDB):** An API object that limits the number of replicas of an application that can be voluntarily disrupted at any given time. This ensures a certain level of availability during planned maintenance or upgrades.

* **Container Life Cycle Hooks:** Allow you to execute custom scripts or commands at specific points in a container's lifecycle, such as before a container is created (`PostStart`) or before it is terminated (`PreStop`).
