# EKS

Canonical note for **Amazon EKS day-2 operations** — running a managed Kubernetes cluster after creation. Generic K8s app concepts → [[CKAD]]. Compute/scheduling troubleshooting → [[troubleshoot-k8s-compute]]. Mesh mTLS → [[Istio]]. IAM/VPC basics → [[AWS]].

---

## Cluster upgrades — order matters (interview gap)

The Ericsson gap: proposing **add-ons → control plane → nodes**. That's backwards and breaks the cluster. The rule comes from the Kubernetes **version-skew policy**: the **API server (control plane) must always be the highest version**; kubelet (nodes) and components (kube-proxy, CoreDNS, CNI) may lag but must **never run ahead** of it. So upgrade top-down:

| Step | What | Why this order |
|---|---|---|
| 0 | **Pre-flight** — one minor version at a time (1.28→1.29, no skips); check deprecated APIs (`kubectl` / `kube-no-trouble`), free subnet IPs (control-plane ENIs need them), PodDisruptionBudgets, add-on compatibility matrix | Skipping a minor or exhausting IPs fails the upgrade |
| 1 | **Control plane** first (AWS-managed; `eks update-cluster-version`) | API server must lead; everything else is verified against it |
| 2 | **Nodes / data plane** — managed node groups (rolling, respects PDBs), self-managed (new AMI + drain/cordon), or Fargate (recycle pods) | kubelet catches up to the new API server; nodes ≤ control plane |
| 3 | **Add-ons last** — VPC CNI, kube-proxy, CoreDNS, EBS/EFS CSI → bump to the versions AWS recommends for the new K8s version | These talk to the API server; upgrading them ahead of it (the mistake) can break pod networking / DNS and pods never come up |

> One-liner: **control plane → nodes → add-ons, one minor at a time.** Never lead with add-ons.

## Day-2 operations catalog

| Area | What you own after creation |
|---|---|
| **Upgrades** | Control-plane + node + add-on version lifecycle (above); stay within EKS support window (extended support = $$) |
| **Node lifecycle** | Managed node groups vs self-managed; AMI patching (EKS-optimized AL2023 / **Bottlerocket**); cordon/drain; Spot interruption handling |
| **Autoscaling** | Pods: **HPA/VPA**. Nodes: **Karpenter** (preferred — just-in-time, consolidation) or Cluster Autoscaler |
| **Add-on config** | VPC CNI (**prefix delegation** to avoid IP exhaustion), CoreDNS, kube-proxy, CSI drivers |
| **Networking** | Subnet/IP capacity, security groups for pods, NetworkPolicy (Calico/Cilium), private vs public API endpoint |
| **Observability** | Control-plane logs → CloudWatch, **Container Insights**, Prometheus/Grafana, metrics-server |
| **Security** | See checklist below |
| **Backup / DR** | **Velero** for cluster objects + PV snapshots (etcd itself is AWS-managed); multi-AZ; stateful snapshots |
| **Cost** | Right-sizing, Spot, Karpenter consolidation, idle-node reclaim |

## Securing an EKS cluster (day-2 security checklist)

Beyond "RBAC + private control plane" — the dimensions interviewers expect:

- **AuthN → K8s mapping:** IAM principals map to K8s identities via **EKS access entries / access policies** (the modern API) or the legacy `aws-auth` ConfigMap. Don't hand out `system:masters`.
- **Pod → AWS permissions:** **IRSA** (IAM Roles for Service Accounts, via the cluster OIDC provider) or the newer **EKS Pod Identity** — per-workload least-privilege, never share the node instance role.
- **RBAC:** least-privilege Roles/ClusterRoles; no blanket `cluster-admin`.
- **Control-plane endpoint:** **private** (or public+private with a CIDR allowlist); reach it via bastion / VPN / SSM port-forward.
- **Secrets:** **envelope-encrypt etcd secrets with KMS** (EKS secrets encryption); pull app secrets from Secrets Manager (External Secrets / CSI driver), not plain K8s Secrets.
- **Node hardening:** Bottlerocket/EKS-optimized AMIs, regular patching, **IMDSv2 required + hop limit 1** (blocks pods from stealing node creds), no SSH (use SSM), tight node IAM role.
- **Network:** default-deny **NetworkPolicy** (Calico/Cilium), **security groups for pods** for AWS-resource-level isolation.
- **Supply chain / admission:** **ECR image scanning**, policy admission control (**Kyverno / OPA Gatekeeper**), signed images.
- **Audit / runtime:** enable all **control-plane log types** (api, audit, authenticator, controllerManager, scheduler) → CloudWatch; **GuardDuty EKS Protection**; runtime detection (**Falco**).

---

## Multi-tenancy — beginner → intermediate → expert

Multi-tenancy = many teams/customers/apps sharing cluster infrastructure. The core question is always **how much isolation do the tenants need?** — which maps to *how much you trust them*. **Soft multi-tenancy** = trusted internal tenants (accidents, not attacks, are the threat). **Hard multi-tenancy** = untrusted/adversarial tenants (must assume a tenant tries to break out). You climb the ladder only as far as the trust model demands — stronger isolation costs more money and ops.

