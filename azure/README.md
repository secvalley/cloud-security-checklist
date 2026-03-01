# Azure Security Checklist

A security hardening checklist for Microsoft Azure environments, based on [CIS Microsoft Azure Foundations Benchmark v2.1](https://www.cisecurity.org/benchmark/azure).

[Back to main checklist](../README.md)

---

## Identity & Access Management

- [ ] **Enable MFA for all users** `Severity: Critical`
  Enforce multi-factor authentication for every user in Entra ID, with no exceptions for admin accounts. Password-only authentication is the leading cause of account compromise.
  > Reference: [CIS Azure 1.1.1](https://www.cisecurity.org/benchmark/azure) | [Microsoft Entra MFA](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-mfa-howitworks)

- [ ] **Enforce MFA for all administrative users** `Severity: Critical`
  Create a Conditional Access policy requiring MFA for users with directory roles (Global Admin, Security Admin, etc.). Admins are the highest-value targets.
  > Reference: [CIS Azure 1.1.2](https://www.cisecurity.org/benchmark/azure) | [Conditional Access: Require MFA for admins](https://learn.microsoft.com/en-us/entra/identity/conditional-access/howto-conditional-access-policy-admin-mfa)

- [ ] **Block legacy authentication protocols** `Severity: Critical`
  Create a Conditional Access policy to block legacy authentication (IMAP, POP3, SMTP Auth, ActiveSync with basic auth). Legacy protocols bypass MFA entirely.
  > Reference: [CIS Azure 1.1.3](https://www.cisecurity.org/benchmark/azure) | [Block legacy authentication](https://learn.microsoft.com/en-us/entra/identity/conditional-access/howto-conditional-access-policy-block-legacy)

- [ ] **Enable Privileged Identity Management (PIM)** `Severity: High`
  Configure PIM for all privileged roles so admins activate just-in-time access instead of holding permanent assignments. Reduces the window of exposure for admin credentials.
  > Reference: [CIS Azure 1.1.4](https://www.cisecurity.org/benchmark/azure) | [Entra PIM](https://learn.microsoft.com/en-us/entra/id-governance/privileged-identity-management/pim-configure)

- [ ] **Limit Global Administrators to fewer than 5** `Severity: High`
  Minimize the number of users with Global Administrator role. Use least-privilege roles (Security Admin, User Admin) for specific tasks instead.
  > Reference: [CIS Azure 1.1.5](https://www.cisecurity.org/benchmark/azure) | [Least privileged roles](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/delegate-by-task)

- [ ] **Configure Conditional Access to require compliant devices** `Severity: Medium`
  Require device compliance or Hybrid Azure AD Join for access to corporate resources. Prevents access from unmanaged or compromised devices.
  > Reference: [CIS Azure 1.1.6](https://www.cisecurity.org/benchmark/azure) | [Require compliant device](https://learn.microsoft.com/en-us/entra/identity/conditional-access/howto-conditional-access-policy-compliant-device)

- [ ] **Disable user consent to applications** `Severity: Medium`
  Set user consent to "Do not allow user consent" in Entra ID. Require admin approval for all app registrations to prevent OAuth consent phishing.
  > Reference: [CIS Azure 1.1.7](https://www.cisecurity.org/benchmark/azure) | [Configure user consent](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/configure-user-consent)

- [ ] **Enable self-service password reset with strong methods** `Severity: Medium`
  Configure SSPR with at least two authentication methods (phone + authenticator app). Reduces helpdesk load while maintaining security.
  > Reference: [CIS Azure 1.1.8](https://www.cisecurity.org/benchmark/azure) | [SSPR deployment](https://learn.microsoft.com/en-us/entra/identity/authentication/howto-sspr-deployment)

---

## Networking

- [ ] **Restrict RDP access (port 3389) from the internet** `Severity: Critical`
  Ensure no NSG rules allow inbound RDP from `0.0.0.0/0` or `*`. Use Azure Bastion or VPN for remote administration instead.
  > Reference: [CIS Azure 6.1](https://www.cisecurity.org/benchmark/azure) | [Azure Bastion](https://learn.microsoft.com/en-us/azure/bastion/bastion-overview)

- [ ] **Restrict SSH access (port 22) from the internet** `Severity: Critical`
  Ensure no NSG rules allow inbound SSH from any source. Limit SSH to known IP ranges or use Azure Bastion.
  > Reference: [CIS Azure 6.2](https://www.cisecurity.org/benchmark/azure) | [NSG security rules](https://learn.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview)

- [ ] **Enable Azure Firewall or third-party NVA** `Severity: High`
  Deploy a central firewall for east-west and north-south traffic inspection. Default NSGs only provide basic filtering.
  > Reference: [CIS Azure 6.3](https://www.cisecurity.org/benchmark/azure) | [Azure Firewall](https://learn.microsoft.com/en-us/azure/firewall/overview)

- [ ] **Enable Network Watcher** `Severity: High`
  Activate Network Watcher in all regions where you have resources. Required for NSG flow logs, packet capture, and connection troubleshooting.
  > Reference: [CIS Azure 6.4](https://www.cisecurity.org/benchmark/azure) | [Network Watcher](https://learn.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview)

- [ ] **Enable NSG Flow Logs** `Severity: High`
  Configure flow logs for all NSGs and send them to a Log Analytics workspace. Essential for traffic analysis and incident investigation.
  > Reference: [CIS Azure 6.5](https://www.cisecurity.org/benchmark/azure) | [NSG flow logs](https://learn.microsoft.com/en-us/azure/network-watcher/nsg-flow-logs-overview)

- [ ] **Use Private Endpoints for PaaS services** `Severity: Medium`
  Deploy Private Endpoints for Azure SQL, Storage, Key Vault, and other PaaS services to eliminate public internet exposure.
  > Reference: [Azure Private Link](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-overview)

- [ ] **Enable DDoS Protection on virtual networks** `Severity: Medium`
  Enable Azure DDoS Protection Standard on VNets hosting public-facing workloads. Basic protection only covers Azure infrastructure, not your applications.
  > Reference: [CIS Azure 6.6](https://www.cisecurity.org/benchmark/azure) | [DDoS Protection](https://learn.microsoft.com/en-us/azure/ddos-protection/ddos-protection-overview)

---

## Storage

- [ ] **Disable anonymous/public access to storage accounts** `Severity: Critical`
  Set "Allow Blob public access" to disabled on all storage accounts. Public blob containers are one of the most common causes of data leaks.
  > Reference: [CIS Azure 3.7](https://www.cisecurity.org/benchmark/azure) | [Prevent public access](https://learn.microsoft.com/en-us/azure/storage/blobs/anonymous-read-access-prevent)

- [ ] **Enable encryption in transit (HTTPS only)** `Severity: Critical`
  Set "Secure transfer required" to enabled on all storage accounts. Prevents data interception over unencrypted HTTP connections.
  > Reference: [CIS Azure 3.1](https://www.cisecurity.org/benchmark/azure) | [Require secure transfer](https://learn.microsoft.com/en-us/azure/storage/common/storage-require-secure-transfer)

- [ ] **Enable storage account encryption with CMK** `Severity: High`
  Use customer-managed keys (CMK) stored in Azure Key Vault for storage encryption instead of Microsoft-managed keys, especially for sensitive data.
  > Reference: [CIS Azure 3.2](https://www.cisecurity.org/benchmark/azure) | [Customer-managed keys](https://learn.microsoft.com/en-us/azure/storage/common/customer-managed-keys-overview)

- [ ] **Enable soft delete for blobs and containers** `Severity: Medium`
  Configure soft delete retention (minimum 7 days) to protect against accidental or malicious deletion of data.
  > Reference: [CIS Azure 3.8](https://www.cisecurity.org/benchmark/azure) | [Soft delete for blobs](https://learn.microsoft.com/en-us/azure/storage/blobs/soft-delete-blob-overview)

- [ ] **Restrict SAS token scope and expiration** `Severity: Medium`
  Use stored access policies with SAS tokens, limit permissions to minimum required, and set short expiration times. Avoid account-level SAS when service-level SAS suffices.
  > Reference: [CIS Azure 3.9](https://www.cisecurity.org/benchmark/azure) | [SAS best practices](https://learn.microsoft.com/en-us/azure/storage/common/storage-sas-overview)

- [ ] **Enable Azure Defender for Storage** `Severity: Medium`
  Activate Microsoft Defender for Storage to detect unusual access patterns, potential data exfiltration, and malware uploads.
  > Reference: [CIS Azure 3.10](https://www.cisecurity.org/benchmark/azure) | [Defender for Storage](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-storage-introduction)

- [ ] **Configure storage account network rules** `Severity: High`
  Set the default network access rule to "Deny" and whitelist only required VNets, subnets, and IP addresses.
  > Reference: [CIS Azure 3.6](https://www.cisecurity.org/benchmark/azure) | [Storage firewalls](https://learn.microsoft.com/en-us/azure/storage/common/storage-network-security)

---

## Compute

- [ ] **Enable Azure Disk Encryption on all VMs** `Severity: High`
  Encrypt OS and data disks using Azure Disk Encryption (ADE) with keys stored in Key Vault. Protects data at rest if disks are detached or copied.
  > Reference: [CIS Azure 7.1](https://www.cisecurity.org/benchmark/azure) | [Azure Disk Encryption](https://learn.microsoft.com/en-us/azure/virtual-machines/disk-encryption-overview)

- [ ] **Install and configure endpoint protection** `Severity: High`
  Deploy Microsoft Defender for Endpoint or equivalent EDR on all VMs. Ensure real-time protection and automatic sample submission are enabled.
  > Reference: [CIS Azure 7.2](https://www.cisecurity.org/benchmark/azure) | [Defender for Endpoint](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint)

- [ ] **Enable automatic OS patching** `Severity: High`
  Configure automatic VM guest patching or Azure Update Manager to ensure security patches are applied promptly. Unpatched VMs are easy targets.
  > Reference: [CIS Azure 7.3](https://www.cisecurity.org/benchmark/azure) | [Automatic VM guest patching](https://learn.microsoft.com/en-us/azure/virtual-machines/automatic-vm-guest-patching)

- [ ] **Disable public IP addresses on VMs when not required** `Severity: Medium`
  Remove public IPs from VMs that don't need direct internet access. Use Azure Bastion, Load Balancer, or NAT Gateway instead.
  > Reference: [CIS Azure 7.4](https://www.cisecurity.org/benchmark/azure) | [Azure Bastion](https://learn.microsoft.com/en-us/azure/bastion/bastion-overview)

- [ ] **Enable Just-In-Time (JIT) VM access** `Severity: Medium`
  Configure JIT access through Microsoft Defender for Cloud to lock down inbound management ports and open them only when needed with time-limited approval.
  > Reference: [CIS Azure 7.5](https://www.cisecurity.org/benchmark/azure) | [JIT VM access](https://learn.microsoft.com/en-us/azure/defender-for-cloud/just-in-time-access-usage)

- [ ] **Use managed disks for all VMs** `Severity: Medium`
  Ensure all VMs use managed disks instead of unmanaged (VHD) disks. Managed disks provide better reliability, encryption options, and RBAC support.
  > Reference: [CIS Azure 7.6](https://www.cisecurity.org/benchmark/azure) | [Managed disks](https://learn.microsoft.com/en-us/azure/virtual-machines/managed-disks-overview)

---

## Database

- [ ] **Enable Transparent Data Encryption (TDE) on SQL databases** `Severity: High`
  Ensure TDE is enabled on all Azure SQL databases. TDE is enabled by default on new databases, but verify it hasn't been disabled on existing ones.
  > Reference: [CIS Azure 4.1.1](https://www.cisecurity.org/benchmark/azure) | [SQL TDE](https://learn.microsoft.com/en-us/azure/azure-sql/database/transparent-data-encryption-tde-overview)

- [ ] **Enable Azure SQL auditing** `Severity: High`
  Configure auditing on all Azure SQL servers to track database events. Send audit logs to a Log Analytics workspace or storage account with appropriate retention.
  > Reference: [CIS Azure 4.1.2](https://www.cisecurity.org/benchmark/azure) | [SQL auditing](https://learn.microsoft.com/en-us/azure/azure-sql/database/auditing-overview)

- [ ] **Configure Azure SQL firewall rules** `Severity: High`
  Disable "Allow Azure services and resources to access this server" and restrict access to specific IP ranges or VNet rules.
  > Reference: [CIS Azure 4.1.3](https://www.cisecurity.org/benchmark/azure) | [SQL firewall rules](https://learn.microsoft.com/en-us/azure/azure-sql/database/firewall-configure)

- [ ] **Enable Advanced Threat Protection for SQL** `Severity: Medium`
  Activate Microsoft Defender for SQL to detect anomalous activities, SQL injection attempts, and suspicious access patterns.
  > Reference: [CIS Azure 4.1.4](https://www.cisecurity.org/benchmark/azure) | [Defender for SQL](https://learn.microsoft.com/en-us/azure/azure-sql/database/azure-defender-for-sql)

- [ ] **Disable public network access for Cosmos DB** `Severity: High`
  Use Private Endpoints for Cosmos DB access and disable public network access. Database endpoints should never be internet-facing.
  > Reference: [CIS Azure 4.5.1](https://www.cisecurity.org/benchmark/azure) | [Cosmos DB network security](https://learn.microsoft.com/en-us/azure/cosmos-db/how-to-configure-private-endpoints)

- [ ] **Enable automatic backups and configure retention** `Severity: Medium`
  Verify that automated backups are configured with appropriate retention periods (minimum 7 days for short-term, geo-redundant for disaster recovery).
  > Reference: [CIS Azure 4.1.5](https://www.cisecurity.org/benchmark/azure) | [SQL backup](https://learn.microsoft.com/en-us/azure/azure-sql/database/automated-backups-overview)

---

## Logging & Monitoring

- [ ] **Enable Azure Activity Log collection** `Severity: Critical`
  Configure a diagnostic setting to send Activity Logs to a Log Analytics workspace. Activity Logs record all control plane operations across your subscription.
  > Reference: [CIS Azure 5.1.1](https://www.cisecurity.org/benchmark/azure) | [Activity Log](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/activity-log)

- [ ] **Create alerts for critical operations** `Severity: High`
  Configure alerts for security-sensitive operations: policy assignment changes, NSG changes, SQL firewall rule changes, Key Vault access policy changes, and security solution modifications.
  > Reference: [CIS Azure 5.2.1](https://www.cisecurity.org/benchmark/azure) | [Activity Log alerts](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-activity-log)

- [ ] **Enable diagnostic settings on all critical resources** `Severity: High`
  Configure diagnostic settings for Key Vault, NSGs, App Services, SQL databases, and other critical resources. Without this, you have no visibility into resource-level operations.
  > Reference: [CIS Azure 5.1.2](https://www.cisecurity.org/benchmark/azure) | [Diagnostic settings](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/diagnostic-settings)

- [ ] **Enable Microsoft Defender for Cloud** `Severity: High`
  Activate Defender for Cloud on all subscriptions with at least the CSPM (free) tier. Enable enhanced security features for critical workload types.
  > Reference: [CIS Azure 2.1.1](https://www.cisecurity.org/benchmark/azure) | [Defender for Cloud](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-cloud-introduction)

- [ ] **Configure log retention to meet compliance requirements** `Severity: Medium`
  Set Log Analytics workspace retention to at least 90 days (365 days recommended). Ensure retention meets your regulatory requirements (SOC 2, ISO 27001, etc.).
  > Reference: [CIS Azure 5.1.3](https://www.cisecurity.org/benchmark/azure) | [Log retention](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/data-retention-configure)

- [ ] **Enable Azure Sentinel or equivalent SIEM** `Severity: Medium`
  Deploy Microsoft Sentinel (or a third-party SIEM) to aggregate logs, detect threats, and automate incident response across your Azure environment.
  > Reference: [Microsoft Sentinel](https://learn.microsoft.com/en-us/azure/sentinel/overview)

---

## Key Vault

- [ ] **Enable soft delete and purge protection** `Severity: Critical`
  Enable both soft delete (default) and purge protection on all Key Vaults. Purge protection prevents permanent deletion of secrets even by administrators during the retention period.
  > Reference: [CIS Azure 8.1](https://www.cisecurity.org/benchmark/azure) | [Key Vault soft delete](https://learn.microsoft.com/en-us/azure/key-vault/general/soft-delete-overview)

- [ ] **Use RBAC authorization instead of access policies** `Severity: High`
  Switch Key Vault permission model from access policies to Azure RBAC. RBAC provides finer-grained access control and integrates with Entra ID Conditional Access.
  > Reference: [CIS Azure 8.2](https://www.cisecurity.org/benchmark/azure) | [Key Vault RBAC](https://learn.microsoft.com/en-us/azure/key-vault/general/rbac-guide)

- [ ] **Enable Key Vault logging** `Severity: High`
  Configure diagnostic settings to log all Key Vault access and operations to a Log Analytics workspace. Essential for detecting unauthorized secret access.
  > Reference: [CIS Azure 8.3](https://www.cisecurity.org/benchmark/azure) | [Key Vault logging](https://learn.microsoft.com/en-us/azure/key-vault/general/logging)

- [ ] **Configure key expiration and rotation** `Severity: High`
  Set expiration dates on all keys and secrets. Configure automatic key rotation where supported. Expired credentials should trigger alerts, not outages.
  > Reference: [CIS Azure 8.4](https://www.cisecurity.org/benchmark/azure) | [Key rotation](https://learn.microsoft.com/en-us/azure/key-vault/keys/how-to-configure-key-rotation)

- [ ] **Restrict Key Vault network access** `Severity: Medium`
  Configure Key Vault firewall to deny public access by default. Allow access only from specific VNets, subnets, or trusted Azure services.
  > Reference: [CIS Azure 8.5](https://www.cisecurity.org/benchmark/azure) | [Key Vault networking](https://learn.microsoft.com/en-us/azure/key-vault/general/network-security)

- [ ] **Use separate Key Vaults per environment** `Severity: Medium`
  Maintain separate Key Vaults for development, staging, and production. Prevents accidental cross-environment secret access and limits blast radius.
  > Reference: [Key Vault best practices](https://learn.microsoft.com/en-us/azure/key-vault/general/best-practices)

---

[Back to main checklist](../README.md)
