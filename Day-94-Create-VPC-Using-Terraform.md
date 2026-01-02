# Day 94: Create VPC Using Terraform

## Challenge Description
The Nautilus DevOps team is migrating infrastructure to AWS and needs to create a VPC named `devops-vpc` in region `us-east-1` with an IPv4 CIDR block using Terraform.

## Key Concepts
*   **Terraform:** Infrastructure as Code (IaC) tool used to define and provision infrastructure.
*   **VPC (Virtual Private Cloud):** A logically isolated section of the AWS Cloud where you can launch AWS resources.
*   **Provider:** A plugin in Terraform that allows interaction with cloud providers and other APIs (AWS in this case).
*   **Resource:** The basic building block in Terraform (e.g., `aws_vpc`), describing a specific piece of infrastructure.

## Solution
1.  We navigate to the Terraform working directory:
    ```bash
    cd /home/bob/terraform
    ```

2.  We create the `main.tf` file to define the VPC resource. Since the `provider.tf` file was already provided with the LocalStack configuration, we only needed to add the resource definition:

    ```hcl
    resource "aws_vpc" "devops-vpc" {
      cidr_block       = "10.0.0.0/16"
      instance_tenancy = "default"

      tags = {
        Name = "devops-vpc"
      }
    }
    ```

3.  We initialize Terraform to download the necessary provider plugins:
    ```bash
    terraform init
    ```

4.  We generate an execution plan to verify the changes:
    ```bash
    terraform plan
    ```

5.  We apply the configuration to create the VPC:
    ```bash
    terraform apply
    ```

## Verification
1.  We can verify the resource creation by listing the state:
    ```bash
    terraform show
    ```
2.  Alternatively, we can check the VPC list using the AWS CLI configured for LocalStack:
    ```bash
    aws --endpoint-url=http://localhost:4566 ec2 describe-vpcs
    ```
