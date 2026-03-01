# Microsoft 365 Security Checklist

A security hardening checklist for Microsoft 365 and Entra ID environments, based on [CIS Microsoft 365 Foundations Benchmark v3.1](https://www.cisecurity.org/benchmark/microsoft_365) and real-world tenant hardening experience.

[Back to main checklist](../README.md)

---

## Entra ID (Azure AD)

- [ ] **Enable Security Defaults or Conditional Access** `Severity: Critical`
  At minimum, enable Security Defaults. For organizations with Entra ID P1/P2, replace Security Defaults with Conditional Access policies for granular control.
  > Reference: [CIS M365 1.1.1](https://www.cisecurity.org/benchmark/microsoft_365) | [Security Defaults](https://learn.microsoft.com/en-us/entra/fundamentals/security-defaults)

- [ ] **Require MFA for all users** `Severity: Critical`
  Create a Conditional Access policy targeting all users, all cloud apps, requiring MFA. Exclude break-glass accounts (which must have FIDO2 keys).
  > Reference: [CIS M365 1.1.2](https://www.cisecurity.org/benchmark/microsoft_365) | [Require MFA for all users](https://learn.microsoft.com/en-us/entra/identity/conditional-access/howto-conditional-access-policy-all-users-mfa)

- [ ] **Block legacy authentication** `Severity: Critical`
  Create a Conditional Access policy blocking legacy authentication clients (Exchange ActiveSync, IMAP, POP3, SMTP Auth). Legacy protocols cannot enforce MFA.
  > Reference: [CIS M365 1.1.3](https://www.cisecurity.org/benchmark/microsoft_365) | [Block legacy auth](https://learn.microsoft.com/en-us/entra/identity/conditional-access/howto-conditional-access-policy-block-legacy)

- [ ] **Configure break-glass emergency access accounts** `Severity: Critical`
  Create at least two cloud-only emergency access accounts with Global Admin role, excluded from Conditional Access, secured with FIDO2 keys, and monitored for sign-in activity.
  > Reference: [CIS M365 1.1.4](https://www.cisecurity.org/benchmark/microsoft_365) | [Emergency access accounts](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/security-emergency-access)

- [ ] **Enable Entra ID Identity Protection** `Severity: High`
  Configure user risk and sign-in risk policies. Set user risk policy to require password change at medium risk. Set sign-in risk policy to require MFA at medium risk.
  > Reference: [CIS M365 1.1.5](https://www.cisecurity.org/benchmark/microsoft_365) | [Identity Protection](https://learn.microsoft.com/en-us/entra/id-protection/overview-identity-protection)

- [ ] **Restrict user consent to applications** `Severity: High`
  Set "Users can consent to apps" to "No" or limit to verified publishers only. Require admin approval for all application registrations.
  > Reference: [CIS M365 1.1.6](https://www.cisecurity.org/benchmark/microsoft_365) | [Configure consent](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/configure-user-consent)

- [ ] **Enable sign-in risk-based Conditional Access** `Severity: High`
  Create a policy that blocks or requires MFA for high-risk sign-ins (unfamiliar locations, impossible travel, anonymous IP addresses).
  > Reference: [CIS M365 1.1.7](https://www.cisecurity.org/benchmark/microsoft_365) | [Risk-based Conditional Access](https://learn.microsoft.com/en-us/entra/identity/conditional-access/howto-conditional-access-policy-risk)

- [ ] **Disable user registration of applications** `Severity: Medium`
  Set "Users can register applications" to "No" in Entra ID settings. Prevents users from creating app registrations that could be used for data exfiltration.
  > Reference: [CIS M365 1.1.8](https://www.cisecurity.org/benchmark/microsoft_365) | [App registration](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/delegate-app-roles)

---

## Exchange Online

- [ ] **Enable DKIM signing for all domains** `Severity: High`
  Configure DomainKeys Identified Mail (DKIM) for every accepted domain in Exchange Online. DKIM proves email authenticity and prevents spoofing.
  > Reference: [CIS M365 2.1.1](https://www.cisecurity.org/benchmark/microsoft_365) | [DKIM setup](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dkim-configure)

- [ ] **Configure DMARC with reject policy** `Severity: High`
  Publish a DMARC record with `p=reject` for all domains. Start with `p=none` to monitor, then move to `quarantine`, then `reject` once legitimate senders are aligned.
  > Reference: [CIS M365 2.1.2](https://www.cisecurity.org/benchmark/microsoft_365) | [DMARC](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-dmarc-configure)

- [ ] **Publish SPF records for all domains** `Severity: High`
  Configure SPF TXT records for every domain. Include `include:spf.protection.outlook.com` and set `-all` (hard fail) to reject unauthorized senders.
  > Reference: [CIS M365 2.1.3](https://www.cisecurity.org/benchmark/microsoft_365) | [SPF setup](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/email-authentication-spf-configure)

- [ ] **Disable automatic email forwarding to external domains** `Severity: Critical`
  Create a transport rule or use the outbound anti-spam policy to block automatic forwarding to external domains. Attackers set up forwarding rules after compromising accounts.
  > Reference: [CIS M365 2.1.4](https://www.cisecurity.org/benchmark/microsoft_365) | [Block forwarding](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/outbound-spam-policies-external-email-forwarding)

- [ ] **Enable audit logging for all mailboxes** `Severity: High`
  Verify that mailbox audit logging is enabled (on by default since 2019) and configured to capture MailboxLogin, Send, and HardDelete events for owner, delegate, and admin.
  > Reference: [CIS M365 2.1.5](https://www.cisecurity.org/benchmark/microsoft_365) | [Mailbox auditing](https://learn.microsoft.com/en-us/purview/audit-mailboxes)

- [ ] **Enable Safe Attachments and Safe Links** `Severity: High`
  Configure Microsoft Defender for Office 365 Safe Attachments (detonation sandbox) and Safe Links (URL rewriting and time-of-click verification).
  > Reference: [CIS M365 2.1.6](https://www.cisecurity.org/benchmark/microsoft_365) | [Safe Attachments](https://learn.microsoft.com/en-us/microsoft-365/security/office-365-security/safe-attachments-about)

- [ ] **Review and limit mail flow rules** `Severity: Medium`
  Audit all transport rules regularly. Look for rules that bypass spam filtering, redirect mail externally, or modify message headers in suspicious ways.
  > Reference: [CIS M365 2.1.7](https://www.cisecurity.org/benchmark/microsoft_365) | [Mail flow rules](https://learn.microsoft.com/en-us/exchange/security-and-compliance/mail-flow-rules/mail-flow-rules)

---

## SharePoint & OneDrive

- [ ] **Restrict external sharing to specific domains** `Severity: High`
  Configure SharePoint sharing to allow external sharing only with specific, approved domains. Do not allow sharing with "Anyone" (anonymous links).
  > Reference: [CIS M365 3.1.1](https://www.cisecurity.org/benchmark/microsoft_365) | [Sharing settings](https://learn.microsoft.com/en-us/sharepoint/turn-external-sharing-on-or-off)

- [ ] **Set external sharing link expiration** `Severity: High`
  Configure default expiration for "Anyone" links (if allowed) and guest access links. Maximum 30 days for guest links, 7 days for anonymous links.
  > Reference: [CIS M365 3.1.2](https://www.cisecurity.org/benchmark/microsoft_365) | [Link expiration](https://learn.microsoft.com/en-us/sharepoint/change-default-sharing-link)

- [ ] **Enable DLP policies for sensitive content** `Severity: High`
  Create Data Loss Prevention policies to detect and protect sensitive information (credit cards, SSNs, health records) shared via SharePoint, OneDrive, and Teams.
  > Reference: [CIS M365 3.1.3](https://www.cisecurity.org/benchmark/microsoft_365) | [DLP policies](https://learn.microsoft.com/en-us/purview/dlp-learn-about-dlp)

- [ ] **Disable sync for unmanaged devices** `Severity: Medium`
  Block OneDrive sync client on devices that are not domain-joined or Intune-compliant. Prevents corporate data from syncing to personal devices.
  > Reference: [CIS M365 3.1.4](https://www.cisecurity.org/benchmark/microsoft_365) | [Sync restrictions](https://learn.microsoft.com/en-us/sharepoint/allow-syncing-only-on-specific-domains)

- [ ] **Enable sensitivity labels for documents** `Severity: Medium`
  Deploy Microsoft Purview sensitivity labels to classify and protect documents. Configure automatic labeling for content containing sensitive data patterns.
  > Reference: [CIS M365 3.1.5](https://www.cisecurity.org/benchmark/microsoft_365) | [Sensitivity labels](https://learn.microsoft.com/en-us/purview/sensitivity-labels)

- [ ] **Monitor external user access regularly** `Severity: Medium`
  Conduct quarterly reviews of external users with access to SharePoint sites. Remove access for users who no longer need it using access reviews in Entra ID Governance.
  > Reference: [Entra access reviews](https://learn.microsoft.com/en-us/entra/id-governance/access-reviews-overview)

---

## Microsoft Teams

- [ ] **Configure guest access policies** `Severity: High`
  Review and restrict guest capabilities in Teams. Disable guest ability to add apps, create or update channels, and delete messages unless business-required.
  > Reference: [CIS M365 4.1.1](https://www.cisecurity.org/benchmark/microsoft_365) | [Guest access](https://learn.microsoft.com/en-us/microsoftteams/guest-access)

- [ ] **Restrict external domain communication** `Severity: Medium`
  Configure external access to allow communication only with specific, approved domains rather than "Open federation" with all Teams/Skype organizations.
  > Reference: [CIS M365 4.1.2](https://www.cisecurity.org/benchmark/microsoft_365) | [External access](https://learn.microsoft.com/en-us/microsoftteams/manage-external-access)

- [ ] **Configure meeting policies for external participants** `Severity: Medium`
  Restrict anonymous users from joining meetings. Require authenticated sign-in and configure lobby settings so organizers can admit external participants.
  > Reference: [CIS M365 4.1.3](https://www.cisecurity.org/benchmark/microsoft_365) | [Meeting policies](https://learn.microsoft.com/en-us/microsoftteams/meeting-policies-participants-and-guests)

- [ ] **Restrict third-party app installation** `Severity: Medium`
  Control which third-party apps users can install in Teams. Allow only IT-approved apps from the Teams app store. Block sideloading of custom apps.
  > Reference: [CIS M365 4.1.4](https://www.cisecurity.org/benchmark/microsoft_365) | [App permission policies](https://learn.microsoft.com/en-us/microsoftteams/teams-app-permission-policies)

- [ ] **Enable meeting recording consent and storage controls** `Severity: Low`
  Configure meeting recording policies to store recordings in OneDrive/SharePoint (not Stream Classic). Set appropriate access controls and expiration on recordings.
  > Reference: [CIS M365 4.1.5](https://www.cisecurity.org/benchmark/microsoft_365) | [Meeting recordings](https://learn.microsoft.com/en-us/microsoftteams/meeting-recording)

---

## Compliance & Data Protection

- [ ] **Enable Unified Audit Log** `Severity: Critical`
  Verify that unified audit logging is enabled in the Purview compliance portal. Required for investigating security incidents and meeting compliance requirements.
  > Reference: [CIS M365 5.1.1](https://www.cisecurity.org/benchmark/microsoft_365) | [Audit log](https://learn.microsoft.com/en-us/purview/audit-log-enable-disable)

- [ ] **Configure audit log retention** `Severity: High`
  Set audit log retention to at least 180 days (365 days recommended). E5 licenses provide 1-year retention; use audit retention policies for longer periods.
  > Reference: [CIS M365 5.1.2](https://www.cisecurity.org/benchmark/microsoft_365) | [Audit retention](https://learn.microsoft.com/en-us/purview/audit-log-retention-policies)

- [ ] **Deploy retention policies for critical workloads** `Severity: High`
  Create retention policies for Exchange, SharePoint, OneDrive, and Teams to prevent premature data deletion and meet regulatory requirements.
  > Reference: [CIS M365 5.1.3](https://www.cisecurity.org/benchmark/microsoft_365) | [Retention policies](https://learn.microsoft.com/en-us/purview/retention-policies-sharepoint)

- [ ] **Enable Microsoft Purview Information Protection** `Severity: Medium`
  Deploy sensitivity labels with encryption and rights management for documents containing confidential or regulated data.
  > Reference: [CIS M365 5.1.4](https://www.cisecurity.org/benchmark/microsoft_365) | [Information Protection](https://learn.microsoft.com/en-us/purview/information-protection)

- [ ] **Configure alert policies for suspicious activity** `Severity: High`
  Review and enable built-in alert policies for: unusual volume of file deletion, mass download, sharing activity anomalies, and elevation of privilege.
  > Reference: [CIS M365 5.1.5](https://www.cisecurity.org/benchmark/microsoft_365) | [Alert policies](https://learn.microsoft.com/en-us/purview/alert-policies)

- [ ] **Enable eDiscovery hold for legal requirements** `Severity: Medium`
  When litigation hold or regulatory preservation is required, configure eDiscovery holds to ensure relevant content is preserved and cannot be deleted by users.
  > Reference: [eDiscovery](https://learn.microsoft.com/en-us/purview/ediscovery-overview)

---

[Back to main checklist](../README.md)
