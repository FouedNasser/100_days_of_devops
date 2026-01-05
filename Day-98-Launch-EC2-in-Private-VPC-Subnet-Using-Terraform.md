# Day 98: Launch EC2 in Private VPC Subnet Using Terraform

## Challenge Description
The Nautilus DevOps team is expanding their AWS infrastructure and requires the setup of a private Virtual Private Cloud (VPC) along with a subnet. This configuration ensures that resources remain isolated and can only communicate within the VPC. We need to:
*   Create a VPC named `devops-priv-vpc` with CIDR `10.0.0.0/16`.
*   Create a subnet named `devops-priv-subnet` with CIDR `10.0.1.0/24` and auto-assign IP disabled.
*   Launch a `t2.micro` EC2 instance named `devops-priv-ec2` in this subnet.
*   Configure a Security Group that allows access only from within the VPC's CIDR block.
*   Use `variables.tf` and `outputs.tf` for configuration management.

## Key Concepts
*   **VPC Isolation:** Creating a private network space in AWS.
*   **Subnet Configuration:** Defining network segments and controlling public IP assignment.
*   **Terraform Variables:** Using `variables.tf` to parameterize CIDR blocks (`KKE_VPC_CIDR`, `KKE_SUBNET_CIDR`).
*   **Terraform Outputs:** Using `outputs.tf` to export resource names (`KKE_vpc_name`, `KKE_subnet_name`, `KKE_ec2_private`).
*   **Security Groups:** Implementing ingress rules based on internal CIDR blocks for restricted access.

## Solution
1.  We navigated to the Terraform working directory:
    ```bash
    cd /home/bob/terraform
    ```

2.  We created the `variables.tf` file to define the CIDR blocks:
    ```hcl
    variable "KKE_VPC_CIDR" {
      description = "CIDR block for the VPC"
      type        = string
      default     = "10.0.0.0/16"
    }

    variable "KKE_SUBNET_CIDR" {
      description = "CIDR block for the Subnet"
      type        = string
      default     = "10.0.1.0/24"
    }
    ```

3.  We created the `outputs.tf` file to define the required output names:
    ```hcl
    output "KKE_vpc_name" {
      value = aws_vpc.devops_priv_vpc.tags.Name
    }

    output "KKE_subnet_name" {
      value = aws_subnet.devops_priv_subnet.tags.Name
    }

    output "KKE_ec2_private" {
      value = aws_instance.devops_priv_ec2.tags.Name
    }
    ```

4.  We created the `main.tf` file to provision the infrastructure:
    ```hcl
    provider "aws" {
      region = "us-east-1"
    }

    # Create VPC
    resource "aws_vpc" "devops_priv_vpc" {
      cidr_block = var.KKE_VPC_CIDR
      tags = {
        Name = "devops-priv-vpc"
      }
    }

    # Create Subnet
    resource "aws_subnet" "devops_priv_subnet" {
      vpc_id                  = aws_vpc.devops_priv_vpc.id
      cidr_block              = var.KKE_SUBNET_CIDR
      map_public_ip_on_launch = false

      tags = {
        Name = "devops-priv-subnet"
      }
    }

    # Security Group
    resource "aws_security_group" "vpc_sg" {
      name        = "devops-priv-sg"
      vpc_id      = aws_vpc.devops_priv_vpc.id

      ingress {
        from_port   = 0
        to_port     = 0
        protocol    = "-1"
        cidr_blocks = [var.KKE_VPC_CIDR]
      }

      egress {
        from_port   = 0
        to_port     = 0
        protocol    = "-1"
        cidr_blocks = ["0.0.0.0/0"]
      }
    }

    # EC2 Instance
    resource "aws_instance" "devops_priv_ec2" {
      ami                    = "ami-0c101f26f147fa7fd"
      instance_type          = "t2.micro"
      subnet_id              = aws_subnet.devops_priv_subnet.id
      vpc_security_group_ids = [aws_security_group.vpc_sg.id]

      tags = {
        Name = "devops-priv-ec2"
      }
    }
    ```

5.  We initialized and applied the Terraform configuration:
    ```bash
    terraform init
    terraform apply -auto-approve
    ```

## Verification
1.  We verified that the infrastructure matches the configuration by running `terraform plan`, which returned `No changes`.
2.  We checked the outputs to confirm the resource names:
    ```bash
    terraform output
    ```
