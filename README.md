## 🧾 What is EC2?

**Amazon EC2 (Elastic Compute Cloud)** is a service that allows you to run virtual servers (called instances) on the AWS cloud. You can choose an OS, instance type (CPU/memory), storage, security settings, and more.


## 📁 EC2 Launch Using Terraform (Infrastructure as Code)

Terraform is an open-source tool by HashiCorp that allows you to define infrastructure in simple, human-readable configuration files and then provision it automatically.

### 🧩 Step-by-Step with Terraform

### Step 1: Create the following 4 Terraform files in a folder (`ec2-launch/`)


### `main.tf` – This defines the AWS provider, EC2 instance, and security group

```hcl
provider "aws" {
  region = var.region
}

resource "aws_security_group" "allow_ssh" {
  name        = "allow_ssh"
  description = "Allow SSH access"

  ingress {
    description = "SSH access"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]  # Open to the world (for demo purposes)
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "my_ec2" {
  ami                    = var.ami
  instance_type          = var.instance_type
  key_name               = var.key_name
  vpc_security_group_ids = [aws_security_group.allow_ssh.id]

  tags = {
    Name = "MyEC2Instance"
  }
}
```


### `variables.tf` – This declares the input variables you will set later

```hcl
variable "region" {}
variable "ami" {}
variable "instance_type" {}
variable "key_name" {}
```


### `terraform.tfvars` – This assigns actual values to the variables

```hcl
region         = "us-east-1"
ami            = "ami-0c02fb55956c7d316" # Amazon Linux 2 AMI (Free Tier eligible)
instance_type  = "t2.micro"
key_name       = "my-key-pair"           # This must exist in your AWS account
```


### `outputs.tf` – This will print the public IP of your instance after creation

```hcl
output "public_ip" {
  value = aws_instance.my_ec2.public_ip
}
```


### Step 2: Run Terraform Commands

Open a terminal inside the folder and run:

```bash
terraform init          # Initializes Terraform
terraform plan          # Shows what Terraform will do
terraform apply         # Creates the resources
```

Add `-auto-approve` to skip confirmation prompt.

✅ You will get the **public IP** of the EC2 instance on successful creation.



### Step 3: Connect to EC2

```bash
ssh -i my-key-pair.pem ec2-user@<public_ip>
```


### Step 4: Clean up

```bash
terraform destroy -auto-approve
```


## 🛡️ Important Notes

- Make sure you have an existing **key pair** in your AWS account.
- Always destroy unused resources to avoid charges.

