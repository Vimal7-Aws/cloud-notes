## What is a Kubernetes Pod?

---

Before getting into Kubernetes Pod concepts, let's understand containers.

A container, as we all know, is a self-contained environment where we package applications and their dependencies. Typically, a container runs a single process (although there are ways to run multiple processes).
Each container gets an IP address and can attach volumes and control CPU and memory resources, among other things. 
All these happen via the concepts of <mark>**namespaces and control groups**.<mark>

Kubernetes is a container orchestration system for deploying, scaling, and managing containerized applications, and it has its own way of running containers. We call it a **pod**. A pod is the smallest deployable unit in Kubernetes that represents a single instance of an application.

For example, if you want to run the Nginx application, you run it in a pod.

So how does it differ from a container?

A container is a single unit. However, a pod can contain more than one container. You can think of pods as a box that can hold one or more containers together.

**Pod** provides a higher level of abstraction that allows you to manage multiple containers as a single unit. Here, instead of each container getting an IP address, the pod gets a single unique IP address, and containers running inside the pod use `localhost` to connect to each other on different ports.


![image](https://github.com/user-attachments/assets/2b9ebb7a-218e-4247-a409-318779cf6a6e)


It means containers inside the Kubernetes pod share the following:

* **Network namespace:** All containers inside a pod communicate via `localhost`.
  
* **IPC namespace:** All containers use a shared interprocess communication namespace.
  
* **UTS namespace:** All containers share the same hostname.  
  
---
What is **not** shared between containers inside a pod?  


* By default, the **PID namespace** is not shared; however, Kubernetes provides options to enable process sharing between containers inside the pod using the `shareProcessNamespace` option.


* The **mount namespace** is not shared between containers. Each container has its own private filesystem and directories. However, the pod's mounted volumes are shared between containers.


---
  
In a nutshell, here is what you should know about a pod:  


* Pods are the smallest deployable units in Kubernetes.
* Pods are ephemeral in nature; they can be created, deleted, and updated.
* A pod can have more than one container; there is no limit to how many containers you can run inside a pod.
* Each pod gets a unique IP address.
* Pods communicate with each other using the IP address.
* Containers inside a pod connect using `localhost` on different ports.
* Containers running inside a pod should have different port numbers to avoid port clashes.
* You can set CPU and memory resources for each container running inside the pod.
* Containers inside a pod share the same volume mount.
* All the containers inside a pod are scheduled on the same node; it cannot span multiple nodes.
* If there is more than one container, during the pod startup, all the main containers start in parallel. Whereas the init containers inside the pod run in sequence.
  
