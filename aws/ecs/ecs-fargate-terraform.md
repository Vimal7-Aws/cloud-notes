Here's a full **Terraform configuration** to provision an **ECS cluster using Fargate launch type**, including:

* ECS cluster
* Task definition (Fargate-compatible)
* ECS service (running on Fargate)
* Networking (VPC, subnets, SG, etc.)
* Sample container (e.g., Nginx)

---

## ✅ ECS Fargate Cluster with Terraform

### 📁 `main.tf`

```hcl
provider "aws" {
  region = "us-east-1"
}

#####################
# Networking (VPC)
#####################
resource "aws_vpc" "ecs_vpc" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "ecs-fargate-vpc"
  }
}

resource "aws_subnet" "public_subnet" {
  count                   = 2
  cidr_block              = cidrsubnet(aws_vpc.ecs_vpc.cidr_block, 8, count.index)
  vpc_id                  = aws_vpc.ecs_vpc.id
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]

  tags = {
    Name = "public-subnet-${count.index}"
  }
}

data "aws_availability_zones" "available" {}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.ecs_vpc.id
}

resource "aws_route_table" "rt" {
  vpc_id = aws_vpc.ecs_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
}

resource "aws_route_table_association" "rta" {
  count          = 2
  subnet_id      = aws_subnet.public_subnet[count.index].id
  route_table_id = aws_route_table.rt.id
}

resource "aws_security_group" "ecs_sg" {
  name        = "ecs-fargate-sg"
  description = "Allow HTTP"
  vpc_id      = aws_vpc.ecs_vpc.id

  ingress {
    description = "HTTP from anywhere"
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

  tags = {
    Name = "ecs-fargate-sg"
  }
}

#########################
# ECS Cluster & Service
#########################
resource "aws_ecs_cluster" "fargate_cluster" {
  name = "fargate-cluster"
}

resource "aws_ecs_task_definition" "nginx" {
  family                   = "nginx-fargate-task"
  requires_compatibilities = ["FARGATE"]
  network_mode             = "awsvpc"
  cpu                      = "256"
  memory                   = "512"
  execution_role_arn       = aws_iam_role.ecs_task_execution_role.arn

  container_definitions = jsonencode([
    {
      name      = "nginx"
      image     = "nginx:latest"
      essential = true
      portMappings = [
        {
          containerPort = 80
          hostPort      = 80
        }
      ]
    }
  ])
}

resource "aws_ecs_service" "nginx_service" {
  name            = "nginx-service"
  cluster         = aws_ecs_cluster.fargate_cluster.id
  launch_type     = "FARGATE"
  desired_count   = 2
  task_definition = aws_ecs_task_definition.nginx.arn

  network_configuration {
    subnets         = aws_subnet.public_subnet[*].id
    security_groups = [aws_security_group.ecs_sg.id]
    assign_public_ip = true
  }

  lifecycle {
    ignore_changes = [task_definition]
  }
}

#########################
# IAM for ECS Fargate
#########################
resource "aws_iam_role" "ecs_task_execution_role" {
  name = "ecsTaskExecutionRole"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Principal = {
          Service = "ecs-tasks.amazonaws.com"
        }
        Effect = "Allow"
        Sid    = ""
      }
    ]
  })
}

resource "aws_iam_role_policy_attachment" "ecs_execution_attach" {
  role       = aws_iam_role.ecs_task_execution_role.name
  policy_arn = "arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy"
}
```

---

### ✅ To Deploy

```bash
terraform init
terraform apply
```

---

### 📦 What You Get

* A VPC with 2 public subnets
* ECS cluster with Fargate
* NGINX service running on 2 Fargate tasks
* Public IPs so you can access NGINX in your browser

---

Would you like to add:

* **Load Balancer (ALB)** for stable endpoint?
* **Private subnets** with NAT Gateway (more secure)?
* Or **EFS storage** for persistent volumes?

Let me know and I’ll build the next step.
