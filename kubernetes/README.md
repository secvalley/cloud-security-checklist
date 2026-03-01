# Kubernetes Security Checklist

A security hardening checklist for Kubernetes clusters, applicable to managed services (AKS, EKS, GKE) and self-managed deployments. Based on [CIS Kubernetes Benchmark v1.8](https://www.cisecurity.org/benchmark/kubernetes) and NSA/CISA Kubernetes Hardening Guide.

[Back to main checklist](../README.md)

---

## Pod Security

- [ ] **Do not run containers as root** `Severity: Critical`
  Set `runAsNonRoot: true` and specify a `runAsUser` in the pod security context. Root containers can escape to the host if a container breakout vulnerability exists.
  > Reference: [CIS K8s 5.2.6](https://www.cisecurity.org/benchmark/kubernetes) | [Pod Security Context](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

- [ ] **Drop all capabilities and add only required ones** `Severity: Critical`
  Set `capabilities: { drop: ["ALL"] }` in the container security context. Add back only specific capabilities needed (e.g., `NET_BIND_SERVICE`).
  > Reference: [CIS K8s 5.2.7](https://www.cisecurity.org/benchmark/kubernetes) | [Linux capabilities](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#set-capabilities-for-a-container)

- [ ] **Set readOnlyRootFilesystem to true** `Severity: High`
  Mount the container root filesystem as read-only. Use emptyDir or named volumes for directories that need write access (temp files, logs).
  > Reference: [CIS K8s 5.2.4](https://www.cisecurity.org/benchmark/kubernetes) | [Read-only filesystem](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

- [ ] **Enforce Pod Security Standards** `Severity: High`
  Enable Pod Security Admission with `restricted` profile on production namespaces. Use `baseline` for system namespaces. `privileged` should be limited to kube-system only.
  > Reference: [CIS K8s 5.2.1](https://www.cisecurity.org/benchmark/kubernetes) | [Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)

- [ ] **Do not allow privilege escalation** `Severity: Critical`
  Set `allowPrivilegeEscalation: false` on all containers. Prevents processes from gaining more privileges than their parent process via setuid/setgid binaries.
  > Reference: [CIS K8s 5.2.5](https://www.cisecurity.org/benchmark/kubernetes) | [Privilege escalation](https://kubernetes.io/docs/concepts/security/pod-security-standards/)

- [ ] **Set resource limits on all containers** `Severity: High`
  Define CPU and memory `requests` and `limits` for every container. Prevents resource exhaustion attacks (crypto mining, DoS) and ensures fair scheduling.
  > Reference: [CIS K8s 5.4.1](https://www.cisecurity.org/benchmark/kubernetes) | [Resource limits](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)

- [ ] **Do not mount the service account token unless needed** `Severity: Medium`
  Set `automountServiceAccountToken: false` on pods that don't call the Kubernetes API. Reduces attack surface if a pod is compromised.
  > Reference: [CIS K8s 5.1.6](https://www.cisecurity.org/benchmark/kubernetes) | [Service account tokens](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#opt-out-of-api-credential-automounting)

---

## RBAC

- [ ] **Do not use cluster-admin role for workloads** `Severity: Critical`
  Never bind the `cluster-admin` ClusterRole to service accounts used by applications. Create custom roles with minimal permissions for each workload.
  > Reference: [CIS K8s 5.1.1](https://www.cisecurity.org/benchmark/kubernetes) | [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)

- [ ] **Create dedicated service accounts per workload** `Severity: High`
  Do not use the `default` service account. Create a dedicated service account for each deployment with only the permissions it needs.
  > Reference: [CIS K8s 5.1.5](https://www.cisecurity.org/benchmark/kubernetes) | [Service accounts](https://kubernetes.io/docs/concepts/security/service-accounts/)

- [ ] **Restrict use of wildcard permissions** `Severity: High`
  Audit all Roles and ClusterRoles for wildcard (`*`) verbs, resources, or API groups. Replace wildcards with explicit permission lists.
  > Reference: [CIS K8s 5.1.3](https://www.cisecurity.org/benchmark/kubernetes) | [RBAC best practices](https://kubernetes.io/docs/concepts/security/rbac-good-practices/)

- [ ] **Limit who can create or modify RBAC resources** `Severity: High`
  Restrict `create`, `update`, and `bind` permissions on Roles, ClusterRoles, RoleBindings, and ClusterRoleBindings to cluster administrators only.
  > Reference: [CIS K8s 5.1.8](https://www.cisecurity.org/benchmark/kubernetes) | [RBAC escalation prevention](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#privilege-escalation-prevention-and-bootstrapping)

- [ ] **Audit RBAC bindings regularly** `Severity: Medium`
  Review all RoleBindings and ClusterRoleBindings at least quarterly. Remove bindings for service accounts, users, or groups that no longer need access.
  > Reference: [CIS K8s 5.1.2](https://www.cisecurity.org/benchmark/kubernetes) | [Audit RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)

---

## Network Security

- [ ] **Implement Network Policies** `Severity: Critical`
  Deploy NetworkPolicies in every namespace to control pod-to-pod traffic. Start with a default deny-all policy, then explicitly allow required communication.
  > Reference: [CIS K8s 5.3.2](https://www.cisecurity.org/benchmark/kubernetes) | [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

- [ ] **Restrict egress traffic** `Severity: High`
  Define egress NetworkPolicies to limit outbound connections from pods. Pods should only reach services they need (databases, APIs, DNS). Prevents data exfiltration via compromised pods.
  > Reference: [CIS K8s 5.3.3](https://www.cisecurity.org/benchmark/kubernetes) | [Egress policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors)

- [ ] **Use a CNI plugin that supports Network Policies** `Severity: High`
  Verify your CNI plugin (Calico, Cilium, Azure CNI) enforces NetworkPolicies. Some default CNI plugins (e.g., Flannel) do not enforce them, making policies silently ineffective.
  > Reference: [CNI comparison](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

- [ ] **Enable mutual TLS (mTLS) between services** `Severity: Medium`
  Deploy a service mesh (Istio, Linkerd) or use Cilium to encrypt pod-to-pod traffic with mTLS. Protects against traffic interception within the cluster.
  > Reference: [Istio mTLS](https://istio.io/latest/docs/concepts/security/#mutual-tls-authentication) | [Linkerd mTLS](https://linkerd.io/2/features/automatic-mtls/)

- [ ] **Restrict access to the Kubernetes API server** `Severity: High`
  Limit API server access to known IP ranges using authorized networks (managed K8s) or firewall rules. Do not expose the API server to the public internet.
  > Reference: [CIS K8s 3.1.1](https://www.cisecurity.org/benchmark/kubernetes) | [API server access](https://kubernetes.io/docs/concepts/security/controlling-access/)

---

## Image Security

- [ ] **Scan container images for vulnerabilities** `Severity: Critical`
  Integrate image scanning (Trivy, Grype, Snyk) into CI/CD pipelines. Block deployments with critical or high CVEs. Rescan running images regularly.
  > Reference: [NSA K8s Guide](https://media.defense.gov/2022/Aug/29/2003066362/-1/-1/0/CTR_KUBERNETES_HARDENING_GUIDANCE_1.2_20220829.PDF) | [Trivy](https://aquasecurity.github.io/trivy/)

- [ ] **Use trusted base images only** `Severity: High`
  Pull images from verified sources (official images, your own registry). Avoid `latest` tags. Pin images to specific digests for production.
  > Reference: [CIS K8s 5.5.1](https://www.cisecurity.org/benchmark/kubernetes) | [Image security](https://kubernetes.io/docs/concepts/containers/images/)

- [ ] **Use minimal base images** `Severity: Medium`
  Build on distroless, Alpine, or scratch images. Smaller images have fewer packages, fewer CVEs, and a smaller attack surface.
  > Reference: [Distroless images](https://github.com/GoogleContainerTools/distroless)

- [ ] **Enforce image signing and verification** `Severity: Medium`
  Sign images with Cosign/Sigstore and enforce signature verification using an admission controller (Kyverno, OPA Gatekeeper, or Connaisseur).
  > Reference: [Sigstore Cosign](https://docs.sigstore.dev/cosign/overview/) | [Kyverno image verification](https://kyverno.io/docs/writing-policies/verify-images/)

- [ ] **Use a private container registry** `Severity: High`
  Host images in a private registry (ACR, ECR, GCR, Harbor) with authentication required. Configure imagePullSecrets on service accounts.
  > Reference: [CIS K8s 5.5.2](https://www.cisecurity.org/benchmark/kubernetes) | [Private registry](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)

---

## Secrets Management

- [ ] **Do not store secrets in environment variables or manifests** `Severity: Critical`
  Never hardcode secrets in Pod specs, ConfigMaps, or Dockerfiles. Use Kubernetes Secrets (minimum) or an external secrets manager.
  > Reference: [CIS K8s 5.4.1](https://www.cisecurity.org/benchmark/kubernetes) | [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)

- [ ] **Use an external secrets manager** `Severity: High`
  Integrate with Azure Key Vault, AWS Secrets Manager, HashiCorp Vault, or Google Secret Manager using CSI driver or External Secrets Operator.
  > Reference: [Secrets Store CSI](https://secrets-store-csi-driver.sigs.k8s.io/) | [External Secrets Operator](https://external-secrets.io/)

- [ ] **Enable encryption at rest for etcd** `Severity: High`
  Configure encryption at rest for Kubernetes Secrets stored in etcd. Managed services (AKS, EKS, GKE) handle this automatically; verify it's enabled on self-managed clusters.
  > Reference: [CIS K8s 1.2.29](https://www.cisecurity.org/benchmark/kubernetes) | [Encrypting secrets](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/)

- [ ] **Restrict secret access with RBAC** `Severity: High`
  Grant `get` and `list` permissions on secrets only to service accounts and users that explicitly need them. Avoid namespace-wide secret read access.
  > Reference: [CIS K8s 5.1.4](https://www.cisecurity.org/benchmark/kubernetes) | [Secret RBAC](https://kubernetes.io/docs/concepts/configuration/secret/#least-privilege-secrets)

- [ ] **Rotate secrets regularly** `Severity: Medium`
  Implement automatic secret rotation where supported (Vault, AWS Secrets Manager). For manual secrets, establish a 90-day rotation policy and track compliance.
  > Reference: [Vault rotation](https://developer.hashicorp.com/vault/docs/concepts/lease)

- [ ] **Audit secret access** `Severity: Medium`
  Enable Kubernetes audit logging and monitor access to Secret resources. Alert on unusual patterns: bulk secret reads, access from unexpected service accounts.
  > Reference: [Audit logging](https://kubernetes.io/docs/tasks/debug/debug-cluster/audit/)

---

[Back to main checklist](../README.md)
