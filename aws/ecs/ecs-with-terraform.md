Great! Here's a complete **Terraform configuration** to deploy a **Fargate-based ECS service** behind an **Application Load Balancer (ALB)**. This example includes:

* ECS cluster
* Task definition
* ECS service
* ALB + target group + listener
* VPC + subnets + security groups (basic)
* IAM roles

---

## 🧾 Assumptions

* You already have a Docker image pushed to **ECR** (e.g., `123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp:latest`)
* Region: `us-east-1`
* Basic web app listening on port `80`

---

## 📂 Directory Structure

```
ecs-fargate-app/
├── main.tf
├── variables.tf
└── outputs.tf
```

---

### 📄 `main.tf`

```hcl
provider "aws" {
  region = "us-east-1"
}

########################
# Networking Resources #
########################

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public_a" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
  map_public_ip_on_launch = true
}

resource "aws_subnet" "public_b" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "us-east-1b"
  map_public_ip_on_launch = true
}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id
}

resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id
}

resource "aws_route" "default_route" {
  route_table_id         = aws_route_table.public.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id             = aws_internet_gateway.igw.id
}

resource "aws_route_table_association" "a" {
  subnet_id      = aws_subnet.public_a.id
  route_table_id = aws_route_table.public.id
}

resource "aws_route_table_association" "b" {
  subnet_id      = aws_subnet.public_b.id
  route_table_id = aws_route_table.public.id
}

#####################
# Security Groups   #
#####################

resource "aws_security_group" "alb_sg" {
  vpc_id = aws_vpc.main.id

  ingress {
    from_port   = 80
    to_port     = 80
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

##################
# IAM Roles      #
##################

resource "aws_iam_role" "ecs_task_execution_role" {
  name = "ecsTaskExecutionRole"

  assume_role_policy = jsonencode({
    Version = "2012-10-17",
    Statement = [{
      Effect = "Allow",
      Principal = {
        Service = "ecs-tasks.amazonaws.com"
      },
      Action = "sts:AssumeRole"
    }]
  })
}

resource "aws_iam_role_policy_attachment" "ecs_execution_policy" {
  role       = aws_iam_role.ecs_task_execution_role.name
  policy_arn = "arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy"
}

##########################
# ECS Cluster & Task Def #
##########################

resource "aws_ecs_cluster" "this" {
  name = "my-fargate-cluster"
}

resource "aws_ecs_task_definition" "app" {
  family                   = "myapp-task"
  requires_compatibilities = ["FARGATE"]
  network_mode             = "awsvpc"
  cpu                      = "256"
  memory                   = "512"
  execution_role_arn       = aws_iam_role.ecs_task_execution_role.arn

  container_definitions = jsonencode([{
    name      = "myapp"
    image     = var.app_image
    portMappings = [{
      containerPort = 80
      protocol      = "tcp"
    }]
    essential = true
    logConfiguration = {
      logDriver = "awslogs",
      options = {
        awslogs-group         = "/ecs/myapp"
        awslogs-region        = "us-east-1"
        awslogs-stream-prefix = "ecs"
      }
    }
  }])
}

resource "aws_cloudwatch_log_group" "ecs_logs" {
  name              = "/ecs/myapp"
  retention_in_days = 7
}

#################
# ALB Setup     #
#################

resource "aws_lb" "app_alb" {
  name               = "app-alb"
  internal           = false
  load_balancer_type = "application"
  subnets            = [aws_subnet.public_a.id, aws_subnet.public_b.id]
  security_groups    = [aws_security_group.alb_sg.id]
}

resource "aws_lb_target_group" "app_tg" {
  name     = "app-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id

  health_check {
    path = "/"
  }
}

resource "aws_lb_listener" "http" {
  load_balancer_arn = aws_lb.app_alb.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.app_tg.arn
  }
}

####################
# ECS Service      #
####################

resource "aws_ecs_service" "app" {
  name            = "myapp-service"
  cluster         = aws_ecs_cluster.this.id
  task_definition = aws_ecs_task_definition.app.arn
  launch_type     = "FARGATE"
  desired_count   = 2

  network_configuration {
    subnets         = [aws_subnet.public_a.id, aws_subnet.public_b.id]
    security_groups = [aws_security_group.ecs_sg.id]
    assign_public_ip = true
  }

  load_balancer {
    target_group_arn = aws_lb_target_group.app_tg.arn
    container_name   = "myapp"
    container_port   = 80
  }

  depends_on = [
    aws_lb_listener.http
  ]
}
```

---

### 📄 `variables.tf`

```hcl
variable "app_image" {
  description = "Docker image for the app"
  type        = string
  default     = "123456789012.dkr.ecr.us-east-1.amazonaws.com/myapp:latest"
}
```

---

### 📄 `outputs.tf`

```hcl
output "load_balancer_dns" {
  value = aws_lb.app_alb.dns_name
}
```

---

## 🚀 How to Deploy

```bash
terraform init
terraform apply
```

---

## 🌐 Accessing the App

After `terraform apply` completes, you’ll see the **DNS of the ALB** in output. Visit:

```
http://<load_balancer_dns>
```

---

Would you like to extend this with:

* Auto scaling?
* HTTPS (ACM + HTTPS listener)?
* Private subnets?
* ECS Service Discovery?

Let me know!
