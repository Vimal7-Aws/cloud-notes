Integrating **AWS Application Load Balancer (ALB)** with **Amazon ECS** and **SSL certificates (certs)** from **AWS Certificate Manager (ACM)** is a common architecture for secure microservices deployment.

Here’s a step-by-step guide:

---

## ✅ Overview: ALB + ECS + ACM Integration

### 🧩 Components:

* **ALB**: Distributes HTTP/HTTPS traffic.
* **ECS (Fargate or EC2)**: Runs containers.
* **ACM**: Provides SSL/TLS certificates for HTTPS.
* **Target Group**: Routes traffic from ALB to ECS services.

---

## 🛠️ Step-by-Step Integration Guide

### 1. **Request a Certificate in ACM**

* Navigate to **ACM → Request a public certificate**.
* Provide domain name (e.g., `app.example.com`).
* Choose validation method (DNS preferred).
* Once validated, **note the ARN** of the certificate.

---

### 2. **Create an ALB (Application Load Balancer)**

* Go to **EC2 → Load Balancers → Create ALB**.
* Choose **internet-facing** (for public access).
* Set listener:

  * **HTTP (port 80)** and/or
  * **HTTPS (port 443)** → attach ACM certificate.
* Add at least 2 subnets in **different Availability Zones**.
* Configure security groups (allow inbound 80/443).

---

### 3. **Create Target Group for ECS**

* Type: **IP** (recommended for ECS with Fargate) or **Instance** (ECS on EC2).
* Protocol: **HTTP**
* Register targets: leave empty (ECS will auto-register via service).

---

### 4. **Create an ECS Service**

* Cluster: Choose existing or create new ECS cluster.
* Launch type: **Fargate** or **EC2**
* Define Task Definition (CPU/memory/containers)
* In the **Load balancing section**:

  * Type: **Application Load Balancer**
  * Choose previously created ALB
  * Listener: Select **HTTPS:443**
  * Choose the target group
* Enable **Service Discovery (optional)** if using private DNS.

---

### 5. **Configure Listener Rules on ALB**

* For HTTPS listener:

  * Add a **rule** to forward traffic to your ECS service based on path or host (e.g., `/api` or `api.example.com`).

---

### 6. **Test Secure Access**

* Visit `https://<your-domain>` and validate:

  * The certificate is working (HTTPS).
  * ALB routes to the correct ECS service.
  * Container responds correctly.

---

## 🧪 Example Terraform Snippets

### **Attach ACM Cert to ALB Listener**

```hcl
resource "aws_lb_listener" "https" {
  load_balancer_arn = aws_lb.main.arn
  port              = 443
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-2016-08"
  certificate_arn   = aws_acm_certificate.cert.arn

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.ecs_tg.arn
  }
}
```

---

## 📌 Tips

| Tip                                         | Notes                                                 |
| ------------------------------------------- | ----------------------------------------------------- |
| ACM certs must be in **same region** as ALB | Otherwise, they won’t be attachable                   |
| Use **path- or host-based routing**         | To separate traffic to multiple ECS services          |
| Keep **security groups open for ALB ↔ ECS** | Ensure ECS service allows inbound from ALB            |
| Consider using **Route 53**                 | For DNS routing to the ALB with friendly domain names |

---

Here is a **complete Terraform setup in Markdown format** that includes:

* VPC, subnets, and networking
* ACM TLS certificate
* Route 53 DNS configuration
* Application Load Balancer (ALB)
* ECS Fargate service with HTTPS
* Secure DNS routing via custom domain

---

# 🚀 Terraform: Route53 + ALB + ECS + ACM

### ✅ Assumptions:

* You already own the domain (e.g., `example.com`)
* Domain is hosted in Route 53
* TLS cert is requested via ACM

---

## 🧩 Provider

```hcl
provider "aws" {
  region = "us-east-1"
}
```

---

## 🌐 Networking: VPC + Subnets

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public_a" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
}

resource "aws_subnet" "public_b" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "us-east-1b"
}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id
}

resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
}

resource "aws_route_table_association" "a" {
  subnet_id      = aws_subnet.public_a.id
  route_table_id = aws_route_table.public.id
}

