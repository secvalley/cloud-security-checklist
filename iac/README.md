# Infrastructure as Code Security Checklist

A security hardening checklist for Infrastructure as Code tooling, covering Terraform, Bicep/ARM, CloudFormation, and general IaC security practices.

[Back to main checklist](../README.md)

---

## Terraform

- [ ] **Encrypt state files at rest** `Severity: Critical`
  Use remote backends with encryption (S3 + KMS, Azure Storage + CMK, GCS + CMEK). State files contain sensitive values including passwords and connection strings.
  > Reference: [Terraform Backend Configuration](https://developer.hashicorp.com/terraform/language/backend) | [S3 Backend](https://developer.hashicorp.com/terraform/language/backend/s3)

- [ ] **Enable state file locking** `Severity: High`
  Configure state locking (DynamoDB for S3, native for Azure Storage, native for GCS) to prevent concurrent modifications that corrupt state.
  > Reference: [State Locking](https://developer.hashicorp.com/terraform/language/state/locking)

- [ ] **Restrict state file access** `Severity: Critical`
  Limit read/write access to state files to CI/CD service principals and authorized operators only. State files are equivalent to a full inventory of your infrastructure with credentials.
  > Reference: [Terraform State Security](https://developer.hashicorp.com/terraform/language/state/sensitive-data)

- [ ] **Use `sensitive` flag for secret variables** `Severity: High`
  Mark variables and outputs containing secrets with `sensitive = true`. Prevents values from appearing in plan output and logs.
  > Reference: [Sensitive Variables](https://developer.hashicorp.com/terraform/language/values/variables#suppressing-values-in-cli-output)

- [ ] **Pin provider and module versions** `Severity: High`
  Use exact version constraints (`= 5.0.0`) or pessimistic constraints (`~> 5.0`) for providers and modules. Unpinned versions can introduce breaking changes or malicious code.
  > Reference: [Provider Version Constraints](https://developer.hashicorp.com/terraform/language/providers/requirements#version-constraints)

- [ ] **Use `terraform plan` review before every apply** `Severity: High`
  Never run `terraform apply -auto-approve` in production. Always review the plan output, especially for destroy or replace operations.
  > Reference: [Terraform Workflow](https://developer.hashicorp.com/terraform/intro/core-workflow)

- [ ] **Run static analysis with tfsec or Checkov** `Severity: Medium`
  Integrate tfsec, Checkov, or Trivy IaC scanning in CI pipelines. Catches misconfigurations (public S3 buckets, open security groups) before they reach production.
  > Reference: [tfsec](https://aquasecurity.github.io/tfsec/) | [Checkov](https://www.checkov.io/)

- [ ] **Use Terraform Cloud/Enterprise or TACOS for team workflows** `Severity: Medium`
  For teams, use Terraform Cloud, Spacelift, Env0, or similar to enforce policy, manage state, provide audit trails, and control access.
  > Reference: [Terraform Cloud](https://developer.hashicorp.com/terraform/cloud-docs)

---

## Bicep / ARM Templates

- [ ] **Use `@secure()` decorator for secret parameters** `Severity: Critical`
  Mark parameters containing passwords, connection strings, and keys with `@secure()`. Prevents values from appearing in deployment logs and resource group deployment history.
  > Reference: [Bicep Secure Parameters](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/parameters#secure-parameters)

- [ ] **Reference Key Vault secrets in parameters** `Severity: High`
  Use Key Vault references in parameter files instead of hardcoding secrets. Secrets are resolved at deployment time without exposure in templates.
  > Reference: [Key Vault Reference](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/key-vault-parameter)

- [ ] **Enable deployment What-If before production deployments** `Severity: High`
  Run `az deployment group what-if` to preview changes before applying. Catches unintended resource deletions and modifications.
  > Reference: [ARM What-If](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-what-if)

- [ ] **Validate templates with Bicep linter** `Severity: Medium`
  Enable the Bicep linter and fix all warnings. The linter catches common mistakes, unused parameters, and insecure default values.
  > Reference: [Bicep Linter](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/linter)

- [ ] **Set secure defaults for resource properties** `Severity: High`
  Configure secure defaults: HTTPS-only, minimum TLS 1.2, encryption enabled, public access disabled. Don't rely on operators to override insecure defaults.
  > Reference: [Bicep Best Practices](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/best-practices)

- [ ] **Use template specs or Bicep modules for reusable patterns** `Severity: Medium`
  Publish hardened resource configurations as template specs or Bicep modules. Teams consume pre-approved, security-reviewed building blocks.
  > Reference: [Template Specs](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/template-specs) | [Bicep Modules](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/modules)

- [ ] **Run PSRule for Azure in CI** `Severity: Medium`
  Integrate PSRule for Azure to validate ARM/Bicep templates against Azure Well-Architected Framework recommendations and CIS benchmarks.
  > Reference: [PSRule for Azure](https://azure.github.io/PSRule.Rules.Azure/)

---

## CloudFormation

- [ ] **Enable termination protection on production stacks** `Severity: High`
  Activate termination protection to prevent accidental stack deletion. Requires explicit disabling before a stack can be deleted.
  > Reference: [Termination Protection](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-protect-stacks.html)

- [ ] **Use `NoEcho` for secret parameters** `Severity: Critical`
  Set `NoEcho: true` on parameters containing passwords and keys. Prevents values from appearing in the CloudFormation console and API responses.
  > Reference: [NoEcho Parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html)

- [ ] **Reference Secrets Manager or SSM Parameter Store** `Severity: High`
  Use dynamic references to resolve secrets at deployment time from Secrets Manager (`{{resolve:secretsmanager:...}}`) or SSM (`{{resolve:ssm-secure:...}}`).
  > Reference: [Dynamic References](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/dynamic-references.html)

- [ ] **Configure stack policies** `Severity: High`
  Define stack policies to protect critical resources (databases, IAM roles) from unintended updates or replacements during stack updates.
  > Reference: [Stack Policies](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/protect-stack-resources.html)

- [ ] **Enable drift detection** `Severity: Medium`
  Run drift detection regularly to identify resources that have been modified outside of CloudFormation. Drift indicates manual changes that bypass IaC controls.
  > Reference: [Drift Detection](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-stack-drift.html)

- [ ] **Run cfn-lint and cfn-nag in CI** `Severity: Medium`
  Use cfn-lint for template validation and cfn-nag for security analysis. Catches misconfigurations, insecure resource properties, and policy violations.
  > Reference: [cfn-lint](https://github.com/aws-cloudformation/cfn-lint) | [cfn-nag](https://github.com/stelligent/cfn_nag)

- [ ] **Use StackSets for multi-account deployments** `Severity: Medium`
  Deploy security baselines (GuardDuty, Config rules, CloudTrail) across all accounts using CloudFormation StackSets. Ensures consistent security posture.
  > Reference: [StackSets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/what-is-cfnstacksets.html)

---

## General IaC Security

- [ ] **Never commit secrets to version control** `Severity: Critical`
  Use `.gitignore` to exclude `.tfvars` files, `.env` files, and state files. Add pre-commit hooks with tools like git-secrets, gitleaks, or trufflehog to detect committed secrets.
  > Reference: [git-secrets](https://github.com/awslabs/git-secrets) | [gitleaks](https://github.com/gitleaks/gitleaks)

- [ ] **Implement pre-commit hooks for IaC** `Severity: High`
  Configure pre-commit hooks to run formatting, linting, and security scanning before every commit. Catches issues at the developer's workstation, not in CI.
  > Reference: [pre-commit-terraform](https://github.com/antonbabenko/pre-commit-terraform) | [pre-commit](https://pre-commit.com/)

- [ ] **Require CI/CD pipeline for all infrastructure changes** `Severity: High`
  Block direct `apply` from developer workstations. All infrastructure changes should flow through a CI/CD pipeline with plan review, policy checks, and approval gates.
  > Reference: [GitOps for Infrastructure](https://developer.hashicorp.com/terraform/tutorials/automation/github-actions)

- [ ] **Implement policy-as-code** `Severity: High`
  Use OPA/Rego, Sentinel, or Azure Policy to enforce organizational rules (naming conventions, required tags, region restrictions, allowed resource types) automatically.
  > Reference: [OPA](https://www.openpolicyagent.org/) | [Sentinel](https://developer.hashicorp.com/sentinel)

- [ ] **Tag all resources with owner, environment, and cost center** `Severity: Medium`
  Enforce mandatory tags on all resources via policy. Tags enable cost attribution, security incident response (who owns this?), and lifecycle management.
  > Reference: [Tagging Strategies](https://docs.aws.amazon.com/tag-editor/latest/userguide/tagging-best-practices.html)

- [ ] **Use separate state/stacks per environment** `Severity: High`
  Maintain isolated state files for dev, staging, and production. Prevents a bad plan in dev from accidentally destroying production resources.
  > Reference: [Terraform Workspaces](https://developer.hashicorp.com/terraform/language/state/workspaces)

- [ ] **Review and minimize provider permissions** `Severity: Medium`
  Grant CI/CD service principals only the permissions needed for the resources they manage. Avoid using admin/owner credentials for IaC pipelines.
  > Reference: [Least Privilege for CI/CD](https://developer.hashicorp.com/terraform/cloud-docs/run/run-environment)

- [ ] **Maintain IaC documentation and module registry** `Severity: Low`
  Document module inputs, outputs, and usage examples. Maintain an internal module registry so teams reuse approved patterns instead of writing insecure one-offs.
  > Reference: [Terraform Registry](https://developer.hashicorp.com/terraform/registry)

---

[Back to main checklist](../README.md)
