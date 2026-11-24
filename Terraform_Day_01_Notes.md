# Terraform with AWS --- Day 01 Notes

## 1. Learning objectives

By the end of Day 1, understand:

-   What Terraform is and why it is used
-   Infrastructure as Code (IaC)
-   IaC versus configuration management
-   Declarative versus imperative approaches
-   Terraform architecture at a high level
-   Providers, resources, and HCL
-   Terraform installation
-   The basic Terraform workflow and commands

------------------------------------------------------------------------

## 2. What is Terraform?

**Simple definition:** Terraform is an Infrastructure as Code (IaC) tool
used to provision and manage infrastructure through configuration files.

**Interview definition:** Terraform is an open-source Infrastructure as
Code tool developed by HashiCorp. It allows teams to define, provision,
and manage infrastructure using declarative configuration files written
in HashiCorp Configuration Language (HCL).

Terraform can manage infrastructure and services through providers,
including:

-   AWS
-   Microsoft Azure
-   Google Cloud
-   Kubernetes
-   GitHub

### Why do we use Terraform?

Manually creating infrastructure through a cloud console can be
time-consuming and error-prone, especially when the same infrastructure
must be recreated across Development, QA, Staging, and Production.

Terraform helps teams:

-   Automate infrastructure provisioning
-   Create consistent and repeatable environments
-   Reuse infrastructure code
-   Track infrastructure changes in version control
-   Review planned changes before applying them
-   Collaborate on infrastructure management

------------------------------------------------------------------------

## 3. What is Infrastructure as Code (IaC)?

**Definition:** Infrastructure as Code means defining and managing
infrastructure through machine-readable configuration files instead of
creating and configuring everything manually.

### Manual approach

For example, to create an EC2 instance through the AWS Console, an
engineer may:

1.  Sign in to AWS.
2.  Open EC2.
3.  Select an Amazon Machine Image (AMI).
4.  Select an instance type.
5.  Configure networking and security groups.
6.  Launch the instance.

Repeating these steps for many resources and environments increases the
chance of mistakes.

### IaC approach

The engineer describes the required infrastructure in code. A tool such
as Terraform reads the configuration and communicates with the relevant
platform through a provider.

``` text
Terraform configuration
          |
          v
     Terraform CLI
          |
          v
      AWS provider
          |
          v
        AWS APIs
          |
          v
   AWS infrastructure
```

### Benefits of IaC

  -----------------------------------------------------------------------
  Benefit                             Explanation
  ----------------------------------- -----------------------------------
  Automation                          Reduces repetitive manual
                                      infrastructure work

  Consistency                         Helps create environments from
                                      consistent configurations

  Reusability                         Allows teams to reuse configuration
                                      and modules

  Version control                     Infrastructure changes can be
                                      tracked in Git

  Reviewability                       Changes can be reviewed before
                                      deployment

  Reproducibility                     Infrastructure can be recreated
                                      from code

  Collaboration                       Teams can work with shared,
                                      reviewed configurations
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 4. IaC versus configuration management

Infrastructure provisioning and configuration management are related but
different activities.

### Infrastructure provisioning

Creates and manages infrastructure, for example:

-   VPCs
-   Subnets
-   EC2 instances
-   Security groups
-   Load balancers
-   Managed databases

Terraform is commonly used for this work.

### Configuration management

Configures operating systems, servers, and software, for example:

-   Installing Java or Docker
-   Configuring Nginx
-   Creating users
-   Managing configuration files
-   Deploying application software

Ansible is commonly used for this work.

  -----------------------------------------------------------------------
  Terraform                           Ansible
  ----------------------------------- -----------------------------------
  Primarily provisions and manages    Commonly automates configuration
  infrastructure                      management and software tasks

  Can create EC2, VPC, S3, and other  Can install and configure software
  resources                           on servers

  Uses HCL for Terraform              Commonly uses YAML playbooks
  configuration                       

  Primarily declarative               Supports task-based procedural
                                      execution and declarative modules
  -----------------------------------------------------------------------

### Using Terraform and Ansible together

``` text
Terraform
    |
    v
Create VPC and EC2 instances
    |
    v
Ansible
    |
    v
Install Docker / Java / Nginx
    |
    v
Configure and deploy application
```

**Interview question: Can Terraform replace Ansible?**

**Answer:** Not completely. Terraform primarily provisions and manages
infrastructure, while Ansible is commonly used for configuration
management and application-related automation. They solve different
problems and can complement each other.

------------------------------------------------------------------------

## 5. Declarative versus imperative

### Declarative approach

You describe **what** you want the final result to look like. Terraform
determines the actions needed to reach that desired configuration.

Example:

``` hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"
}
```

This describes an EC2 instance configuration. The AMI shown is a
placeholder, not a usable AMI ID.

### Imperative approach

You describe **how** to perform an operation step by step.

For example:

