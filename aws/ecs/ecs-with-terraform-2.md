Here's a complete Terraform example to **create an AWS ECS cluster** backed by **2 EC2 instances** using the **EC2 launch type** (not Fargate).

---

### ✅ Prerequisites:

* VPC and subnets (this code creates them).
* Terraform AWS provider configured.

---

### 📁 `main.tf`

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "ecs_vpc" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = "ecs-vpc"
  }
}

resource "aws_subnet" "ecs_subnet" {
  vpc_id                  = aws_vpc.ecs_vpc.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "us-east-1a"
  map_public_ip_on_launch = true

  tags = {
    Name = "ecs-subnet"
  }
}

resource "aws_internet_gateway" "ecs_igw" {
  vpc_id = aws_vpc.ecs_vpc.id

  tags = {
    Name = "ecs-igw"
  }
}

resource "aws_route_table" "ecs_rt" {
  vpc_id = aws_vpc.ecs_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.ecs_igw.id
  }

  tags = {
    Name = "ecs-rt"
  }
}

resource "aws_route_table_association" "ecs_rta" {
  subnet_id      = aws_subnet.ecs_subnet.id
  route_table_id = aws_route_table.ecs_rt.id
}

resource "aws_security_group" "ecs_sg" {
  name        = "ecs-sg"
  description = "Allow traffic for ECS cluster"
  vpc_id      = aws_vpc.ecs_vpc.id

  ingress {
    from_port   = 0
    to_port     = 65535
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
    Name = "ecs-sg"
  }
}

resource "aws_key_pair" "ecs_key" {
  key_name   = "ecs-key"
  public_key = file("~/.ssh/id_rsa.pub")  # Make sure you have a key pair here
}

resource "aws_launch_template" "ecs_lt" {
  name_prefix   = "ecs-ec2-lt-"
  image_id      = data.aws_ami.ecs_ami.id
  instance_type = "t3.micro"
  key_name      = aws_key_pair.ecs_key.key_name

  vpc_security_group_ids = [aws_security_group.ecs_sg.id]

  user_data = base64encode(<<EOF
#!/bin/bash
echo ECS_CLUSTER=${aws_ecs_cluster.ecs_cluster.name} >> /etc/ecs/ecs.config
EOF
  )

  tag_specifications {
    resource_type = "instance"
    tags = {
      Name = "ecs-instance"
    }
  }
}

data "aws_ami" "ecs_ami" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-ecs-hvm-*-x86_64-ebs"]
  }
}

resource "aws_autoscaling_group" "ecs_asg" {
  desired_capacity     = 2
  max_size             = 2
  min_size             = 2
  vpc_zone_identifier  = [aws_subnet.ecs_subnet.id]
  launch_template {
    id      = aws_launch_template.ecs_lt.id
    version = "$Latest"
  }

  tag {
    key                 = "Name"
    value               = "ecs-instance"
    propagate_at_launch = true
  }

  lifecycle {
    create_before_destroy = true
  }
}

resource "aws_ecs_cluster" "ecs_cluster" {
  name = "my-ecs-cluster"
}
```

---

### ✅ After creating this:

You’ll have:

* A VPC + subnet + IGW + route table
* An ECS cluster
* 2 EC2 instances launched in an ASG with ECS agent preconfigured
* Security group open to the world (update this for production)

---

### 🔧 To deploy:

```bash
terraform init
terraform apply
```

---

### 📦 Optional Next Steps:

* Add ECS Task Definitions
* Add ECS Services to run containers
* Add ALB for traffic

Would you like me to add an ECS service and task definition too?
