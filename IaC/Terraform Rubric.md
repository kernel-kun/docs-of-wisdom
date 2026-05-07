# Terraform Rubric

## Commands

- How do you list the complete state of all resources?
  - `terraform state list`
- How do you show the detailed state of a single resource?
  - `terraform state show <resource_address>`
- What is the difference between `terraform state show` and `terraform show`?
  - `terraform state show <addr>` — shows a single resource's attributes from the state file
  - `terraform show` — renders the entire state file (or a plan file) as human-readable output
- How do you view a saved plan file?
  - `terraform show <planfile>` or `terraform show -json <planfile>`
- How do you remove a resource from state without destroying it?
  - `terraform state rm <resource_address>`
- How do you import an existing resource into state?
  - `terraform import <resource_address> <cloud_id>`

## Providers

- How does Terraform handle providers you haven't explicitly declared in a `required_providers` block?
  - Terraform infers the provider from resource prefixes (e.g., `aws_instance` → `hashicorp/aws`), downloads the latest version on `terraform init`, and adds it to `.terraform.lock.hcl`
- How do you pin a provider to a specific version?
  - In the `required_providers` block: `aws = { source = "hashicorp/aws", version = "~> 5.0" }`
- How do you alias a provider and reference that alias in a resource?
  - Declare: `provider "aws" { alias = "us_west" region = "us-west-2" }`
  - Use: `resource "aws_instance" "web" { provider = aws.us_west ... }`
- How do you set the region for a specific resource when you have multiple provider configs?
  - Via the aliased provider block — the region lives in the provider config, not the resource
- What are the three ways to list currently installed providers?
  - `terraform version` — shows core + provider versions
  - `terraform init` — re-resolves and prints provider versions during initialization
  - `terraform providers` — shows the provider tree required by the configuration

## Variables

- If you reference `var.foo` and define its value in `terraform.tfvars`, but never declare a `variable "foo" {}` block, what happens?
  - Error — Terraform requires a `variable` declaration; having a value without the declaration is invalid
- How do you reference a variable inside a resource block?
  - `var.<name>` e.g., `instance_type = var.instance_size`
- What are the four ways to pass variable values (in increasing precedence)?
  1. Default value in the `variable` block (lowest)
  2. `terraform.tfvars` or `*.auto.tfvars` files
  3. `-var-file=<file>` flag
  4. `-var 'key=value'` flag or `TF_VAR_<name>` env var (highest)
- What is the exact precedence order?
  - Defaults < `terraform.tfvars` < `*.auto.tfvars` (alphabetical) < `-var-file` < `-var` / `TF_VAR_` env var — last one wins
- What are the must-know attributes in a `variable` block?
  - `default` — fallback value if none supplied
  - `type` — constraint (string, number, bool, list, map, object, etc.)
  - `description` — documents purpose (shown in `terraform plan` prompts)
  - `validation` — custom rules with `condition` and `error_message`
- How do you mark a variable as sensitive?
  - `sensitive = true` inside the `variable` block — Terraform redacts it from plan/apply output
- What does `TF_VAR_` prefix do?
  - Any env var prefixed with `TF_VAR_` is automatically read as a variable value, e.g., `export TF_VAR_region="us-east-1"`

## String Interpolation & Expressions

- How do you reference a variable inside a string?
  - `"Hello, ${var.name}"` — wrap expressions in `${}`
- How do you reference a resource attribute inside a string?
  - `"ID is ${aws_instance.web.id}"`
- How do you reference an output from a module inside a string?
  - `"VPC ID: ${module.network.vpc_id}"`
- Can you use expressions (conditionals, functions) inside `${}`?
  - Yes — `"env: ${var.env == "prod" ? "production" : "staging"}"`

## Outputs

- How do you declare an output?
  - `output "name" { value = <expression> }`
- How do you mark an output as sensitive?
  - `sensitive = true` in the `output` block
- How do you reference an output from a child module?
  - `module.<module_name>.<output_name>`

## Resource Blocks

- What is the general syntax of a resource block?
  - `resource "<provider>_<type>" "<local_name>" { ... }`
- How do you reference one resource's attribute from another?
  - `<resource_type>.<name>.<attribute>` e.g., `aws_subnet.main.id`
- What is `depends_on` and when do you use it?
  - Explicit dependency when Terraform can't infer the ordering from attribute references

## Data Sources

- What is the syntax for a data block?
  - `data "<provider>_<type>" "<name>" { ... }`
- How do you reference a data source attribute?
  - `data.<type>.<name>.<attribute>` e.g., `data.aws_ami.latest.id`
- When would you use a data source vs. a resource?
  - Data source: read existing infrastructure you don't manage
  - Resource: create/manage infrastructure through Terraform

## State Management

- Where is state stored by default?
  - Locally in `terraform.tfstate`
- How do you configure remote state (e.g., S3)?
  - `backend "s3" { bucket = "..." key = "..." region = "..." }` inside `terraform { ... }`
- How do you move a resource to a different state address?
  - `terraform state mv <old_address> <new_address>`
