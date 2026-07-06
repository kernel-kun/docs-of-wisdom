# Knowledge Update Log

The audit trail proving interviews → better canonical notes. One row per edit.

| Date | Trigger (interview/round) | Canonical note | What changed |
|---|---|---|---|
| 2026-07-06 | [[Q2 Software]] R1 | [[AWS]] | **Created note.** Edge layer (API GW/ALB/WAF/CloudFront), rate limiting, IAM/secrets, authN/authZ + Okta/OIDC, governance (CloudTrail/Config/Orgs/SCP), ECS vs EC2, push/pull ingestion |
| 2026-07-06 | [[Q2 Software]] R1 | [[AWS]] | Added **durable push ingestion** section — API GW→SQS/Kinesis direct integration, pre-signed S3, DLQ, always-warm-consumer fallback; the "LB can't write to SQS / accept without dropping" gap |
| 2026-07-06 | [[Q2 Software]] R1 | [[AWS]] | Added **fan-out / pub-sub** section — SQS vs SNS vs EventBridge vs Kinesis, SNS→SQS fan-out, fire-and-forget vs guaranteed, end-user WebSocket/AppSync push; the "SQS partitions" trap |
| 2026-07-06 | [[Lucidity]] HM | [[TLS & mTLS]] | **Created note.** Keys vs certificates, the CA & chain of trust, TLS (one-way) vs mTLS, the handshake split (CA-signature = identity, `CertificateVerify` = ownership); the "certs = GPG keypair" / missing-CA gap |
| 2026-07-06 | [[Lucidity]] HM | [[Istio]] | Added **mTLS design section** — istiod as CA, SPIFFE identity, PeerAuthentication STRICT, shared root across clusters, on-prem/non-mesh edge, SPIFFE/SPIRE & cert-manager alternatives |
| 2026-07-06 | [[Lucidity]] HM | [[AWS]] | Added **SSM Session Manager** section — agent-initiated outbound-443, zero inbound, IAM instance profile, ssm/ssmmessages/ec2messages endpoints + PrivateLink; the "how does SSM work under the hood" gap |
| 2026-07-06 | [[Lucidity]] HM | [[Networking]] | **Created note.** RFC 1918 blocks & sizes, switch vs router (L2/L3), overlapping-CIDR→NAT, Linux/hypervisor virtual networking (namespace/bridge/veth/tap) |
| 2026-07-06 | [[Lucidity]] HM | [[Networking]] | Added **NAT gateway under the hood** section — stateful SNAT + conntrack, POSTROUTING MASQUERADE/SNAT, why egress-only, AWS IGW/NAT-GW mapping |
| 2026-07-06 | [[Ericsson]] R1 | [[AWS]] | Added **authenticate & assume roles** section — users vs roles vs Identity Center, CLI `sts:AssumeRole`/profiles + console switch-role, trust vs permission policy, same/cross-account |
| 2026-07-06 | [[Ericsson]] R1 | [[AWS]] | Added **VPC egress** section — IGW is VPC-level (route table makes a subnet public), NAT gateway lives in a public subnet; private-subnet egress path + VPC endpoints alternative |
| 2026-07-06 | [[Ericsson]] R1 | [[EKS]] | **Created note.** Upgrade order (control plane→nodes→add-ons, version skew), day-2 ops catalog, security checklist (IRSA/Pod Identity, KMS, NetworkPolicy, private endpoint, IMDSv2, GuardDuty) |
| 2026-07-06 | [[Ericsson]] R1 (follow-up) | [[EKS]] | Added **multi-tenancy guide** (beginner/intermediate/expert isolation ladder, soft vs hard, vcluster/Capsule/HNC) + curated **day-2 resources** (EKS Best Practices Guide, Workshop, Karpenter, kubent/Pluto, Velero, Kyverno, blueprints) |

> Rule: the *explanation* goes in the canonical note. This log only records **that** it happened.
