Using **DaemonSets with `tolerations` and `affinity`** allows you to **control where** the Pods run — great for targeting only certain nodes (e.g., GPU nodes, tainted nodes, availability zones, etc.).

---

## ✅ 1. **Using Tolerations with DaemonSets**

`Tolerations` allow Pods to run on nodes that have **taints**.

### 🔧 Example: Tolerate a tainted node

#### Taint on the Node:

```bash
kubectl taint nodes node1 key=value:NoSchedule
```

#### DaemonSet YAML with toleration:

```yaml
spec:
  template:
    spec:
      tolerations:
      - key: "key"
        operator: "Equal"
        value: "value"
        effect: "NoSchedule"
```

> 🧠 **Meaning**: This DaemonSet pod can run on nodes that are **tainted** with `key=value:NoSchedule`.

---

## ✅ 2. **Using Node Affinity with DaemonSets**

`Affinity` is used to tell Kubernetes **which nodes to prefer** (or require) for scheduling.

### 🔧 Example: Only run on nodes with label `node-type=infra`

```yaml
spec:
  template:
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-type
                operator: In
                values:
                - infra
```

> 🧠 **Meaning**: This DaemonSet will **only schedule on nodes** with label `node-type=infra`.

---

## 🧪 Combining Tolerations + Affinity (Full YAML Snippet)

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: my-daemonset
spec:
  selector:
    matchLabels:
      app: node-agent
  template:
    metadata:
      labels:
        app: node-agent
    spec:
      containers:
      - name: agent
        image: my-agent:latest
      tolerations:
      - key: "node-role"
        operator: "Equal"
        value: "monitoring"
        effect: "NoSchedule"
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role
                operator: In
                values:
                - monitoring
```

---

## 🧠 Real-world Example Use Cases

| Scenario                          | Use Toleration?              | Use Affinity?                    |
| --------------------------------- | ---------------------------- | -------------------------------- |
| Run DaemonSet only on GPU nodes   | ✅ (if GPU nodes are tainted) | ✅ (match `gpu=true`)             |
| Run on dedicated monitoring nodes | ✅                            | ✅ (label like `role=monitoring`) |
| Avoid system-critical nodes       | ❌ (don't tolerate taints)    | ✅ (match only safe nodes)        |

---

Let me know if you want:

* a working demo setup (minikube/kind)
* how to handle DaemonSets with taints like `CriticalAddonsOnly`
* Helm templating for affinity/toleration logic
