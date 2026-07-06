# Playbook — System Design

## Flow
1. **Clarify** — functional + non-functional reqs, scope, out-of-scope
2. **Estimate** — QPS, storage, bandwidth (back-of-envelope)
3. **API + data model**
4. **High-level diagram** — happy path
5. **Deep-dive** — the 1–2 hard parts they care about
6. **Bottlenecks & tradeoffs** — cache, shard, queue, replicate
7. **Security & governance pass** — don't wait to be prompted: authN/authZ placement, IAM/secrets, rate limiting, edge layer, audit/governance. See [[AWS]] checklist below. _(added after [[Q2 Software]] R1)_
8. **Wrap** — recap, what I'd revisit

## Security & governance checklist (say these unprompted)
- **Edge order:** Route53 → CloudFront (+WAF) → API Gateway (rate limit/throttle) → ALB (path routing, OIDC) → compute. LB alone ≠ rate limiting.
- **authN/authZ:** where it sits (API GW authorizer / ALB `authenticate-oidc` / Okta-OIDC). IAM roles > keys; Secrets Manager/SSM.
- **Governance:** CloudTrail (audit), Config (compliance), Organizations/SCP, per-consumer quotas.
- Details → [[AWS]].

## Reusable building blocks (link canonical)
- Load balancing / proxy / edge → [[Istio]] · **AWS edge layer → [[AWS]]**
- Container orchestration → [[CKAD]] · [[troubleshoot-k8s-compute]] · **ECS vs EC2/EKS → [[AWS]]**
- OS/perf limits → [[SysAdmin-Troubleshooting]]

## My failure modes
- (from [[Recurring Mistakes]])

Related: [[System Design]] question bank