| Level | Isolation boundary | Mechanisms | Use when |
|---|---|---|---|
| **Beginner** (soft) | **Namespace per tenant** | RBAC (tenant-scoped Roles), **ResourceQuota** + **LimitRange** (stop noisy neighbors), default-deny **NetworkPolicy**, **Pod Security Standards** (`restricted`) | Trusted internal teams on one shared cluster |
| **Intermediate** (soft, hardened) | Namespace **+ dedicated compute & identity** | Per-tenant **node groups** via taints/tolerations + `nodeSelector`/affinity (or Karpenter provisioners per tenant), **IRSA/Pod Identity per tenant** (own IAM scope), policy enforcement (**Kyverno / OPA Gatekeeper**), namespace automation (**Capsule**, **Hierarchical Namespaces/HNC**), per-tenant cost/observability (cost-allocation tags, **Kubecost**, separate Grafana orgs) | Many internal teams needing compute isolation, blast-radius control, and chargeback — still one control plane |
| **Expert** (hard) | **Own API server or own cluster** | **Virtual clusters (vcluster)** — each tenant gets a syncer-backed API server on shared nodes; **sandboxed runtimes** (gVisor, Kata, Firecracker, Bottlerocket) for kernel-level isolation; **cluster-per-tenant** or **account-per-tenant** (separate VPC/KMS); strict network segmentation | Untrusted / external / regulated tenants where a breakout is unacceptable |

**The isolation spectrum (cheap→expensive, weak→strong):** shared namespace → namespace + quotas/RBAC/NetworkPolicy → dedicated node pools → **virtual clusters** → dedicated clusters → dedicated AWS accounts. Move right only when the threat model forces it; each step multiplies cost and management overhead.

Recurring gotchas: the **control plane is always shared below the cluster-per-tenant line** (a tenant can still exhaust API/etcd) → per-namespace API priority & fairness; **CRDs and webhooks are cluster-scoped** (one tenant's CRD/webhook affects all) → a top reason to jump to vcluster/dedicated clusters; **node kernel is shared** unless you use sandboxed runtimes.

## Resources — day-2 operations & best practices

**Start here (the canonical references):**
- **EKS Best Practices Guide** — https://aws.github.io/aws-eks-best-practices/ — the single most important reference: security, reliability, scalability, cost, networking, **and a full multi-tenancy chapter** (https://aws.github.io/aws-eks-best-practices/security/docs/multitenancy/).
- **EKS User Guide** — https://docs.aws.amazon.com/eks/latest/userguide/ — authoritative; see *Updating a cluster* and *Cluster management*.
- **EKS Workshop** — https://www.eksworkshop.com/ — hands-on labs (autoscaling, security, observability, fundamentals).

**Upgrades:**
- **kube-no-trouble (kubent)** — https://github.com/doitintl/kube-no-trouble — scan for deprecated/removed APIs before a version bump.
- **Pluto** (Fairwinds) — https://github.com/FairwindsOps/pluto — same purpose, CI-friendly.
- EKS **cluster insights** (built-in upgrade readiness checks) — in the EKS console/API.

**Autoscaling & nodes:**
- **Karpenter** — https://karpenter.sh/ — just-in-time node provisioning + consolidation (AWS-preferred over Cluster Autoscaler).
- **Bottlerocket** — https://bottlerocket.dev/ — minimal, secure container OS for nodes.

**Security & policy:**
- **CIS Amazon EKS Benchmark** (via kube-bench) — https://github.com/aquasecurity/kube-bench.
- **Kyverno** — https://kyverno.io/ · **OPA Gatekeeper** — https://open-policy-agent.github.io/gatekeeper/ — admission policy.
- **Falco** — https://falco.org/ — runtime threat detection.

**Backup / DR:**
- **Velero** — https://velero.io/ — cluster-object backup + PV snapshots (etcd is AWS-managed, so you back up *state + volumes*).

**Multi-tenancy tooling:**
- **vcluster** (Loft) — https://www.vcluster.com/ — virtual clusters for hard multi-tenancy.
- **Capsule** — https://capsule.clastix.io/ — namespace-as-a-service tenancy operator.
- **Hierarchical Namespace Controller (HNC)** — https://github.com/kubernetes-sigs/hierarchical-namespaces.
- **Kubernetes multi-tenancy concepts** — https://kubernetes.io/docs/concepts/security/multi-tenancy/.

**IaC / blueprints:**
- **terraform-aws-eks** (community module) — https://github.com/terraform-aws-modules/terraform-aws-eks · **EKS Blueprints** — https://github.com/aws-ia/terraform-aws-eks-blueprints · **eksctl** — https://eksctl.io/. IaC provisioning conventions → [[Terraform Rubric]].

---

Related: [[CKAD]] · [[troubleshoot-k8s-compute]] · [[Helm Rubric]] · [[Istio]] · [[AWS]] · [[TLS & mTLS]] · [[Terraform Rubric]]