1.  Sign in to AWS.
2.  Open EC2.
3.  Select an AMI.
4.  Select an instance type.
5.  Configure networking.
6.  Launch the instance.

  -----------------------------------------------------------------------
  Declarative                         Imperative
  ----------------------------------- -----------------------------------
  Describes the desired outcome       Describes the steps to perform

  Focuses on what is required         Focuses on how to do it

  Terraform configuration is          Shell scripts are a common example
  declarative                         
  -----------------------------------------------------------------------

**Interview answer:** Terraform follows a declarative approach. We
describe the desired infrastructure, and Terraform determines the
actions required to reach that configuration.

------------------------------------------------------------------------

## 6. Terraform architecture

At a high level, the main components are:

### Terraform configuration

The `.tf` files written by an engineer. They describe the desired
infrastructure and its configuration.

Common filenames include:

-   `main.tf`
-   `providers.tf`
-   `variables.tf`
-   `outputs.tf`

These are conventional filenames; Terraform does not require every
project to use this exact structure.

### Terraform Core

Terraform Core reads and evaluates configuration, processes
dependencies, works with state, and creates an execution plan.

### Provider

A provider is a plugin that allows Terraform to interact with an
external platform or service.

Examples:

-   AWS provider
-   Azure provider
-   Kubernetes provider
-   GitHub provider

### Infrastructure

The actual resources managed through Terraform, such as EC2 instances,
S3 buckets, VPCs, or Kubernetes resources.

``` text
       Terraform configuration
                  |
                  v
            Terraform Core
                  |
                  v
              Provider
                  |
                  v
         Platform APIs / services
                  |
                  v
       Infrastructure resources
```

**How does Terraform communicate with AWS?**

Terraform uses the AWS provider. The provider communicates with AWS APIs
using the configured AWS credentials and permissions to perform the
requested operations.

------------------------------------------------------------------------

## 7. Installing Terraform

Official installation documentation:

https://developer.hashicorp.com/terraform/install

### Ubuntu Linux

Install prerequisites:

``` bash
sudo apt-get update
sudo apt-get install -y wget gpg
```

Add the HashiCorp signing key:

``` bash
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
```

Add the HashiCorp repository:

``` bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(. /etc/os-release && echo $VERSION_CODENAME) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```

Install Terraform:

``` bash
sudo apt-get update
sudo apt-get install terraform
```

Verify the installation:

``` bash
terraform version
```

### Windows

Using Windows Package Manager:

``` powershell
winget install Hashicorp.Terraform
```

Verify:

``` powershell
terraform version
```

For other installation methods and macOS instructions, refer to the
official installation guide.

------------------------------------------------------------------------

## 8. Introduction to HCL

HCL stands for **HashiCorp Configuration Language**.

Terraform uses HCL to define infrastructure in `.tf` files.

Example:

``` hcl
resource "aws_instance" "web" {
  ami           = "ami-xxxxxxxx"
  instance_type = "t3.micro"
}
```

Breakdown:

-   `resource` --- declares a resource block.
-   `aws_instance` --- the resource type supplied by the AWS provider.
-   `web` --- the local name assigned to this resource in the
    configuration.
-   `ami` --- an argument specifying the Amazon Machine Image.
-   `instance_type` --- an argument specifying the EC2 instance type.

### HCL comments

``` hcl
# Single-line comment

// Single-line comment

/*
Multi-line
comment
*/
```

HCL is human-readable and supports blocks, arguments, expressions,
variables, and functions. These will be covered in later lessons.

------------------------------------------------------------------------

## 9. Basic Terraform commands

  -----------------------------------------------------------------------
  Command                             Purpose
  ----------------------------------- -----------------------------------
  `terraform version`                 Displays the installed Terraform
                                      version

  `terraform init`                    Initializes the working directory
                                      and downloads required providers
                                      and modules

  `terraform fmt`                     Formats Terraform configuration
                                      files

  `terraform validate`                Checks configuration syntax and
                                      internal consistency

  `terraform plan`                    Creates and displays a proposed
                                      execution plan

  `terraform apply`                   Applies the proposed changes to
                                      managed infrastructure

  `terraform destroy`                 Plans and applies the destruction
                                      of managed resources

  `terraform show`                    Displays the current state or the
                                      contents of a saved plan

  `terraform output`                  Displays root-module output values
  -----------------------------------------------------------------------

### The four fundamental commands

#### 1. `terraform init`

Initializes a Terraform working directory and downloads the required
providers and modules.

``` bash
terraform init
```

#### 2. `terraform plan`

Compares the configuration with the tracked state and refreshed
information from providers, then displays the proposed changes.

``` bash
terraform plan
```

`terraform plan` does not apply the changes.

#### 3. `terraform apply`

Creates and executes an execution plan to make the required
infrastructure changes. By default, Terraform asks for confirmation
before applying.

``` bash
terraform apply
```

#### 4. `terraform destroy`

Plans and applies the destruction of resources managed by the current
configuration and state.

``` bash
terraform destroy
```

### Basic workflow

``` text
Write Terraform configuration
            |
            v
      terraform init
            |
            v
       terraform fmt
            |
            v
     terraform validate
            |
            v
      terraform plan
            |
            v
      terraform apply
            |
            v
 Infrastructure created or updated
```

