Imperative = you list the steps.
Everything you’ve done so far is imperative. Whether you clicked through the console or ran aws ec2 create-vpc, you were issuing commands in order: create this, then that, then attach them.

Declarative = you describe the end result. The file says what should exist, not how to build it. Terraform figures out the steps. Run terraform apply twice and the second run does nothing — reality already matches the file. That’s idempotency, and it’s the whole reason declarative wins.

State
For Terraform to know “do I need to create this or does it already exist?”, it needs a memory of what it built. That’s state, stored in terraform.tfstate — a JSON file mapping your config to real AWS resource IDs: "aws_vpc.main": { "id": "vpc-0cb1a0e2531f2b135", ... }

your .tf files   ──┐
                   ├──> Terraform computes the difference
state file       ──┤
                   │
actual AWS       ──┘

Terraform plan shows you that difference before anything changes. apply executes it. Two things about state worth knowing now: It can contain secrets. Database passwords, generated keys — they end up in the state file in plaintext. That’s why your lab tells you to .gitignore it immediately.

A Terraform provider is a plugin that enables Terraform to interact with external APIs, cloud platforms, and services. It acts as a translator between your human-readable Terraform configuration code (HCL) and the specific API commands that platforms like AWS, Azure, Google Cloud, or GitHub understand. This is why you can create a delclarative result.

Module. A reusable container of configuration files that groups multiple resources together into a single, shareable blueprint.
A provider exposes the raw resources from a service, while a module acts like a function that packages those resources for reuse

Drift
When reality stops matching state. Almost always because someone changed something in the console. Say Terraform created a security group, then you clicked into it and added a rule by hand. Now:

Your .tf file says 1 rule
State says 1 rule
AWS actually has 2

Next plan notices and wants to remove your manual rule, because the config is the source of truth.



terraform init

Run once per directory, and again whenever you add a provider or module. It:

downloads the AWS provider plugin into .terraform/
downloads the VPC module’s source
writes .terraform.lock.hcl pinning exact versions
sets up the state backend

Nothing touches AWS. It’s dependency installation.

terraform plan

The dry run. Terraform:

reads your .tf files
reads the state file
queries AWS for the actual current state of everything in state
computes the difference and prints it

Output uses:

+ create
~ update in place
- destroy
-/+ destroy and recreate

Nothing changes. This is your diff review, and reading it carefully is the habit the lab is drilling.

terraform apply

Runs a plan, shows it, asks for yes, then executes. Creates resources in dependency order, parallelizing where possible, and writes every resource ID into the state file as it goes.