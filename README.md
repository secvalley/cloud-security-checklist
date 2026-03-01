# Cloud Security Checklist

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/secvalley/cloud-security-checklist?style=social)](https://github.com/secvalley/cloud-security-checklist/stargazers)
[![Contributors](https://img.shields.io/github/contributors/secvalley/cloud-security-checklist)](https://github.com/secvalley/cloud-security-checklist/graphs/contributors)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

A comprehensive, actionable security checklist for cloud environments. Based on CIS Benchmarks and real-world hardening experience.

Use these checklists to audit your cloud infrastructure, prepare for compliance assessments, or establish security baselines for new deployments.

---

## Checklists

| Platform | Description | Controls |
|----------|-------------|----------|
| [**Azure**](azure/) | Identity, Networking, Storage, Compute, Database, Logging, Key Vault | 46 |
| [**AWS**](aws/) | IAM, S3, EC2, RDS, CloudTrail, VPC, KMS | 45 |
| [**GCP**](gcp/) | IAM, Compute, Storage, Networking, Logging, BigQuery | 34 |
| [**Microsoft 365**](microsoft-365/) | Entra ID, Exchange Online, SharePoint, Teams, Compliance | 32 |
| [**Kubernetes**](kubernetes/) | Pod Security, RBAC, Network, Image Security, Secrets | 28 |
| [**Infrastructure as Code**](iac/) | Terraform, Bicep/ARM, CloudFormation, CI/CD Gates | 30 |

**Total: 215 security controls across 6 platforms**

---

## Why This Checklist?

Most cloud breaches stem from misconfigurations, not sophisticated exploits. Overly permissive IAM roles, public storage buckets, disabled logging, missing encryption - these are the real attack surface.

This project exists because:

- **CIS Benchmarks are 300+ pages** - Teams need a practical, scannable format
- **Checklists beat documents** - A checkbox you can tick drives action; a PDF gathers dust
- **Multi-cloud is reality** - Most organizations run more than one cloud; security controls shouldn't live in silos
- **Open source means community-reviewed** - More eyes on security guidance means better guidance

Each control includes severity ratings, actionable descriptions, and references to CIS Benchmarks and vendor documentation.

---

## How to Use

1. **Pick your platform** from the table above
2. **Work through controls** by category (start with Critical/High severity)
3. **Check off** completed items as you harden your environment
4. **Revisit quarterly** - cloud services change, new controls get added

You can fork this repo to track your organization's progress, or use the checklists as a reference during audits.

---

## Severity Levels

| Level | Meaning |
|-------|---------|
| `Critical` | Immediate risk of breach or data exposure. Fix first. |
| `High` | Significant security gap. Address within days. |
| `Medium` | Defense-in-depth control. Plan for near-term. |
| `Low` | Best practice. Implement as resources allow. |

---

## Contributing

Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on adding new controls, suggesting changes, or reporting issues.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

Maintained by [SecValley](https://secvalley.com) - Cloud Security Consulting & CSPM Solutions

If this checklist helps you, consider giving it a star to help others find it.