When appropriate, use `terraform destroy` to remove temporary practice
infrastructure and avoid ongoing cloud charges.

------------------------------------------------------------------------

## 10. Day 1 hands-on exercise

The first exercise uses the Local provider. It creates a local text
file, so you can learn the Terraform workflow without creating AWS
resources.

### Step 1: Create a project directory

``` bash
mkdir terraform-day1
cd terraform-day1
```

### Step 2: Create `main.tf`

``` hcl
terraform {
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.5"
    }
  }
}

provider "local" {}

resource "local_file" "hello" {
  filename = "hello.txt"
  content  = "Welcome to Terraform learning"
}
```

### Step 3: Initialize

``` bash
terraform init
```

Terraform downloads the Local provider.

### Step 4: Format and validate

``` bash
terraform fmt
terraform validate
```

### Step 5: Review the plan

``` bash
terraform plan
```

Expected summary for a new, empty working directory:

``` text
Plan: 1 to add, 0 to change, 0 to destroy.
```

### Step 6: Apply the configuration

``` bash
terraform apply
```

Review the plan and enter `yes` when prompted.

Terraform creates `hello.txt`.

Verify the content:

``` bash
cat hello.txt
```

Expected output:

``` text
Welcome to Terraform learning
```

### Step 7: Modify the configuration

Change the content argument:

``` hcl
content = "I am learning Terraform."
```

Run:

``` bash
terraform plan
```

Review the proposed change, then run:

``` bash
terraform apply
```

Verify the updated file:

``` bash
cat hello.txt
```

### Step 8: Destroy the managed resource

``` bash
terraform destroy
```

Review the proposed destruction and enter `yes` when prompted.

The Terraform-managed file is removed.

**Practice note:** Terraform will also create a `.terraform` directory,
a dependency lock file named `.terraform.lock.hcl`, and a state file
named `terraform.tfstate`. We will study providers and state management
in detail in later lessons. Do not commit state files containing
sensitive information to a public GitHub repository.

------------------------------------------------------------------------

## 11. Day 1 interview questions

### Basic questions

1.  What is Terraform?
2.  What is Infrastructure as Code?
3.  Why do we use Terraform?
4.  What are the benefits of IaC?
5.  What is the difference between Terraform and Ansible?
6.  Is Terraform declarative or imperative?
7.  What is HCL?
8.  What is a Terraform provider?
9.  How does Terraform communicate with AWS?
10. What is the difference between `terraform init` and
    `terraform plan`?
11. What is the purpose of `terraform apply`?
12. What happens when you execute `terraform destroy`?

### Scenario-based questions

**Q1.** You created five EC2 instances using Terraform. Someone manually
deletes two instances from the AWS Console. What would you expect when
you run `terraform plan`?

**Q2.** Two engineers need to manage infrastructure using Terraform. How
would you manage shared configuration and state safely?

**Q3.** Your company has Development, QA, and Production environments.
How would you manage infrastructure consistently across these
environments?

**Q4.** Can Terraform manage resources in AWS and Azure?

These scenarios will be revisited after learning state management,
providers, and modules.

------------------------------------------------------------------------

## 12. Quick revision notes

  -----------------------------------------------------------------------
  Concept                             Short definition
  ----------------------------------- -----------------------------------
  Terraform                           IaC tool used to provision and
                                      manage infrastructure

  IaC                                 Managing infrastructure through
                                      code

  HCL                                 HashiCorp Configuration Language

  Provider                            Plugin that allows Terraform to
                                      interact with a platform or service

  Resource                            Infrastructure component managed by
                                      Terraform

  Declarative                         Describes the desired outcome

  `terraform init`                    Initializes the working directory

  `terraform fmt`                     Formats configuration files

  `terraform validate`                Validates configuration

  `terraform plan`                    Previews proposed changes

  `terraform apply`                   Applies infrastructure changes

  `terraform destroy`                 Destroys managed resources
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 13. Day 1 completion checklist

-   [ ] Understand Terraform and IaC.
-   [ ] Understand manual infrastructure versus IaC.
-   [ ] Understand Terraform versus Ansible.
-   [ ] Understand declarative versus imperative approaches.
-   [ ] Understand Terraform architecture.
-   [ ] Install Terraform.
-   [ ] Understand HCL basics.
-   [ ] Practise the basic Terraform commands.
-   [ ] Complete the Local provider exercise.
-   [ ] Prepare interview notes and review the questions.

------------------------------------------------------------------------

## 14. Next lesson --- Day 2

**Terraform configuration blocks, providers, and resources**

Planned topics:

1.  Terraform block in depth
2.  Required providers
3.  Provider configuration
4.  AWS provider
5.  Resource block syntax
6.  Resource arguments and attributes
7.  Multiple resources
8.  Resource dependencies
9.  Creating an EC2 instance using Terraform

**Day 1 target:** Understand the fundamentals and complete the Local
provider exercise before moving to AWS infrastructure.
