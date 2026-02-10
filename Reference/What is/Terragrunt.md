#iac #project #product 

https://terragrunt.gruntwork.io/

Terragrunt is a thin wrapper around Terraform that helps you keep Terraform code DRY, consistent, and easier to manage across many environments, regions, and accounts. It is designed for real-world infrastructure setups where the same Terraform modules are reused dozens or hundreds of times with small variations.

Terragrunt does **not** replace Terraform. Instead, it orchestrates Terraform by handling configuration reuse, remote state, dependency ordering, and multi-environment structure.

## Why Terragrunt exists

Plain Terraform works well for small setups, but teams quickly run into problems such as:

- Massive duplication of backend and provider configuration
- Copy-pasted variables across environments
- Complex scripts to run Terraform in the right order
- Difficulty managing dependencies between stacks

Terragrunt solves these problems by adding a higher-level configuration layer on top of Terraform.

## Core concepts

### DRY configuration with `terragrunt.hcl`

Terragrunt uses `terragrunt.hcl` files to define environment-specific configuration while reusing shared logic:

- Common settings live in parent folders
- Child folders inherit and override values
- No duplicated backend or provider blocks

### Opinionated remote state management

Terragrunt can automatically configure Terraform remote state:

- S3 + DynamoDB (AWS)
- GCS (GCP)
- Azure Blob Storage

You define the backend once, and every module uses it consistently.

### Dependency management

Terragrunt understands dependencies between Terraform modules:

- Ensures networking is created before compute
- Allows outputs from one module to be inputs to another
- Can run stacks in dependency order with a single command

### Multi-environment orchestration

Terragrunt is especially strong for setups like:

- dev / staging / prod
- multiple regions
- multiple cloud accounts

You typically end up with a directory structure that mirrors your environment layout instead of copying Terraform code.