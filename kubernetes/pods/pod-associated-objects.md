![image](https://github.com/user-attachments/assets/6e1f281a-5791-44b0-9fe6-44e41035ea7f)



When it comes to running applications on Kubernetes, we don't run an individual pod. Because Kubernetes is all about scaling and maintaining the availability of pods.

So if you run a single pod, it will be a single point of failure. Because the Pods themselves cannot be directly scaled.

As we discussed in the Kubernetes Architecture, we need controllers like Replicaset to ensure the desired number of pods are running at all the time.

Kubernetes has different types of objects associated with pods for different use cases.

Click to view in HD
The following are important pod-associated objects:

* **Replicaset:** To maintain a stable set of Pods replicas running at any given time.
* **Deployment:** To run stateless applications like web servers, APIs, etc.
* **StatefulSets:** To run stateful applications like distributed databases.
* **Daemonsets:** To run agents on all the Kubernetes nodes.
* **Jobs:** For batch processing
* **CronJobs:** Scheduled Jobs
