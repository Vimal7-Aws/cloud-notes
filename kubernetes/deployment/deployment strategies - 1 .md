Helm supports Kubernetes **deployment strategies** primarily through the configuration of `Deployment` resources in your Helm chart templates (usually under `templates/deployment.yaml`). These strategies determine **how pods are updated** during a Helm upgrade or a Kubernetes rolling update.

There are **two main deployment strategies** supported by Kubernetes (and therefore Helm):

---

### ✅ 1. **RollingUpdate (default)**

This is the default and most commonly used strategy. It **gradually replaces old Pods with new ones**, ensuring availability during the update.

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 25%
    maxSurge: 25%
```

* **maxUnavailable**: The maximum number of pods that can be unavailable during the update.
* **maxSurge**: The maximum number of extra pods (above desired count) created during the update.

**Pros:**

* Zero downtime if configured properly
* Controlled rollout

**Cons:**

* If the new version has issues, some traffic may hit broken pods before rollback

---

### ✅ 2. **Recreate**

This strategy **deletes all old Pods before creating new ones**.

```yaml
strategy:
  type: Recreate
```

**Pros:**

* Simpler for apps that **can't handle multiple versions running simultaneously** (e.g., stateful services)

**Cons:**

* Causes **downtime** during the transition

---

## How to Set Strategy in a Helm Chart

Modify your `deployment.yaml` template like this:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "yourchart.fullname" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  strategy:
    type: {{ .Values.strategy.type }}
    {{- if eq .Values.strategy.type "RollingUpdate" }}
    rollingUpdate:
      maxUnavailable: {{ .Values.strategy.rollingUpdate.maxUnavailable }}
      maxSurge: {{ .Values.strategy.rollingUpdate.maxSurge }}
    {{- end }}
```

And in `values.yaml`:

```yaml
replicaCount: 3

strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 25%
    maxSurge: 25%
```

