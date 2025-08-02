### 🛠️ Helm Hooks — What They Are and When to Use Them

Helm **hooks** allow you to **run Kubernetes resources at specific points** in the Helm release lifecycle (like before installing, after upgrading, before deleting, etc.).

They are useful when you want to run jobs or scripts **before or after** Helm does its main work.

---

## 📌 Common Use Cases for Helm Hooks

| Use Case               | Example                                   |
| ---------------------- | ----------------------------------------- |
| **Database migration** | Run a Job *before* starting your app      |
| **Seeding data**       | Job *after install*                       |
| **Cleanup tasks**      | Job *before delete*                       |
| **Backup operations**  | Job *before upgrade*                      |
| **Custom validation**  | Job *pre-install* that checks environment |

---

## 🔧 How to Use Helm Hooks

You define hooks in the **YAML manifest** using an annotation:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-hook-job
  annotations:
    "helm.sh/hook": pre-install
spec:
  template:
    spec:
      containers:
      - name: migrate
        image: my-image:latest
        command: ["./migrate.sh"]
      restartPolicy: Never
```

---

## 🎯 Hook Types (Lifecycle Events)

| Hook Name       | Description                        |
| --------------- | ---------------------------------- |
| `pre-install`   | Before any resources are installed |
| `post-install`  | After all resources are installed  |
| `pre-delete`    | Before deleting release resources  |
| `post-delete`   | After deleting release resources   |
| `pre-upgrade`   | Before upgrading release resources |
| `post-upgrade`  | After upgrading release resources  |
| `pre-rollback`  | Before rollback starts             |
| `post-rollback` | After rollback completes           |

---

## ⏱️ Controlling Hook Behavior

Additional hook annotations you can use:

```yaml
annotations:
  "helm.sh/hook": pre-install,pre-upgrade
  "helm.sh/hook-weight": "5"                 # Order of execution (lower runs first)
  "helm.sh/hook-delete-policy": hook-succeeded
```

### Hook Delete Policies:

* `before-hook-creation`: delete old hook jobs before new ones
* `hook-succeeded`: delete if succeeded
* `hook-failed`: delete if failed
* `before-hook-creation,hook-succeeded`: common combo

---

## ⚠️ Important Notes

* **Hooks are not managed** as part of the release's primary resources. You won't see them in `helm get manifest`.
* If the hook fails, the release can fail unless retries are handled.
* Helm does **not retry failed hooks** automatically.

---

## ✅ Example: Run a DB Migration Before App Install

```yaml
# templates/db-migrate-job.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: db-migrate
  annotations:
    "helm.sh/hook": pre-install
    "helm.sh/hook-delete-policy": hook-succeeded
spec:
  template:
    spec:
      containers:
      - name: db-migrate
        image: my-app-image
        command: ["./migrate.sh"]
      restartPolicy: Never
```

---

Would you like an example with multiple hooks or a complex setup like ordering with weights?
