# AWS Security Checklist

A security hardening checklist for Amazon Web Services environments, based on [CIS Amazon Web Services Foundations Benchmark v3.0](https://www.cisecurity.org/benchmark/amazon_web_services).

[Back to main checklist](../README.md)

---

## IAM

- [ ] **Enable MFA on the root account** `Severity: Critical`
  The root account has unrestricted access to all resources. Enable hardware MFA (not virtual) on the root account and store the MFA device securely.
  > Reference: [CIS AWS 1.5](https://www.cisecurity.org/benchmark/amazon_web_services) | [Root account MFA](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user_manage_mfa.html)

- [ ] **Do not use the root account for daily operations** `Severity: Critical`
  After initial setup, lock the root account credentials. Create individual IAM users or use AWS IAM Identity Center (SSO) for all administrative tasks.
  > Reference: [CIS AWS 1.7](https://www.cisecurity.org/benchmark/amazon_web_services) | [Root account best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html)

- [ ] **Enable MFA for all IAM users with console access** `Severity: Critical`
  Require MFA for every IAM user that can sign in to the AWS Management Console. Use an SCP to enforce MFA at the organization level.
  > Reference: [CIS AWS 1.10](https://www.cisecurity.org/benchmark/amazon_web_services) | [MFA setup](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa_enable.html)

- [ ] **Eliminate use of long-term access keys** `Severity: High`
  Remove static access keys where possible. Use IAM roles with temporary credentials for EC2, Lambda, and other services. For human users, use SSO instead.
  > Reference: [CIS AWS 1.12](https://www.cisecurity.org/benchmark/amazon_web_services) | [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)

- [ ] **Rotate access keys within 90 days** `Severity: High`
  For any remaining access keys, configure rotation within 90 days. Use AWS Config rule `access-keys-rotated` to monitor compliance.
  > Reference: [CIS AWS 1.14](https://www.cisecurity.org/benchmark/amazon_web_services) | [Rotating access keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html#Using_RotateAccessKey)

- [ ] **Attach IAM policies to groups or roles, not users** `Severity: Medium`
  Avoid attaching policies directly to IAM users. Use groups for human users and roles for services. Simplifies permission management and auditing.
  > Reference: [CIS AWS 1.15](https://www.cisecurity.org/benchmark/amazon_web_services) | [IAM best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

- [ ] **Enable IAM Access Analyzer** `Severity: Medium`
  Activate IAM Access Analyzer in all regions to identify resources shared with external entities. Review and remediate findings regularly.
  > Reference: [CIS AWS 1.20](https://www.cisecurity.org/benchmark/amazon_web_services) | [IAM Access Analyzer](https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html)

- [ ] **Configure a strong IAM password policy** `Severity: Medium`
  Set minimum length to 14 characters, require uppercase, lowercase, numbers, and symbols. Enable password expiration at 90 days.
  > Reference: [CIS AWS 1.8](https://www.cisecurity.org/benchmark/amazon_web_services) | [Password policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_passwords_account-policy.html)

---

## S3

- [ ] **Block all public access at the account level** `Severity: Critical`
  Enable S3 Block Public Access settings at the AWS account level. This overrides any bucket-level policies that might grant public access.
  > Reference: [CIS AWS 2.1.4](https://www.cisecurity.org/benchmark/amazon_web_services) | [Block public access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html)

- [ ] **Enable server-side encryption on all buckets** `Severity: High`
  Configure default encryption (SSE-S3 minimum, SSE-KMS recommended) on all S3 buckets. Use bucket policies to deny unencrypted uploads.
  > Reference: [CIS AWS 2.1.1](https://www.cisecurity.org/benchmark/amazon_web_services) | [S3 encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html)

- [ ] **Enable S3 bucket versioning** `Severity: High`
  Enable versioning on all buckets containing important data. Versioning protects against accidental deletion and provides a recovery mechanism.
  > Reference: [CIS AWS 2.1.3](https://www.cisecurity.org/benchmark/amazon_web_services) | [S3 versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html)

- [ ] **Enable S3 access logging** `Severity: High`
  Configure server access logging for all S3 buckets to a centralized logging bucket. Required for audit trails and breach investigation.
  > Reference: [CIS AWS 2.1.2](https://www.cisecurity.org/benchmark/amazon_web_services) | [S3 access logging](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerLogs.html)

- [ ] **Enforce encryption in transit** `Severity: High`
  Add a bucket policy to deny requests that don't use `aws:SecureTransport`. Ensures all S3 traffic uses TLS.
  > Reference: [CIS AWS 2.1.5](https://www.cisecurity.org/benchmark/amazon_web_services) | [Enforce TLS](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)

- [ ] **Enable MFA Delete on critical buckets** `Severity: Medium`
  Enable MFA Delete on buckets containing sensitive or compliance data. Prevents unauthorized deletion even with compromised credentials.
  > Reference: [CIS AWS 2.1.6](https://www.cisecurity.org/benchmark/amazon_web_services) | [MFA Delete](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiFactorAuthenticationDelete.html)

- [ ] **Configure lifecycle policies** `Severity: Low`
  Set lifecycle rules to transition old objects to cheaper storage classes and expire objects that are no longer needed. Reduces cost and attack surface.
  > Reference: [S3 lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)

---

## EC2

- [ ] **Restrict security group inbound rules** `Severity: Critical`
  Ensure no security group allows unrestricted inbound access (0.0.0.0/0) to administrative ports (22, 3389). Use Systems Manager Session Manager or a bastion host.
  > Reference: [CIS AWS 5.2](https://www.cisecurity.org/benchmark/amazon_web_services) | [Security groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)

- [ ] **Require IMDSv2 on all instances** `Severity: Critical`
  Configure all EC2 instances to require Instance Metadata Service v2 (IMDSv2). IMDSv1 is vulnerable to SSRF attacks that can steal IAM role credentials.
  > Reference: [CIS AWS 5.6](https://www.cisecurity.org/benchmark/amazon_web_services) | [IMDSv2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html)

- [ ] **Enable EBS encryption by default** `Severity: High`
  Enable default EBS encryption at the account level in each region. All new volumes and snapshots will be automatically encrypted.
  > Reference: [CIS AWS 2.2.1](https://www.cisecurity.org/benchmark/amazon_web_services) | [EBS encryption](https://docs.aws.amazon.com/ebs/latest/userguide/encryption-by-default.html)

- [ ] **Use IAM roles instead of access keys on instances** `Severity: High`
  Attach IAM roles (instance profiles) to EC2 instances instead of storing access keys on them. Roles provide automatic credential rotation.
  > Reference: [CIS AWS 1.16](https://www.cisecurity.org/benchmark/amazon_web_services) | [Instance profiles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html)

- [ ] **Enable detailed monitoring for production instances** `Severity: Medium`
  Enable detailed monitoring (1-minute intervals) on production EC2 instances for timely anomaly detection and alerting.
  > Reference: [EC2 monitoring](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-cloudwatch.html)

- [ ] **Remove unused security groups** `Severity: Medium`
  Regularly audit and delete security groups that are not attached to any resource. Unused security groups can be misconfigured and later attached to new resources.
  > Reference: [Security group best practices](https://docs.aws.amazon.com/vpc/latest/userguide/security-group-rules.html)

- [ ] **Use AWS Systems Manager for patch management** `Severity: Medium`
  Configure SSM Patch Manager to automatically apply security patches. Define patch baselines and maintenance windows for all managed instances.
  > Reference: [SSM Patch Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-patch.html)

---

## RDS

- [ ] **Enable encryption at rest for all RDS instances** `Severity: High`
  Enable encryption when creating RDS instances (cannot be enabled after creation without recreating the instance). Use KMS CMK for sensitive workloads.
  > Reference: [CIS AWS 2.3.1](https://www.cisecurity.org/benchmark/amazon_web_services) | [RDS encryption](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html)

- [ ] **Disable public accessibility on RDS instances** `Severity: Critical`
  Set "Publicly accessible" to "No" on all RDS instances. Place databases in private subnets with no internet gateway route.
  > Reference: [CIS AWS 2.3.2](https://www.cisecurity.org/benchmark/amazon_web_services) | [RDS network](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html)

- [ ] **Enable automated backups with adequate retention** `Severity: High`
  Configure automated backups with at least 7 days retention. Enable cross-region backup replication for disaster recovery.
  > Reference: [CIS AWS 2.3.3](https://www.cisecurity.org/benchmark/amazon_web_services) | [RDS backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)

- [ ] **Enable enhanced monitoring** `Severity: Medium`
  Enable Enhanced Monitoring to get real-time OS metrics. Helps identify performance issues and potential security anomalies.
  > Reference: [RDS monitoring](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.html)

- [ ] **Enable deletion protection** `Severity: Medium`
  Enable deletion protection on all production RDS instances to prevent accidental or unauthorized database deletion.
  > Reference: [RDS deletion protection](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_DeleteInstance.html)

- [ ] **Use IAM authentication where possible** `Severity: Medium`
  Enable IAM database authentication for MySQL and PostgreSQL. Eliminates the need to store database passwords and provides centralized access control.
  > Reference: [RDS IAM auth](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.IAMDBAuth.html)

---

## CloudTrail

- [ ] **Enable CloudTrail in all regions** `Severity: Critical`
  Create a multi-region trail that logs management events across all AWS regions. Attackers often operate in regions you're not watching.
  > Reference: [CIS AWS 3.1](https://www.cisecurity.org/benchmark/amazon_web_services) | [CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)

- [ ] **Enable CloudTrail log file validation** `Severity: High`
  Turn on log file integrity validation to detect tampering. Validates that log files haven't been modified or deleted after delivery.
  > Reference: [CIS AWS 3.2](https://www.cisecurity.org/benchmark/amazon_web_services) | [Log validation](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-log-file-validation-intro.html)

- [ ] **Enable CloudTrail log encryption** `Severity: High`
  Encrypt CloudTrail logs using a KMS CMK. Default S3 encryption (SSE-S3) is insufficient for compliance requirements.
  > Reference: [CIS AWS 3.5](https://www.cisecurity.org/benchmark/amazon_web_services) | [CloudTrail encryption](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/encrypting-cloudtrail-log-files-with-aws-kms.html)

- [ ] **Integrate CloudTrail with CloudWatch Logs** `Severity: High`
  Send CloudTrail events to CloudWatch Logs for real-time monitoring and alerting. Configure metric filters for unauthorized API calls and console sign-in failures.
  > Reference: [CIS AWS 3.4](https://www.cisecurity.org/benchmark/amazon_web_services) | [CloudTrail CloudWatch](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/send-cloudtrail-events-to-cloudwatch-logs.html)

- [ ] **Ensure CloudTrail S3 bucket is not public** `Severity: Critical`
  Verify the S3 bucket receiving CloudTrail logs has no public access. A public trail bucket exposes your entire API activity history.
  > Reference: [CIS AWS 3.3](https://www.cisecurity.org/benchmark/amazon_web_services) | [CloudTrail S3 security](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/best-practices-security.html)

- [ ] **Enable data event logging for S3 and Lambda** `Severity: Medium`
  Configure CloudTrail to log data events (object-level operations) for sensitive S3 buckets and Lambda function invocations.
  > Reference: [CIS AWS 3.6](https://www.cisecurity.org/benchmark/amazon_web_services) | [Data events](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html)

---

## VPC

- [ ] **Enable VPC Flow Logs** `Severity: High`
  Enable flow logs on all VPCs and send them to CloudWatch Logs or S3. Flow logs are critical for network traffic analysis and incident response.
  > Reference: [CIS AWS 3.7](https://www.cisecurity.org/benchmark/amazon_web_services) | [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)

- [ ] **Restrict default security group to deny all traffic** `Severity: High`
  Modify the default security group in every VPC to have no inbound or outbound rules. Resources accidentally associated with the default SG should have no access.
  > Reference: [CIS AWS 5.4](https://www.cisecurity.org/benchmark/amazon_web_services) | [Default security group](https://docs.aws.amazon.com/vpc/latest/userguide/default-security-group.html)

- [ ] **Use NACLs as an additional layer of defense** `Severity: Medium`
  Configure Network ACLs on subnets containing sensitive resources. NACLs provide stateless filtering as a second layer beyond security groups.
  > Reference: [CIS AWS 5.1](https://www.cisecurity.org/benchmark/amazon_web_services) | [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html)

- [ ] **Do not use the default VPC** `Severity: Medium`
  Create custom VPCs for all workloads. The default VPC has overly permissive settings (public subnets, internet gateway) that violate least-privilege.
  > Reference: [CIS AWS 5.3](https://www.cisecurity.org/benchmark/amazon_web_services) | [Default VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html)

- [ ] **Use VPC Endpoints for AWS services** `Severity: Medium`
  Deploy Gateway and Interface VPC Endpoints for S3, DynamoDB, and other AWS services. Keeps traffic within the AWS network and eliminates need for NAT Gateway for these services.
  > Reference: [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints.html)

- [ ] **Implement proper subnet segmentation** `Severity: Medium`
  Separate public, private, and data subnets. Only place resources that require direct internet access in public subnets.
  > Reference: [VPC subnets](https://docs.aws.amazon.com/vpc/latest/userguide/configure-subnets.html)

---

## KMS

- [ ] **Enable automatic key rotation** `Severity: High`
  Enable automatic annual rotation for all customer-managed KMS keys. AWS manages the key material rotation transparently.
  > Reference: [CIS AWS 3.8](https://www.cisecurity.org/benchmark/amazon_web_services) | [KMS key rotation](https://docs.aws.amazon.com/kms/latest/developerguide/rotate-keys.html)

- [ ] **Restrict KMS key policies** `Severity: High`
  Review key policies to ensure only authorized principals can use, manage, or grant access to KMS keys. Avoid wildcard principals in key policies.
  > Reference: [CIS AWS 3.9](https://www.cisecurity.org/benchmark/amazon_web_services) | [Key policies](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html)

- [ ] **Use separate keys per workload** `Severity: Medium`
  Create dedicated KMS keys for different workloads and data classifications. Allows independent access control, rotation, and audit trails per key.
  > Reference: [KMS best practices](https://docs.aws.amazon.com/kms/latest/developerguide/best-practices.html)

- [ ] **Monitor KMS key usage with CloudTrail** `Severity: Medium`
  Ensure all KMS API calls are logged via CloudTrail. Set up CloudWatch alarms for sensitive operations like `DisableKey`, `ScheduleKeyDeletion`, and `PutKeyPolicy`.
  > Reference: [KMS logging](https://docs.aws.amazon.com/kms/latest/developerguide/logging-using-cloudtrail.html)

- [ ] **Review grants and cross-account access regularly** `Severity: Medium`
  Audit KMS grants and cross-account key sharing quarterly. Remove grants that are no longer needed and verify cross-account access is still authorized.
  > Reference: [KMS grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html)

---

[Back to main checklist](../README.md)
