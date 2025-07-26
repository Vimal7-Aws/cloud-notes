### 🧱 Init Containers in Kubernetes

An **Init Container** is a special type of container in a Pod that **runs before the main application containers** start. It’s ideal for performing initialization logic like:

* Waiting for dependencies (DBs, services)
* Setting up config or secrets
* Performing database migrations
* Downloading files or binaries

---

## ✅ Key Properties

* **Run sequentially**: One init container runs **at a time**, and the next starts only after the previous one completes **successfully**.
* If any init container **fails**, Kubernetes will restart the pod (based on restartPolicy).
* They **have access to the same volumes** as app containers.

---

## 🔍 Common Use Cases

| Use Case                      | Description                              |
| ----------------------------- | ---------------------------------------- |
| Wait for DB to be ready       | Poll a DB until it's responsive          |
| Copy files into shared volume | Download/configure software              |
| Security/sidecar injection    | Setup environment before main app starts |
| Setup DNS config or certs     | Pull TLS certs or service IPs            |

---

## 📦 Example Pod with Init Container

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  containers:
  - name: app
    image: busybox
    command: ["sh", "-c", "cat /init/message.txt && sleep 3600"]
    volumeMounts:
    - name: init-volume
      mountPath: /init

  initContainers:
  - name: init-setup
    image: busybox
    command: ["sh", "-c", "echo Hello from Init > /init/message.txt"]
    volumeMounts:
    - name: init-volume
      mountPath: /init

  volumes:
  - name: init-volume
    emptyDir: {}
```

In this example:

* `init-setup` creates a file in `/init/message.txt`
* Main `app` container reads that message

---

## 🔄 Helm Support

In Helm charts, you define init containers under `spec.initContainers`:

```yaml
spec:
  initContainers:
    - name: wait-for-db
      image: busybox
      command: ['sh', '-c', 'until nc -z db 5432; do echo waiting for db; sleep 2; done']
```

---

## 🧪 How to Debug

Use `kubectl describe pod <pod-name>` to check init container status:

```sh
kubectl get pod init-demo
kubectl describe pod init-demo
```

---

Yes, ✅ **init containers must complete successfully before the main container(s) start**.

---

## 🔄 How It Works:

1. **Init containers run sequentially**, in the order they’re defined.
2. Each must **exit with status `0`** (i.e., success).
3. Only after all init containers finish, Kubernetes starts the **main app containers**.
4. If any init container fails:

   * Kubernetes retries it (with backoff),
   * The main containers will **not start** until all init containers succeed.

---

## 🧠 Why is this behavior important?

It ensures that your app only starts **after the environment is ready**, for example:

* Wait for a dependency (e.g., DB, API) to be reachable
* Run DB migrations
* Extract config files or secrets
* Copy binaries or set up shared volumes

---

## 🧪 Example

```yaml
initContainers:
  - name: wait-for-db
    image: busybox
    command: ['sh', '-c', 'until nc -z db-service 5432; do echo waiting for db; sleep 2; done']
```

This container will **block the app** from starting until the database is reachable.

---