resource "aws_route_table_association" "b" {
  subnet_id      = aws_subnet.public_b.id
  route_table_id = aws_route_table.public.id
}
```

---

## 🔐 ACM Certificate

```hcl
resource "aws_acm_certificate" "cert" {
  domain_name       = "app.example.com"
  validation_method = "DNS"
}
```

---

## 🌍 Route 53 DNS Validation

```hcl
resource "aws_route53_zone" "main" {
  name = "example.com"
}

resource "aws_route53_record" "cert_validation" {
  name    = aws_acm_certificate.cert.domain_validation_options[0].resource_record_name
  type    = aws_acm_certificate.cert.domain_validation_options[0].resource_record_type
  zone_id = aws_route53_zone.main.zone_id
  records = [aws_acm_certificate.cert.domain_validation_options[0].resource_record_value]
  ttl     = 300
}

resource "aws_acm_certificate_validation" "cert" {
  certificate_arn         = aws_acm_certificate.cert.arn
  validation_record_fqdns = [aws_route53_record.cert_validation.fqdn]
}
```

---

## 🔐 Security Groups

```hcl
resource "aws_security_group" "alb_sg" {
  vpc_id = aws_vpc.main.id

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_security_group" "ecs_sg" {
  vpc_id = aws_vpc.main.id

  ingress {
    from_port       = 80
    to_port         = 80
    protocol        = "tcp"
    security_groups = [aws_security_group.alb_sg.id]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

---

## 🚢 ECS Cluster + Service

```hcl
resource "aws_ecs_cluster" "main" {
  name = "ecs-cluster"
}

resource "aws_ecs_task_definition" "web" {
  family                   = "web-task"
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]
  cpu                      = "256"
  memory                   = "512"

  container_definitions = jsonencode([
    {
      name  = "web"
      image = "nginx"
      portMappings = [{
        containerPort = 80
        protocol      = "tcp"
      }]
    }
  ])
}

resource "aws_ecs_service" "web" {
  name            = "web-service"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.web.arn
  desired_count   = 1
  launch_type     = "FARGATE"

  network_configuration {
    subnets         = [aws_subnet.public_a.id, aws_subnet.public_b.id]
    security_groups = [aws_security_group.ecs_sg.id]
    assign_public_ip = true
  }

  load_balancer {
    target_group_arn = aws_lb_target_group.ecs_tg.arn
    container_name   = "web"
    container_port   = 80
  }

  depends_on = [aws_lb_listener.https]
}
```

---

## ⚖️ ALB + HTTPS Listener

```hcl
resource "aws_lb" "alb" {
  name               = "ecs-alb"
  internal           = false
  load_balancer_type = "application"
  subnets            = [aws_subnet.public_a.id, aws_subnet.public_b.id]
  security_groups    = [aws_security_group.alb_sg.id]
}

resource "aws_lb_target_group" "ecs_tg" {
  name        = "ecs-target-group"
  port        = 80
  protocol    = "HTTP"
  target_type = "ip"
  vpc_id      = aws_vpc.main.id

  health_check {
    path = "/"
  }
}

resource "aws_lb_listener" "https" {
  load_balancer_arn = aws_lb.alb.arn
  port              = 443
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-2016-08"
  certificate_arn   = aws_acm_certificate_validation.cert.certificate_arn

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.ecs_tg.arn
  }
}
```

---

## 🌐 Route53 Record for ALB DNS

```hcl
resource "aws_route53_record" "alb_dns" {
  zone_id = aws_route53_zone.main.zone_id
  name    = "app.example.com"
  type    = "A"

  alias {
    name                   = aws_lb.alb.dns_name
    zone_id                = aws_lb.alb.zone_id
    evaluate_target_health = true
  }
}
```

---

## ✅ Output Example

```hcl
output "alb_dns_name" {
  value = aws_lb.alb.dns_name
}
```

---

Would you like:

* 🔐 WAF or CloudFront on top?
* 🧪 CI/CD with GitHub Actions or CodePipeline?
* 📈 CloudWatch monitoring and logging?

Let me know how you'd like to extend this!

