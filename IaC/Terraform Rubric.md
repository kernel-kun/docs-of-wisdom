# Terraform Rubric <!-- omit from toc -->

- [Foundations](#foundations)
  - [Providers](#providers)
  - [Version Constraints](#version-constraints)
  - [Variables](#variables)
  - [Locals](#locals)
  - [Outputs](#outputs)
  - [String Interpolation \& Expressions](#string-interpolation--expressions)
  - [Built-in References (Named Values)](#built-in-references-named-values)
    - [Quick reference table](#quick-reference-table)
  - [Types: Sets \& Duplicates](#types-sets--duplicates)
- [Writing Resources](#writing-resources)
  - [Resource Blocks](#resource-blocks)
  - [Data Sources](#data-sources)
  - [Naming Conventions](#naming-conventions)
  - [Lifecycle](#lifecycle)
  - [Count \& for\_each](#count--for_each)
    - [When to use count vs. for\_each](#when-to-use-count-vs-for_each)
    - [Common patterns](#common-patterns)
    - [The index-shift problem (why `for_each` \> `count` for dynamic lists)](#the-index-shift-problem-why-for_each--count-for-dynamic-lists)
  - [Provisioners](#provisioners)
- [CLI Operations](#cli-operations)
  - [Commands](#commands)
  - [Format \& Validate](#format--validate)
  - [Plan, Apply \& Destroy](#plan-apply--destroy)
    - [Targeting \& Exclusion](#targeting--exclusion)
    - [Refresh](#refresh)
  - [Taint \& Replace](#taint--replace)
  - [Logging](#logging)
  - [Terraform Console](#terraform-console)
- [State](#state)
  - [State Management](#state-management)
  - [State Commands Cheatsheet](#state-commands-cheatsheet)
  - [State Locking](#state-locking)
- [Scaling](#scaling)
  - [Modules](#modules)
  - [Workspaces](#workspaces)
- [Reference](#reference)
  - [Must-Know Functions](#must-know-functions)
    - [String Functions](#string-functions)
    - [Collection Functions](#collection-functions)
    - [Type Conversion Functions](#type-conversion-functions)
    - [Conditional \& Null Handling](#conditional--null-handling)
    - [Encoding Functions](#encoding-functions)
    - [Filesystem Functions](#filesystem-functions)
    - [Numeric Functions](#numeric-functions)
    - [Network/CIDR Functions](#networkcidr-functions)
    - [Interview Cheat-Sheet: When to reach for which function](#interview-cheat-sheet-when-to-reach-for-which-function)


---

## Foundations

### Providers

- How does Terraform handle providers you haven't explicitly declared in a `required_providers` block?
  - Terraform infers the provider from resource prefixes (e.g., `aws_instance` → `hashicorp/aws`), downloads the latest version on `terraform init`, and adds it to `.terraform.lock.hcl`
- Can the AWS provider work without a `required_providers` declaration?
  - Yes — Terraform infers `hashicorp/aws` from the `aws_` prefix and downloads the latest; it works but is bad practice (no version pinning = non-reproducible builds)
- Can the AWS provider work without a `provider "aws" {}` block?
  - Yes — if credentials are available via env vars (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`) or shared credentials file, and you're fine with the default region from `AWS_REGION`; the provider block just becomes implicit
- How do you configure the AWS provider explicitly?
  ```hcl
  provider "aws" {
    region  = "us-east-1"
    profile = "dev"
  }
  ```
- How do you handle multiple AWS regions?
  - Use provider aliases:

    ```hcl
    provider "aws" {
      region = "us-east-1"
    }

    provider "aws" {
      alias  = "eu"
      region = "eu-west-1"
    }

    resource "aws_instance" "eu_server" {
      provider = aws.eu
      # ...
    }
    ```

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

### Version Constraints

- `= 5.0.0` — exactly this version
- `> 5.0.0` — greater than (any newer)
- `>= 5.0.0` — greater than or equal
- `< 6.0.0` — less than
- `<= 5.9.0` — less than or equal
- `!= 5.2.0` — anything except this version
- `~> 5.0` — pessimistic constraint: `>= 5.0, < 6.0` (allows minor/patch bumps)
- `~> 5.0.1` — `>= 5.0.1, < 5.1.0` (allows only patch bumps)
- Most common in practice: `version = "~> 5.0"` (pin major, allow minor upgrades)

### Variables

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

### Locals

- Why do we need `locals` when we already have `variable`?
  - Variables are **inputs** (set by the caller/user), locals are **computed internal values** (derived from variables, resources, or expressions) — they reduce repetition without exposing knobs to the user
  - Use locals for values that should NOT be configurable from outside the module
- When to use locals vs. variables:
  - `variable` = "the caller decides this value"
  - `local` = "this module computes this value internally"
- How do you declare locals?
  ```hcl
  locals {
    env_prefix  = "${var.project}-${var.environment}"
    common_tags = {
      Project     = var.project
      Environment = var.environment
      ManagedBy   = "terraform"
    }
  }
  ```
- How do you reference locals across the code?
  - `local.<name>` — e.g., `local.env_prefix`, `local.common_tags`
  - Works anywhere in the same module (any `.tf` file in the same directory)
- Can you have multiple `locals` blocks?
  - Yes — Terraform merges all `locals` blocks in the module; you can split them across files for organization
- Common use cases:
  - Computed names: `local.env_prefix` used in every resource's `Name` tag
  - Shared tags: `merge(local.common_tags, { Role = "web" })` on each resource
  - Conditional logic: `local.is_prod = var.environment == "prod"`
  - Reducing repetition: compute a value once, reference it 10 times

### Outputs

- How do you declare an output?
  - `output "name" { value = <expression> }`
- How do you mark an output as sensitive?
  - `sensitive = true` in the `output` block
- How do you reference an output from a child module?
  - `module.<module_name>.<output_name>`

### String Interpolation & Expressions

- How do you reference a variable inside a string?
  - `"Hello, ${var.name}"` — wrap expressions in `${}`
- How do you reference a resource attribute inside a string?
  - `"ID is ${aws_instance.web.id}"`
- How do you reference an output from a module inside a string?
  - `"VPC ID: ${module.network.vpc_id}"`
- Can you use expressions (conditionals, functions) inside `${}`?
  - Yes — `"env: ${var.env == "prod" ? "production" : "staging"}"`

### Built-in References (Named Values)

- `path.module` — filesystem path of the module where the expression is defined
  - Use case: referencing files relative to the current module — `file("${path.module}/templates/init.sh")`
- `path.root` — filesystem path of the root module (the one you run `terraform apply` in)
  - Use case: referencing files relative to the project root from a child module
- `path.cwd` — filesystem path of the original working directory before any `-chdir` argument
  - Rarely used; `path.root` is almost always what you want
- `terraform.workspace` — name of the currently selected workspace (default is `"default"`)
  - Use case: environment-based logic — `count = terraform.workspace == "prod" ? 3 : 1`
- `self` — reference to the current resource (only available inside `provisioner` and `connection` blocks)
  - Use case: `self.public_ip` inside a provisioner to reference the instance being provisioned
- `count.index` — 0-based index of the current instance (only inside `count` blocks)
- `each.key` / `each.value` — current item's key and value (only inside `for_each` blocks)

#### Quick reference table

| Named Value               | Available Where                          | Returns                                       |
| ------------------------- | ---------------------------------------- | --------------------------------------------- |
| `path.module`             | Anywhere in a module                     | Absolute path to the module directory         |
| `path.root`               | Anywhere                                 | Absolute path to the root module directory    |
| `path.cwd`                | Anywhere                                 | Working directory where Terraform was invoked |
| `terraform.workspace`     | Anywhere                                 | Current workspace name (string)               |
| `self`                    | `provisioner` / `connection` blocks only | The enclosing resource's attributes           |
| `count.index`             | Resources/modules with `count`           | Integer (0-based)                             |
| `each.key` / `each.value` | Resources/modules with `for_each`        | Map key or set member / map value             |

### Types: Sets & Duplicates

- Why does `toset()` reject / de-duplicate elements?
  - A set is defined as a collection of **unique** values with no ordering — duplicates are removed by design, not by accident
- If sets de-duplicate, why can't I just pass a list with duplicates to `for_each`?
  - Because `for_each` uses each element as a **resource instance key** (the map key / set member becomes part of the resource address like `aws_instance.web["a"]`); duplicate keys would mean two instances with the same address, which is invalid
  - Terraform forces you to be explicit: if your list has duplicates, `toset()` silently drops them, which may create fewer resources than you expect — that's a bug in your data, not something Terraform should hide
- What type does `for_each` accept?
  - A **map** or a **set of strings** — never a list or tuple directly

---

## Writing Resources

### Resource Blocks

- What is the general syntax of a resource block?
  - `resource "<provider>_<type>" "<local_name>" { ... }`
- How do you reference one resource's attribute from another?
  - `<resource_type>.<name>.<attribute>` e.g., `aws_subnet.main.id`
- What is `depends_on` and when do you use it?
  - Explicit dependency when Terraform can't infer the ordering from attribute references

### Data Sources

- What is the syntax for a data block?
  - `data "<provider>_<type>" "<name>" { ... }`
- How do you reference a data source attribute?
  - `data.<type>.<name>.<attribute>` e.g., `data.aws_ami.latest.id`
- When would you use a data source vs. a resource?
  - Data source: read existing infrastructure you don't manage
  - Resource: create/manage infrastructure through Terraform

### Naming Conventions

- What is the recommended naming convention for Terraform resources?
  - `snake_case` with meaningful, descriptive names: `aws_instance.app_server`, not `aws_instance.instance1`
- Why does naming convention matter?
  - Resource names become part of the **state address** (`aws_instance.app_server`) — renaming later requires `terraform state mv` or an import; bad names create permanent tech debt
- Key rules:
  - Use `snake_case` (underscores, not hyphens or camelCase)
  - Name by **purpose**, not by type: `aws_security_group.allow_http` not `aws_security_group.sg1`
  - For single instances of a type, `this` or `main` is acceptable: `aws_vpc.this`
  - Variables and outputs: also `snake_case` — `var.instance_type`, `output.db_endpoint`
  - Files: `main.tf`, `variables.tf`, `outputs.tf`, `providers.tf`, `terraform.tfvars`

### Lifecycle

- What is the `lifecycle` block?
  - A nested block inside any resource that controls how Terraform creates, updates, and destroys that resource
- What are the must-know lifecycle arguments?
  1. `create_before_destroy` — creates the replacement resource before destroying the old one (enables rolling/zero-downtime updates)
  2. `prevent_destroy` — rejects any plan that would destroy this resource (protects databases, S3 buckets, etc.)
  3. `ignore_changes` — tells Terraform to ignore drift on specified attributes after creation (useful when external systems modify tags, etc.)
  4. `replace_triggered_by` — forces replacement of this resource when a referenced resource or attribute changes
- How do you perform rolling updates (zero-downtime replacements)?
  - Use `create_before_destroy = true` — Terraform spins up the new instance first, then destroys the old one
  - Caveat: may cause naming conflicts if the resource has a unique name constraint; use `name_prefix` instead of `name` where possible
- How do you protect a critical resource from accidental deletion?
  - `lifecycle { prevent_destroy = true }` — any plan that includes destroying it will error out
- How do you ignore external changes to specific attributes?
  - `lifecycle { ignore_changes = [tags, ami] }` — Terraform won't revert those attributes even if they drift
- How do you ignore the entire resource block after creation (fully hands-off)?
  - `lifecycle { ignore_changes = all }` — Terraform creates the resource once and never proposes updates to any attribute, regardless of drift
  - The resource still exists in state and can be destroyed; it just won't be updated
- When would you use `replace_triggered_by`?
  - When a resource must be recreated if a dependency changes but Terraform can't infer that automatically, e.g.:
    ```hcl
    lifecycle {
      replace_triggered_by = [aws_ami.latest.id]
    }
    ```

### Count & for_each

- What does `count` do?
  - Creates N identical instances of a resource/module: `count = 3` → `resource[0]`, `resource[1]`, `resource[2]`
- How do you reference the current index inside a `count` block?
  - `count.index` (0-based)
- What does `for_each` do?
  - Creates one instance per item in a map or set, keyed by the map key or set member
- How do you reference the current item inside a `for_each` block?
  - `each.key` — the map key or set member
  - `each.value` — the map value (same as `each.key` for sets)
- Can you use `count` and `for_each` on the same resource?
  - No — they are mutually exclusive

#### When to use count vs. for_each

| Scenario                                                       | Use                               | Why                                                 |
| -------------------------------------------------------------- | --------------------------------- | --------------------------------------------------- |
| Fixed number of identical resources                            | `count`                           | Simple integer, no need for keys                    |
| List of items where **order matters** and items are **unique** | `for_each` with `toset(var.list)` | Stable keys; adding/removing doesn't shift indices  |
| Map of configs (each resource slightly different)              | `for_each` with map               | Each instance gets meaningful key + distinct values |
| Conditional resource (0 or 1 instance)                         | `count = var.enabled ? 1 : 0`     | Classic on/off pattern                              |
| Resources from a list where position may change                | `for_each`                        | Avoids the index-shift problem                      |

#### Common patterns

- **Create resources from a list using `count`:**

  ```hcl
  variable "names" { default = ["web", "api", "worker"] }

  resource "aws_instance" "server" {
    count = length(var.names)
    tags  = { Name = var.names[count.index] }
  }
  ```

  - Pitfall: if you remove "api" from the list, "worker" shifts from index 2 → 1 and Terraform destroys/recreates it

- **Create resources from a list using `for_each` (preferred):**

  ```hcl
  variable "names" { default = ["web", "api", "worker"] }

  resource "aws_instance" "server" {
    for_each = toset(var.names)
    tags     = { Name = each.key }
  }
  ```

  - Removing "api" only destroys `aws_instance.server["api"]` — others are untouched

- **Create resources from a map (distinct configs per instance):**

  ```hcl
  variable "instances" {
    default = {
      web    = { type = "t3.small", az = "us-east-1a" }
      worker = { type = "t3.large", az = "us-east-1b" }
    }
  }

  resource "aws_instance" "server" {
    for_each      = var.instances
    instance_type = each.value.type
    availability_zone = each.value.az
    tags          = { Name = each.key }
  }
  ```

- **Conditional resource (on/off toggle):**

  ```hcl
  resource "aws_cloudwatch_log_group" "debug" {
    count = var.enable_debug_logs ? 1 : 0
    name  = "/app/debug"
  }
  ```

  - Reference: `aws_cloudwatch_log_group.debug[0].arn` (must guard with conditional if count may be 0)

#### The index-shift problem (why `for_each` > `count` for dynamic lists)

- With `count`, instances are addressed by integer index: `resource[0]`, `resource[1]`, ...
- Removing an item from the middle shifts all subsequent indices → Terraform plans destroy + recreate for shifted resources
- With `for_each`, instances are addressed by key: `resource["web"]`, `resource["api"]` — removing one key only affects that single instance

### Provisioners

- What are provisioners?
  - Blocks that execute scripts or commands on a resource after creation (or before destruction) — they are a **last resort** for bootstrapping when no native provider resource exists
- Why are provisioners discouraged?
  - They break Terraform's declarative model, aren't tracked in state, can't be planned, and make configurations harder to reproduce; prefer `user_data`, config management tools, or native resources
- What are the three types of provisioners?
  1. `local-exec` — runs a command on the **machine running Terraform**
  2. `remote-exec` — runs commands on the **remote resource** via SSH or WinRM
  3. `file` — copies files/directories to the remote resource
- Must-know usage patterns:

  ```hcl
  resource "aws_instance" "web" {
    ami           = "ami-123456"
    instance_type = "t3.micro"

    # Run on the remote instance after creation
    provisioner "remote-exec" {
      inline = ["sudo apt-get update", "sudo apt-get install -y nginx"]
    }

    # Run locally after creation (e.g., update inventory)
    provisioner "local-exec" {
      command = "echo ${self.private_ip} >> inventory.txt"
    }

    # Run before destruction
    provisioner "local-exec" {
      when    = destroy
      command = "echo 'Destroying ${self.id}' >> destroy.log"
    }

    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }
  ```

- What does `when = destroy` do?
  - Runs the provisioner during resource destruction instead of creation
- What does `on_failure = continue` do?
  - Allows Terraform to continue even if the provisioner fails (default is `fail` which taints the resource)
- What is `self` in a provisioner?
  - A reference to the enclosing resource's attributes (e.g., `self.public_ip`, `self.id`)

---

## CLI Operations

### Commands

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

### Format & Validate

- What are the two things you should always run before `terraform init/plan/apply`?
  1. `terraform fmt` — formats code to canonical HCL style
  2. `terraform validate` — checks configuration against the provider schema for correctness
- What does `terraform fmt` do exactly?
  - Rewrites `.tf` files to a consistent style (indentation, alignment, ordering) — it only touches formatting, not logic
- Can you run `terraform fmt` recursively across all subdirectories?
  - Yes — `terraform fmt -recursive`
- What does `terraform validate` check?
  - Validates syntax, attribute names, types, required arguments, and block structure against the provider schema — catches typos and invalid attributes before you ever run a plan
- Does `terraform validate` require `terraform init` to have been run first?
  - Yes — it needs the provider plugins installed to validate against their schemas
- What is the recommended pre-plan workflow?
  1. `terraform fmt -recursive` (fix style)
  2. `terraform validate` (catch schema errors)
  3. `terraform plan` (preview changes)

### Plan, Apply & Destroy

- What does `terraform plan` do?
  - Generates an execution plan showing what Terraform will create, update, or destroy — without making any changes (it is inherently a dry run)
- What does `terraform apply` do?
  - Executes the changes required to reach the desired state; by default it runs a plan first and prompts for confirmation
- Can you do a dry run of `terraform apply`?
  - `terraform apply` already shows the plan and asks for approval before acting; for a completely non-interactive dry run, use `terraform plan` instead — there is no separate `-dry-run` flag
- What does `terraform destroy` do?
  - Destroys all resources managed by the current configuration (equivalent to `terraform apply -destroy`)
- Can `terraform plan` generate a destroy plan without actually destroying?
  - Yes — `terraform plan -destroy` shows what would be destroyed without touching anything

#### Targeting & Exclusion

- How do you destroy (or plan/apply) a single resource out of many?
  - Use `-target`: `terraform destroy -target=aws_instance.web`
  - Works with `plan` and `apply` too: `terraform plan -target=module.network`
- Can you pass multiple `-target` flags?
  - Yes — repeat the flag: `terraform apply -target=aws_instance.a -target=aws_instance.b`
- Does Terraform support an `-exclude` flag (the inverse of `-target`)?
  - No — Terraform has no native exclusion flag; you can only specify what to include via `-target`
- Does OpenTofu support `-exclude`?
  - Yes — OpenTofu added `-exclude=ADDRESS` (and `-exclude-file`) which excludes matched resources and their dependents from the plan/apply
- When should `-target` / `-exclude` be used?
  - Exceptional circumstances only (recovering from mistakes, working around tool limitations) — not for routine operations; prefer breaking large configs into smaller modules instead

#### Refresh

- What does `terraform refresh` do?
  - Reads the current state of all managed remote objects (e.g., from AWS) and updates the local state file to match reality — without modifying any actual infrastructure
- Does running refresh update the state file?
  - Yes — it writes the detected real-world state back into `terraform.tfstate`
- Is `terraform refresh` still recommended?
  - No — it is deprecated; use `terraform apply -refresh-only` (or `terraform plan -refresh-only`) instead
- Why was it deprecated?
  - `terraform refresh` applies state changes automatically without confirmation; `apply -refresh-only` shows detected drift and prompts for approval first, which is safer (avoids accidentally marking resources as deleted due to misconfigured credentials)

### Taint & Replace

- What did `terraform taint` do?
  - Marked a resource as "damaged" in state, forcing Terraform to destroy and recreate it on the next apply
- Is `terraform taint` still recommended?
  - No — it is deprecated; use `terraform apply -replace=<address>` instead
- Why was it deprecated?
  - `taint` modifies state immediately (side effect without plan review); `-replace` is plan-safe — you see what will happen before confirming
- How do you force-recreate a resource now?
  - `terraform apply -replace=aws_instance.web`
  - Or with plan: `terraform plan -replace=aws_instance.web`
- Can you replace multiple resources at once?
  - Yes — repeat the flag: `terraform apply -replace=aws_instance.a -replace=aws_instance.b`
- What about `terraform untaint`?
  - Also deprecated — no longer needed since `-replace` doesn't persist a tainted marker in state

### Logging

- How do you set the log level for Terraform?
  - `export TF_LOG=<level>` — valid levels: `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`
  - `TRACE` is the most verbose; `ERROR` is the quietest
- How do you write logs to a specific file instead of stderr?
  - `export TF_LOG_PATH="/tmp/terraform.log"`
  - Must be combined with `TF_LOG` — without `TF_LOG` set, no logs are generated regardless of path
- Can you set log levels per component?
  - Yes — `TF_LOG_CORE=TRACE` (Terraform core) and `TF_LOG_PROVIDER=DEBUG` (provider plugins) for independent control
- How do you disable logging?
  - Unset the env var: `unset TF_LOG` (or set to empty)

### Terraform Console

- What is `terraform console`?
  - An interactive REPL that lets you evaluate Terraform expressions against the current state and configuration
- When to use it:
  - Quickly test functions: `> join("-", ["foo", "bar"])` → `"foo-bar"`
  - Inspect resource attributes: `> aws_instance.web.public_ip` → `"3.14.159.26"`
  - Debug interpolations: `> "prefix-${var.env}"` → `"prefix-prod"`
  - Validate complex expressions before putting them in code: `> [for k, v in var.map : upper(k)]`
  - Check `terraform.workspace`: `> terraform.workspace` → `"dev"`
- Does it require `terraform init`?
  - Yes — it needs providers initialized to resolve resource data from state
- Does it modify state?
  - No — it is read-only; you can safely experiment without side effects

---

## State

### State Management

- Where is state stored by default?
  - Locally in the current working directory
- What is the default state file name?
  - `terraform.tfstate` (and `terraform.tfstate.backup` for the previous state)
- Is it possible to run Terraform without a state file?
  - No — Terraform fundamentally requires state to map configuration to real-world resources; without state it cannot know what it manages, detect drift, or plan changes
- Why is state essential?
  - It tracks the binding between resource addresses in config and real resource IDs in the cloud; without it, Terraform would have to query every possible resource to find matches (which most APIs don't support)
- How do you configure remote state (e.g., S3)?
  - `backend "s3" { bucket = "..." key = "..." region = "..." }` inside `terraform { ... }`
- How do you move a resource to a different state address?
  - `terraform state mv <old_address> <new_address>`
- What are the ways to remove Terraform tracking for a resource without destroying the actual infrastructure?
  1. `terraform state rm <address>` — removes the resource from state; the real object remains untouched but Terraform "forgets" it
  2. `removed` block (modern, recommended) — add to config:
     ```hcl
     removed {
       from = aws_instance.old
       lifecycle { destroy = false }
     }
     ```
     This integrates removal into the normal plan/apply workflow, is reviewable in PRs, and supports removing multiple resources at once
- After removing from state, what happens on the next `terraform plan`?
  - Terraform sees no tracked object for that address and proposes creating a new one — you should also remove the resource block from config to avoid conflicts

### State Commands Cheatsheet

| Command                                        | What it does                                     | When to use                                                   |
| ---------------------------------------------- | ------------------------------------------------ | ------------------------------------------------------------- |
| `terraform state list`                         | Lists all resource addresses in state            | Quick inventory of what Terraform manages                     |
| `terraform state show <addr>`                  | Shows all attributes of one resource             | Inspect a resource's current tracked state (IDs, IPs, etc.)   |
| `terraform state mv <old> <new>`               | Renames a resource address in state              | After renaming a resource in code (avoids destroy+recreate)   |
| `terraform state rm <addr>`                    | Removes resource from state (infra untouched)    | Hand off a resource to manual management or another workspace |
| `terraform state pull`                         | Dumps the entire state file to stdout as JSON    | Inspect remote state locally, scripting, or backup            |
| `terraform state push <file>`                  | Uploads a local state file to the remote backend | Disaster recovery, manual state repair (dangerous)            |
| `terraform import <addr> <id>`                 | Adds an existing cloud resource into state       | Adopting infra that was created outside Terraform             |
| `terraform state replace-provider <old> <new>` | Swaps the provider for all resources             | After forking a provider or migrating registries              |

- **`terraform state mv`** — detailed use cases:
  - Renamed a resource: `terraform state mv aws_instance.old aws_instance.new`
  - Moved into a module: `terraform state mv aws_instance.web module.compute.aws_instance.web`
  - Refactored module name: `terraform state mv module.old module.new`
  - Without this, Terraform would destroy the old address and create at the new one
- **`terraform state rm`** — detailed use cases:
  - Handing a resource off to another team/workspace
  - Resource was imported by mistake
  - You want Terraform to stop managing something but leave it running
  - Prefer the `removed` block for new code (plan-reviewable, version-controlled)
- **`terraform import`** — detailed use cases:
  - Brownfield adoption: existing infra needs to come under Terraform control
  - Someone manually created a resource that should be managed
  - Workflow: write the resource block first → run `terraform import <addr> <cloud_id>` → run `terraform plan` to verify no diff
- **`terraform state pull` / `push`** — when to use:
  - `pull`: inspect what's in remote state without opening the console; pipe to `jq` for scripting
  - `push`: restore from backup after state corruption (use with extreme caution — can overwrite team's work)
- **`terraform state replace-provider`** — when to use:
  - Migrating from `hashicorp/aws` to a community fork
  - Provider source address changed (rare, but happens during registry reorganizations)

### State Locking

- What is state locking?
  - Prevents concurrent `terraform apply` runs from corrupting the state file — only one operation can hold the lock at a time
- How do you enable state locking with the S3 backend (modern approach)?

  ```hcl
  terraform {
    backend "s3" {
      bucket       = "my-tf-state"
      key          = "infra/terraform.tfstate"
      region       = "us-east-1"
      use_lockfile = true
    }
  }
  ```

  - Creates a `<key>.tflock` file in S3 alongside the state file; requires only standard S3 permissions (`s3:GetObject`, `s3:PutObject`, `s3:DeleteObject`)

- What was the old DynamoDB approach?

  ```hcl
  terraform {
    backend "s3" {
      bucket         = "my-tf-state"
      key            = "infra/terraform.tfstate"
      region         = "us-east-1"
      dynamodb_table = "terraform-locks"
    }
  }
  ```

  - Required a separate DynamoDB table with a partition key named `LockID`
  - Needed additional IAM permissions (`dynamodb:GetItem`, `dynamodb:PutItem`, `dynamodb:DeleteItem`)

- What's the difference between the two approaches?
  | | S3 native (`use_lockfile`) | DynamoDB (deprecated) |
  |---|---|---|
  | Extra infra | None (lock lives in same S3 bucket) | Separate DynamoDB table |
  | IAM permissions | Standard S3 only | S3 + DynamoDB |
  | Status | Current / recommended | Deprecated, will be removed |
  | Lock mechanism | `.tflock` file in S3 | Item in DynamoDB table |
- Is DynamoDB locking still supported?
  - Yes for now, but deprecated — both can be configured simultaneously to allow migration from old setups

---

## Scaling

### Modules

- What is a module?
  - Any directory containing `.tf` files; the root module is your working directory, child modules are reusable components called via `module` blocks
- How do you call a local module?
  ```hcl
  module "network" {
    source = "./modules/network"
    vpc_cidr = "10.0.0.0/16"
  }
  ```
- How do you call a module from the Terraform Registry?
  ```hcl
  module "vpc" {
    source  = "terraform-aws-modules/vpc/aws"
    version = "~> 5.0"
    cidr    = "10.0.0.0/16"
  }
  ```
- How do you call a module from a Git repo?
  ```hcl
  module "vpc" {
    source = "git::https://github.com/org/terraform-modules.git//vpc?ref=v1.2.0"
  }
  ```
- What sources are supported?
  - Local path (`./modules/x`), Terraform Registry, GitHub/Bitbucket, S3 bucket, GCS bucket, generic Git/Mercurial URLs
- How do you pass values into a module?
  - Via input variables — the module declares `variable` blocks, the caller sets them in the `module` block
- Can you access a module's internal resources or locals from outside?
  - No — modules are encapsulated; you can only access values the module explicitly exposes via `output` blocks
- How do you reference a module's output?
  - `module.<module_name>.<output_name>` — e.g., `module.network.vpc_id`
- What happens when you add a new module and run `terraform plan`?
  - Error — you must run `terraform init` (or `terraform get`) first to download/initialize the module source
- Can modules use `count` or `for_each`?
  - Yes — you can create multiple instances of a module:
    ```hcl
    module "app" {
      source   = "./modules/app"
      for_each = var.environments
      env_name = each.key
    }
    ```
  - Reference: `module.app["prod"].endpoint`
- What is the recommended module structure?
  ```
  modules/network/
  ├── main.tf          # Resources
  ├── variables.tf     # Input variables
  ├── outputs.tf       # Exposed outputs
  └── versions.tf      # required_providers & terraform version
  ```

### Workspaces

- What are Terraform workspaces?
  - Isolated state instances within the same configuration — each workspace has its own `terraform.tfstate`
- What is the default workspace?
  - `default` — every Terraform project starts with this workspace
- Key commands:
  - `terraform workspace list` — show all workspaces
  - `terraform workspace new dev` — create a new workspace
  - `terraform workspace select prod` — switch to a workspace
  - `terraform workspace show` — print current workspace name
  - `terraform workspace delete staging` — delete a workspace (must have empty state)
- How does workspace state storage look (local backend)?
  ```
  project/
  ├── terraform.tfstate              # "default" workspace state
  └── terraform.tfstate.d/
      ├── dev/
      │   └── terraform.tfstate      # "dev" workspace state
      ├── staging/
      │   └── terraform.tfstate      # "staging" workspace state
      └── prod/
          └── terraform.tfstate      # "prod" workspace state
  ```
- How does it look with S3 backend?
  - Each workspace writes to `<key>` prefixed/suffixed by workspace name (configurable via `workspace_key_prefix`):
    ```
    s3://my-bucket/env:/dev/infra/terraform.tfstate
    s3://my-bucket/env:/prod/infra/terraform.tfstate
    ```
- How does `terraform.workspace` enable dynamic provisioning?

  ```hcl
  locals {
    instance_type = terraform.workspace == "prod" ? "t3.large" : "t3.micro"
    instance_count = terraform.workspace == "prod" ? 3 : 1
  }

  resource "aws_instance" "app" {
    count         = local.instance_count
    instance_type = local.instance_type
    tags = {
      Environment = terraform.workspace
    }
  }
  ```

- When to use workspaces:
  - Same infrastructure, different environments (dev/staging/prod) with minor parameter differences
  - Temporary testing environments that you spin up and tear down
  - Per-developer sandboxes
- When NOT to use workspaces:
  - Completely different infrastructure between environments (use separate root modules instead)
  - When teams need different access controls per environment (workspaces share the same backend permissions)
  - When environments diverge significantly in resource composition

---

## Reference

### Must-Know Functions

#### String Functions

- `join(separator, list)` — concatenates list elements into a single string
  - `join("-", ["foo", "bar", "baz"])` → `"foo-bar-baz"`
- `split(separator, string)` — splits a string into a list
  - `split(",", "a,b,c")` → `["a", "b", "c"]`
- `format(spec, values...)` — printf-style string formatting
  - `format("Hello, %s! You have %d items.", "Alice", 3)` → `"Hello, Alice! You have 3 items."`
- `replace(string, search, replace)` — replaces all occurrences of a substring
  - `replace("hello world", "world", "terraform")` → `"hello terraform"`
- `trimspace(string)` — removes leading/trailing whitespace
  - `trimspace("  hello  ")` → `"hello"`
- `lower(string)` / `upper(string)` — case conversion
  - `lower("FOO")` → `"foo"`
- `regex(pattern, string)` — extracts the first match of a regex
  - `regex("[0-9]+", "port-8080")` → `"8080"`

#### Collection Functions

- `length(collection)` — returns count of elements (works on list, map, set, and strings)
  - `length(["a", "b", "c"])` → `3`
- `lookup(map, key, default)` — safe map access with a fallback
  - `lookup({env = "prod"}, "region", "us-east-1")` → `"us-east-1"`
- `merge(map1, map2, ...)` — combines maps; later values win on key conflicts
  - `merge({a = 1}, {b = 2}, {a = 99})` → `{a = 99, b = 2}`
- `keys(map)` / `values(map)` — extract map keys or values as a list
  - `keys({a = 1, b = 2})` → `["a", "b"]`
- `flatten(list_of_lists)` — flattens nested lists into a single flat list
  - `flatten([["a", "b"], ["c"]])` → `["a", "b", "c"]`
- `concat(list1, list2, ...)` — combines multiple lists into one
  - `concat(["a"], ["b", "c"])` → `["a", "b", "c"]`
- `element(list, index)` — returns item at index (wraps around if index > length)
  - `element(["a", "b", "c"], 4)` → `"b"` (index 4 mod 3 = 1)
- `zipmap(keys_list, values_list)` — creates a map from parallel lists
  - `zipmap(["name", "age"], ["Alice", "30"])` → `{name = "Alice", age = "30"}`
- `contains(list, value)` — checks if a value exists in a list
  - `contains(["prod", "staging"], "prod")` → `true`
- `distinct(list)` — removes duplicate elements from a list
  - `distinct(["a", "b", "a"])` → `["a", "b"]`

#### Type Conversion Functions

- `toset(list)` — converts list to set (deduplicates, loses ordering)
- `tolist(set)` — converts set to list
- `tomap(object)` — converts an object to a map
- `tonumber(string)` / `tostring(number)` — type casting
- `try(expr1, expr2, ...)` — returns the first expression that doesn't error
  - `try(var.obj.nested.key, "default")` → `"default"` if path doesn't exist
- `can(expr)` — returns `true` if the expression evaluates without error
  - `can(tonumber("abc"))` → `false`

#### Conditional & Null Handling

- `coalesce(val1, val2, ...)` — returns the first non-null, non-empty-string value
  - `coalesce("", null, "fallback")` → `"fallback"`
- `coalescelist(list1, list2, ...)` — returns the first non-empty list
  - `coalescelist([], ["default"])` → `["default"]`
- `one(list)` — returns the single element of a one-item list, or `null` if empty
  - Used with conditional resources: `one(aws_instance.optional[*].id)`

#### Encoding Functions

- `jsonencode(value)` — converts HCL value to JSON string
  - `jsonencode({name = "web", port = 80})` → `"{\"name\":\"web\",\"port\":80}"`
- `jsondecode(string)` — parses JSON string into HCL value
- `base64encode(string)` / `base64decode(string)` — Base64 encoding/decoding
- `yamlencode(value)` / `yamldecode(string)` — YAML encoding/decoding

#### Filesystem Functions

- `file(path)` — reads a file's contents as a string
  - `file("${path.module}/scripts/init.sh")`
- `templatefile(path, vars)` — reads a file and renders template variables into it
  - `templatefile("${path.module}/user_data.tftpl", { name = var.name })`
- `fileexists(path)` — returns `true` if the file exists at the given path

#### Numeric Functions

- `max(numbers...)` / `min(numbers...)` — returns largest/smallest value
  - `max(5, 12, 9)` → `12`
- `ceil(number)` / `floor(number)` — round up/down to nearest integer
  - `ceil(4.2)` → `5`
- `abs(number)` — absolute value

#### Network/CIDR Functions

- `cidrsubnet(prefix, newbits, netnum)` — calculates a subnet address within a CIDR block
  - `cidrsubnet("10.0.0.0/16", 8, 1)` → `"10.0.1.0/24"` (adds 8 bits, picks subnet #1)
- `cidrhost(prefix, hostnum)` — calculates a host IP within a CIDR block
  - `cidrhost("10.0.1.0/24", 5)` → `"10.0.1.5"`

#### Interview Cheat-Sheet: When to reach for which function

| Problem                                               | Function          |
| ----------------------------------------------------- | ----------------- |
| Build a name from parts                               | `join`, `format`  |
| Parse a value out of a string                         | `split`, `regex`  |
| Combine multiple maps (e.g., default + override tags) | `merge`           |
| Flatten module outputs (list of lists)                | `flatten`         |
| Create a map from two parallel lists                  | `zipmap`          |
| Safe map lookup with default                          | `lookup`          |
| Dynamic subnet calculation                            | `cidrsubnet`      |
| Conditional fallback value                            | `coalesce`, `try` |
| Check if something is valid without erroring          | `can`             |
| Get single item from conditional resource             | `one`             |
| Convert list for `for_each`                           | `toset`           |
| Read and template a file                              | `templatefile`    |
