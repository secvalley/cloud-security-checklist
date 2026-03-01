# GCP Security Checklist

A security hardening checklist for Google Cloud Platform environments, based on [CIS Google Cloud Platform Foundation Benchmark v3.0](https://www.cisecurity.org/benchmark/google_cloud_computing_platform).

[Back to main checklist](../README.md)

---

## IAM

- [ ] **Avoid using the default service account** `Severity: Critical`
  Do not use the default Compute Engine or App Engine service accounts. Create dedicated service accounts with minimal permissions for each workload.
  > Reference: [CIS GCP 1.1](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Service account best practices](https://cloud.google.com/iam/docs/best-practices-service-accounts)

- [ ] **Disable service account key creation where possible** `Severity: High`
  Use Workload Identity, attached service accounts, or Workload Identity Federation instead of service account keys. Keys are long-lived credentials that can be leaked.
  > Reference: [CIS GCP 1.4](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Workload Identity](https://cloud.google.com/kubernetes-engine/docs/concepts/workload-identity)

- [ ] **Rotate service account keys within 90 days** `Severity: High`
  For any remaining service account keys, enforce rotation within 90 days. Use organization policies to restrict key creation and set expiration.
  > Reference: [CIS GCP 1.5](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Managing keys](https://cloud.google.com/iam/docs/keys-create-delete)

- [ ] **Enforce MFA for all users** `Severity: Critical`
  Enable 2-Step Verification enforcement in Google Workspace admin. Require security keys for administrative accounts.
  > Reference: [CIS GCP 1.2](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [2-Step Verification](https://support.google.com/a/answer/175197)

- [ ] **Avoid granting primitive roles (Owner, Editor, Viewer)** `Severity: High`
  Use predefined or custom roles instead of primitive (basic) roles. Primitive roles grant overly broad permissions that violate least privilege.
  > Reference: [CIS GCP 1.6](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Understanding roles](https://cloud.google.com/iam/docs/understanding-roles)

- [ ] **Enable IAM Recommender** `Severity: Medium`
  Use IAM Recommender to identify and remove excess permissions. Review and apply recommendations at least monthly.
  > Reference: [CIS GCP 1.7](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [IAM Recommender](https://cloud.google.com/policy-intelligence/docs/role-recommendations-overview)

- [ ] **Restrict domain sharing in IAM policies** `Severity: Medium`
  Use organization policies to restrict IAM grants to specific domains. Prevents accidental sharing with external Google accounts.
  > Reference: [CIS GCP 1.3](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Domain restriction](https://cloud.google.com/resource-manager/docs/organization-policy/restricting-domains)

---

## Compute Engine

- [ ] **Enable Shielded VM features** `Severity: High`
  Enable Secure Boot, vTPM, and Integrity Monitoring on all instances. Shielded VMs protect against rootkits and boot-level malware.
  > Reference: [CIS GCP 4.1](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Shielded VMs](https://cloud.google.com/shielded-vm/docs/shielded-vm)

- [ ] **Enable OS Login** `Severity: High`
  Use OS Login instead of managing SSH keys in instance metadata. OS Login ties SSH access to IAM permissions and supports 2FA.
  > Reference: [CIS GCP 4.2](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [OS Login](https://cloud.google.com/compute/docs/instances/managing-instance-access)

- [ ] **Disable serial port access** `Severity: High`
  Disable the interactive serial console on all instances. Serial port access can bypass network security controls.
  > Reference: [CIS GCP 4.3](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Serial console](https://cloud.google.com/compute/docs/troubleshooting/troubleshooting-using-serial-console)

- [ ] **Encrypt disks with CMEK** `Severity: Medium`
  Use Customer-Managed Encryption Keys (CMEK) for persistent disks containing sensitive data. Provides key control beyond Google-managed encryption.
  > Reference: [CIS GCP 4.4](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [CMEK](https://cloud.google.com/compute/docs/disks/customer-managed-encryption)

- [ ] **Disable public IP addresses on instances** `Severity: Medium`
  Remove public (external) IPs from instances that don't require direct internet access. Use Cloud NAT and IAP for outbound and inbound access respectively.
  > Reference: [CIS GCP 4.5](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [IAP TCP forwarding](https://cloud.google.com/iap/docs/using-tcp-forwarding)

- [ ] **Enable automatic patching with OS Patch Management** `Severity: Medium`
  Use OS Patch Management to deploy patch jobs on a regular schedule. Monitor patch compliance across all instances.
  > Reference: [OS Patch Management](https://cloud.google.com/compute/docs/os-patch-management)

---

## Cloud Storage

- [ ] **Enable Uniform Bucket-Level Access** `Severity: High`
  Enable Uniform bucket-level access to use only IAM for access control. Disables ACLs, which are harder to audit and often misconfigured.
  > Reference: [CIS GCP 5.1](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Uniform access](https://cloud.google.com/storage/docs/uniform-bucket-level-access)

- [ ] **Do not grant allUsers or allAuthenticatedUsers access** `Severity: Critical`
  Ensure no bucket grants access to `allUsers` (public) or `allAuthenticatedUsers` (any Google account). Use organization policies to prevent public access.
  > Reference: [CIS GCP 5.2](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Public access prevention](https://cloud.google.com/storage/docs/public-access-prevention)

- [ ] **Enable object versioning on critical buckets** `Severity: Medium`
  Turn on versioning for buckets storing important data. Protects against accidental overwriting and deletion.
  > Reference: [CIS GCP 5.3](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Object versioning](https://cloud.google.com/storage/docs/object-versioning)

- [ ] **Use CMEK for sensitive data** `Severity: Medium`
  Encrypt sensitive buckets with Customer-Managed Encryption Keys stored in Cloud KMS. Provides control over key lifecycle and access.
  > Reference: [CIS GCP 5.4](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [CMEK for Cloud Storage](https://cloud.google.com/storage/docs/encryption/customer-managed-keys)

- [ ] **Configure retention policies for compliance data** `Severity: Medium`
  Set retention policies with Bucket Lock on buckets containing compliance-relevant data. Prevents deletion before the retention period expires.
  > Reference: [Retention policies](https://cloud.google.com/storage/docs/bucket-lock)

---

## Networking

- [ ] **Restrict SSH and RDP from the internet** `Severity: Critical`
  Ensure no firewall rules allow SSH (22) or RDP (3389) from `0.0.0.0/0`. Use Identity-Aware Proxy (IAP) for secure remote access.
  > Reference: [CIS GCP 3.6](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [IAP](https://cloud.google.com/iap/docs/concepts-overview)

- [ ] **Enable VPC Flow Logs** `Severity: High`
  Enable flow logs on all subnets with at least a 5-second aggregation interval. Required for network visibility and incident investigation.
  > Reference: [CIS GCP 3.8](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [VPC Flow Logs](https://cloud.google.com/vpc/docs/using-flow-logs)

- [ ] **Use VPC Service Controls for sensitive projects** `Severity: High`
  Create service perimeters around projects containing sensitive data. VPC Service Controls prevent data exfiltration even by authenticated principals.
  > Reference: [CIS GCP 3.9](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [VPC Service Controls](https://cloud.google.com/vpc-service-controls/docs/overview)

- [ ] **Enable Private Google Access for private subnets** `Severity: Medium`
  Allow instances without external IPs to reach Google APIs via private routes. Eliminates the need for NAT or public IPs to access GCP services.
  > Reference: [CIS GCP 3.7](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Private Google Access](https://cloud.google.com/vpc/docs/private-google-access)

- [ ] **Review and minimize firewall rules** `Severity: Medium`
  Audit firewall rules regularly. Remove rules with overly broad source ranges, unused rules, and rules that allow all protocols/ports.
  > Reference: [CIS GCP 3.10](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Firewall rules](https://cloud.google.com/vpc/docs/firewalls)

- [ ] **Enable Cloud Armor for public-facing services** `Severity: Medium`
  Configure Cloud Armor WAF policies on load balancers serving external traffic. Protects against OWASP Top 10, DDoS, and bot traffic.
  > Reference: [Cloud Armor](https://cloud.google.com/armor/docs/cloud-armor-overview)

---

## Logging & Monitoring

- [ ] **Enable audit logging for all services** `Severity: Critical`
  Ensure Admin Activity, Data Access, and System Event audit logs are enabled for all services. Admin Activity logs are always on; Data Access logs must be explicitly enabled.
  > Reference: [CIS GCP 2.1](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Audit logs](https://cloud.google.com/logging/docs/audit)

- [ ] **Configure log sinks to a centralized destination** `Severity: High`
  Export logs to BigQuery, Cloud Storage, or a SIEM via Pub/Sub aggregated log sink at the organization level. Prevents log deletion by compromised project owners.
  > Reference: [CIS GCP 2.2](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Log sinks](https://cloud.google.com/logging/docs/export)

- [ ] **Set up log-based alerts for critical events** `Severity: High`
  Create alerting policies for: project ownership changes, custom role changes, VPC network changes, firewall rule changes, route changes, and audit configuration changes.
  > Reference: [CIS GCP 2.3-2.9](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Log-based alerts](https://cloud.google.com/logging/docs/alerting/log-based-alerts)

- [ ] **Enable Security Command Center** `Severity: High`
  Activate Security Command Center (Standard or Premium) for centralized vulnerability and threat detection across your GCP organization.
  > Reference: [CIS GCP 2.10](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Security Command Center](https://cloud.google.com/security-command-center/docs/concepts-security-command-center-overview)

- [ ] **Configure log retention policies** `Severity: Medium`
  Set retention periods that meet regulatory requirements. Default retention is 30 days for most logs; increase for audit and compliance needs.
  > Reference: [CIS GCP 2.11](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [Retention](https://cloud.google.com/logging/docs/routing/overview#retention)

---

## BigQuery

- [ ] **Restrict dataset access to specific principals** `Severity: High`
  Review all BigQuery dataset IAM bindings. Remove `allUsers` and `allAuthenticatedUsers` access. Grant access to specific groups or service accounts.
  > Reference: [CIS GCP 7.1](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [BigQuery access control](https://cloud.google.com/bigquery/docs/access-control)

- [ ] **Enable CMEK encryption for sensitive datasets** `Severity: Medium`
  Use Customer-Managed Encryption Keys for BigQuery datasets containing sensitive or regulated data. Provides control over encryption key lifecycle.
  > Reference: [CIS GCP 7.2](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [BigQuery CMEK](https://cloud.google.com/bigquery/docs/customer-managed-encryption)

- [ ] **Enable BigQuery audit logging** `Severity: High`
  Ensure Data Access audit logs are enabled for BigQuery. Monitor who is querying what data and detect anomalous query patterns.
  > Reference: [CIS GCP 7.3](https://www.cisecurity.org/benchmark/google_cloud_computing_platform) | [BigQuery audit logs](https://cloud.google.com/bigquery/docs/reference/auditlogs)

- [ ] **Use authorized views for row/column-level access** `Severity: Medium`
  Implement authorized views or column-level security to restrict access to sensitive columns (PII, financial data) within shared datasets.
  > Reference: [Authorized views](https://cloud.google.com/bigquery/docs/authorized-views) | [Column-level security](https://cloud.google.com/bigquery/docs/column-level-security-intro)

- [ ] **Configure table expiration for temporary data** `Severity: Low`
  Set default table expiration on datasets used for temporary or analytical workloads. Prevents accumulation of stale data that could be exposed.
  > Reference: [Table expiration](https://cloud.google.com/bigquery/docs/best-practices-storage)

---

[Back to main checklist](../README.md)
