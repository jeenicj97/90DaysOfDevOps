# Day 61 -- Introduction to Terraform and Your First AWS Infrastructure

---

## Challenge Tasks

### Task 1: Understand Infrastructure as Code
Before touching the terminal, research and write short notes on:

1. What is Infrastructure as Code (IaC)? Why does it matter in DevOps?
   * Infrastructure as Code (IaC) means creating and managing infrastructure (like servers, databases, and networks) using configuration files instead of manually creating resources through a cloud console.  
2. What problems does IaC solve compared to manually creating resources in the AWS console?
   * When infrastructure is created manually through AWS Console, it is easy to make mistakes or forget which settings were used. IaC solves this by defining the infrastructure in code.

| Without IaC (ClickOps) | With IaC |
|---|---|
| Manual steps, easy to make mistakes | Automated, consistent every time |
| Hard to repeat the same setup | Run the same code anywhere |
| No record of what changed | Git tracks every change |
| Only one person knows how it was set up | Anyone can read the code |

3. How is Terraform different from AWS CloudFormation, Ansible, and Pulumi?  
   * vs. AWS CloudFormation: CloudFormation is Amazon’s native tool. It is great if you use only AWS, but it only works for AWS. Terraform is multi-cloud, allowing you to manage AWS, Azure, and others simultaneously.  
   * vs. Ansible: Ansible is primarily a Configuration Management tool. It is great for installing software, updates, and configuring settings inside an already running server. Terraform is a Provisioning tool, it creates the actual servers and networks from scratch. (Teams often use Terraform to build the servers, and Ansible to configure them).
   * vs. Pulumi: Terraform uses its own custom language called HCL (HashiCorp Configuration Language). Pulumi lets you write IaC using standard programming languages like Python, TypeScript, or Go.  
4. What does it mean that Terraform is "declarative" and "cloud-agnostic"?
   * Declarative: You tell Terraform what the final result should look like (ex. "I want 3 servers and 1 database") and Terraform automatically figures out the exact steps needed to build or update it.  
   * Cloud-agnostic: You can use the exact same workflow and language to provision resources on AWS, Google Cloud, Microsoft Azure, or even GitHub.

---

### Task 2: Install Terraform and Configure AWS
1. Install Terraform:
```bash
# macOS
brew tap hashicorp/tap
brew install hashicorp/tap/terraform

# Linux (amd64)
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

# Windows
choco install terraform
```

2. Verify:
```bash
terraform -version
```

3. Install and configure the AWS CLI:
```bash
aws configure
# Enter your Access Key ID, Secret Access Key, default region (e.g., ap-south-1), output format (json)
```

4. Verify AWS access:
```bash
aws sts get-caller-identity
```

You should see your AWS account ID and ARN.

![Image Alt]()

---

### Task 3: Your First Terraform Config -- Create an S3 Bucket
Create a project directory and write your first Terraform config:

```bash
mkdir terraform-basics && cd terraform-basics
```

Create a file called `main.tf` with:
1. A `terraform` block with `required_providers` specifying the `aws` provider
2. A `provider "aws"` block with your region
3. A `resource "aws_s3_bucket"` that creates a bucket with a globally unique name

Run the Terraform lifecycle:
```bash
terraform init      # Download the AWS provider
terraform plan      # Preview what will be created
terraform apply     # Create the bucket (type 'yes' to confirm)
```

Go to the AWS S3 console and verify your bucket exists.

![Image Alt]()


**Document:** What did `terraform init` download? What does the `.terraform/` directory contain?  
  >  `terraform init`: downloads the required Terraform provider plugins defined in the required_providers block.  
  > `.terraform/` directory: It contains Terraform's local working information, including downloaded provider plugins and other initialization-related data.

---

### Task 4: Add an EC2 Instance
In the same `main.tf`, add:
1. A `resource "aws_instance"` using AMI `ami-0f5ee92e2d63afc18` (Amazon Linux 2 in ap-south-1 -- use the correct AMI for your region)
2. Set instance type to `t2.micro`
3. Add a tag: `Name = "TerraWeek-Day1"`

Run:
```bash
terraform plan      # You should see 1 resource to add (bucket already exists)
terraform apply
```


```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }

  }
}

provider "aws" {
  region = "ap-south-1"
}

resource "aws_s3_bucket" "terraform_bucket" {

  bucket = "terrform-jeeni-2026"
}

resource "aws_instance" "terraform_ec2" {

  ami           = "ami-0f5ee92e2d63afc18"
  instance_type = "t3.micro"

  tags = {
    Name = "Terraweek-Modified"

  }
}

```

Go to the AWS EC2 console and verify your instance is running with the correct name tag.

![Image Alt]()

**Document:** How does Terraform know the S3 bucket already exists and only the EC2 instance needs to be created?

Terraform keeps track of resources it manages in the Terraform state file: `terraform.tfstate`  

After creating the S3 bucket, Terraform records information about it in the state.  

When we run:

```bash
terraform plan
```

Terraform compares:

```text
main.tf
   ↓
terraform.tfstate
   ↓
AWS infrastructure
```

It determines:

> "The S3 bucket already exists and matches the configuration, so I only need to create the new EC2 instance."

This is one of the most important concepts in Terraform.  



---

### Task 5: Understand the State File
Terraform tracks everything it creates in a state file. Time to inspect it.

1. Open `terraform.tfstate` in your editor -- read the JSON structure
2. Run these commands and document what each returns:
```bash
terraform show                          # Human-readable view of current state
terraform state list                    # List all resources Terraform manages
terraform state show aws_s3_bucket.<name>   # Detailed view of a specific resource
terraform state show aws_instance.<name>
```


![Image Alt]()


![Image Alt]()


3. Answer these questions in your notes:
   - What information does the state file store about each resource?  
     > The state file stores Terraform's information about the resources it manages. This can include: Resource IDs, Resource attributes, Provider information, Resource relationships, Configuration-related metadata, Values returned by the cloud provider
   - Why should you never manually edit the state file?
     > The state file is managed by Terraform. Manually changing it can cause the state to become inconsistent with the real infrastructure.
   - Why should the state file not be committed to Git?
     > here are two major reasons.  
     > 1. It can contain sensitive information: Depending on the resources, Terraform state can contain sensitive values.  
     > 2. It creates state-management problems: If multiple developers use the same repository and each person has their own local state file, the states can become inconsistent.

---

### Task 6: Modify, Plan, and Destroy
1. Change the EC2 instance tag from `"TerraWeek-Day1"` to `"TerraWeek-Modified"` in your `main.tf`
2. Run `terraform plan` and read the output carefully:
   - What do the `~`, `+`, and `-` symbols mean?
     > `~` : Modify - means Terraform will modify an existing resource  
     > `+` : Create - means Terraform will create a new resource  
     > `-` : Destroy - means Terraform will destroy/remove a resource  
   - Is this an in-place update or a destroy-and-recreate?
     > For this tag change: TerraWeek-Day1 -> TerraWeek-Day1, Terraform should perform an: *In-place update*
     > The EC2 instance itself doesn't need to be destroyed and recreated just because its Name tag changed.

3. Apply the change
4. Verify the tag changed in the AWS console

![Image Alt]()

5. Finally, destroy everything:
```bash
terraform destroy
```


![Image Alt]()

6. Verify in the AWS console -- both the S3 bucket and EC2 instance should be gone
   > yes, EC2 instance terminated and S3 bucket deleted from console

---
