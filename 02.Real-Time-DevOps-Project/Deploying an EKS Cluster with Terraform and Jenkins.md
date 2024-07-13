# Deploying an EKS Cluster with Terraform and Jenkins

pre-requisit

Terraform
kubernet
AWS account
Teraform installation
Accees Keys


action plan:
create an EC2 instansl + jenkins
write tf code- KES cluster
push the codde on github
create a jenkins like - KES cluster
deplpou the chances to aws
implement a deployment file . kubctl > nginex accessibility

* configure the AWS CLI

*To Verify
DNS Configuration: Verify that your DNS configuration is correct and that your machine can resolve the sts.us-east-1.amazonaws.com hostname. You can test this by running:
```sh
nslookup sts.us-east-1.amazonaws.com
```
AWS CLI Configuration: Make sure your AWS CLI is configured correctly and can access the AWS services. You can test this by running:
```sh
aws sts get-caller-identity
```
- will create a S3 bucket in advance
```mrsinghbucket080320222```

will create a tf files as below
- backend.tf
- data.tf
- main.tf
- provider.tf
- terraform.tfvars
- variables.tf

* in ```backend.tf``` file, value would be:
```sh
terraform {
  backend "s3" {
    bucket = "mrsinghbucket080320222"
    key    = "jenkins/terraform.tfstate"
    region = "us-east-1"
  }
}
```
* in ```data.tf``` file, value would be:
```sh
data "aws_ami" "example" {
    most_recent      = true
  
  owners           = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-kernel-*-hvm-*-x86_64-gp2"]
  }

  filter {
    name   = "root-device-type"
    values = ["ebs"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}

# Declare the aws_availability_zones data source
data "aws_availability_zones" "azs" {
  state = "available"
}
```
* in ```main.tf``` file, value would be:
```sh
# To Create a VPC

module "vpc" {
  source = "terraform-aws-modules/vpc/aws"

  name = "jenkins-vpc"
  cidr = var.vpc_cidr

  # Reference the data source in your module
  azs = data.aws_availability_zones.azs.names

  #private_subnets = var.private_subnets
  public_subnets = var.public_subnets

  enable_dns_hostnames = true
  #enable_nat_gateway   = true
  #single_nat_gateway   = true
  tags = {
    Name        = "jenkins-vpc"
    Terraform   = "true"
    Environment = "dev"
  }
}
```
* in ```provide.tf``` file, value would be:
```sh
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

* in ```terraform.tfvar``` file, value would be:
```sh
vpc_cidr       = "10.0.0.0/16"
public_subnets = ["10.0.1.0/24"]
#private_subnets = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
#public_subnets  = ["10.0.4.0/24", "10.0.5.0/24", "10.0.6.0/24"]
```
* in ```variable.tfvar``` file, value would be:
```sh
variable "vpc_cidr" {
  description = "VPC CIDR"
  type        = string
}

variable "public_subnets" {
  description = "Subnets CIDR"
  type        = list(string)
}
```
* will run the following __Command__ to validate and create it.
```sh
terraform init
terraform fmt  
terraform validate
terraform plan
terraform apply
```

# To search in google 
- aws ami data source terraform
- terraform-aws-modules/security-group/aws
- terraform ec2 module
- terraform eks module
- terraform eks module

If you want to destroy and recreate the EC2 instance, you should first use the destroy command with the -target flag:

```sh
terraform destroy -target=module.ec2_instance
```
After destroying the EC2 instance, you can then run the apply command to recreate it:
```sh
terraform apply -target=module.ec2_instance
```
You can find the exact resource address by running:
```sh
terraform state list
```

terraform destroy -target=module.module_name.resource_type.resource_name
Replace module_name, resource_type, and resource_name with your actual module name and resource details. For example, if your module is named ec2_instance and you want to destroy an EC2 instance resource within it, you might use:

bash
Copy code
terraform destroy -target=module.ec2_instance.aws_instance.this[0]