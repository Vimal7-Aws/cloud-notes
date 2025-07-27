In **AWS ECS (Elastic Container Service)**, **dynamic port mapping** is a feature that allows ECS to automatically assign a host port to a container port **at runtime**. This is commonly used with the **`awsvpc` or `bridge`** networking mode (primarily `bridge`) when running multiple tasks on the same container instance, to avoid port conflicts.

---

### 🔧 Use Case for Dynamic Port Mapping

Suppose multiple tasks need to expose port `80` inside the container, but a container instance can't have more than one task listening on the same port at the host level (e.g., port `80`). With **dynamic port mapping**, ECS assigns **random high-numbered ports** on the host for each task.

---

### 🧱 How It Works

* **Container Port**: Defined in task definition (e.g., `80`)
* **Host Port**: Set to `0` → ECS dynamically allocates an unused port on the container instance.
* This port is then registered in **ECS service discovery** or **ALB target group** (if configured), so you can route traffic to the correct task.

---

### 📝 Task Definition Example (Port Mapping)

```json
"portMappings": [
  {
    "containerPort": 80,
    "hostPort": 0,
    "protocol": "tcp"
  }
]
```

---

### 🚀 ALB Integration with Dynamic Port Mapping

When using **Application Load Balancer (ALB)** with dynamic ports:

1. Use **`awsvpc`** network mode (required for Fargate, optional for EC2).
2. ALB target group uses **instance IP + dynamically assigned port**.
3. ECS automatically **registers/deregisters** tasks with the correct IP/port in the target group.

Example ECS Service with ALB target group (Terraform sample):

```hcl
target_group {
  container_name   = "my-app"
  container_port   = 80 # port inside container
}
```

ECS automatically discovers the dynamically assigned host port (or ENI port in `awsvpc`) and registers it in the target group.

---

### 🛑 Important Notes

* You **must** use `hostPort: 0` in task definition.
* For **Fargate**, dynamic port mapping always happens because ENIs are assigned per task.
* For EC2 launch type:

  * You can use `bridge` or `awsvpc` mode.
  * In `bridge` mode, ECS maps container port to a dynamic host port.
  * In `awsvpc`, container IP = host IP, so port conflicts are handled at ENI level.

---

### ✅ Use Cases

* Multiple tasks on the same host using the same container port.
* Microservices exposed via ALB.
* Scalable services without worrying about host port assignments.

---

Let me know if you want a **Terraform**, **CloudFormation**, or **AWS Console** setup example for this.
