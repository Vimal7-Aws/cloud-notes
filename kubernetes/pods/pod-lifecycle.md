# Kubernetes Pod Lifecycle





![image](https://github.com/user-attachments/assets/eb291cd0-4b94-4abe-8265-89bd64280624)


</br>

A Kubernetes Pod goes through a defined lifecycle, from its creation to its termination. Understanding this lifecycle is crucial for managing and troubleshooting applications running in Kubernetes. Here are the key phases in a Pod's lifecycle:

</br>  

**1. Pending:**

* This is the initial state of a Pod after it has been submitted to the Kubernetes API server.
* The Pod has been accepted by the system, but one or more of its containers has not been created and scheduled onto a node yet.
* This phase includes the time spent by the scheduler trying to find a suitable node for the Pod. Factors influencing this include:
  *  resource availability
  *  node taints and tolerations
  *  and node affinity/anti-affinity rules.
* Downloading container images might also happen during this phase, although it can also occur later.

**2. Running:**

* The Pod has been scheduled to a node, and all containers in the Pod have been created.
* At least one container is still running, or is in the process of starting or restarting.
* Liveness and readiness probes, if defined, start to be evaluated during this phase.

**3. Succeeded:**

* All containers in the Pod have terminated successfully and will not be restarted.
* This phase is typically seen for batch jobs or tasks that run to completion.

**4. Failed:**

* One or more containers in the Pod terminated in failure (indicated by a non-zero exit code) or was terminated by the system.
* The system might attempt to restart the Pod based on its `restartPolicy`.
* The `restartPolicy` (Always, OnFailure, Never) is specified in the Pod's `spec`.

**5. Unknown:**

* The state of the Pod could not be determined, typically due to a communication error with the kubelet on the node where the Pod is supposed to be running.
* This phase should be investigated as it indicates a potential issue with the node or the kubelet.

## Pod Conditions

In addition to these phases, a Pod can also have conditions that provide more granular information about its status. Some common Pod conditions include:

* **PodScheduled:** The Pod has been assigned to a node.
* **ContainersReady:** All containers in the Pod are ready.
* **Initialized:** All init containers in the Pod have completed successfully.
* **Ready:** The Pod is able to serve requests and should be added to the load balancing pools of all matching Services. This condition is true when all containers are ready.

You can view the current phase and conditions of a Pod using the `kubectl describe pod <pod-name>` command.

## Pod Termination

Pods are generally considered ephemeral and are not meant to be long-lived, persistent entities. When a Pod needs to be terminated (e.g., due to scaling down, rolling updates, or node issues), Kubernetes follows a graceful termination process by default:

1.  **API Server Receives Termination Request:** The user or a controller requests the deletion of the Pod.
2.  **Pod Marked for Termination:** The API server updates the Pod's metadata by setting a `deletionTimestamp`.
3.  **Endpoints Removed:** The Pod is removed from the endpoints list of any matching Services, preventing new connections.
4.  **PreStop Hook Executed (if defined):** If the Pod has a `preStop` hook defined in its `spec`, the kubelet on the node executes this hook. This allows the application to gracefully shut down, save state, or finish processing requests. There's a default grace period (30 seconds) for this to complete.
5.  **SIGTERM Signal Sent:** The kubelet sends a `SIGTERM` signal to the main processes in each container of the Pod.
6.  **Grace Period Expires:** If the containers haven't terminated within the grace period, the kubelet sends a `SIGKILL` signal to forcefully terminate them.
7.  **Pod Removed from API Server:** Once all containers have terminated, the kubelet informs the API server, and the Pod object is deleted.

Understanding the Pod lifecycle and termination process is essential for building resilient and well-behaved applications on Kubernetes. You should design your applications to handle termination signals gracefully and consider using `preStop` hooks for necessary cleanup tasks.
