# AWS

Canonical AWS knowledge note. Single source of truth for AWS concepts referenced across the repo (interview prep, system design, IaC). Provisioning/IaC → [[Terraform Rubric]]. Orchestration → [[CKAD]].

---

## Edge & Traffic Layer — when to use what

The recurring interview trap: "I'll put a load balancer at the front" is not a complete edge design. Each component sits at a different layer and does a different job. Chain them; don't substitute one for another.

| Component | Layer | Primary job | Does NOT do |
|---|---|---|---|
| **Route 53** | DNS | Resolve domain → IP; latency/geo/failover routing | No inspection, no auth |
| **CloudFront** | Edge CDN (L7) | Cache static/dynamic at edge POPs, TLS termination, DDoS absorption (with Shield) | Not a substitute for app auth |
| **AWS WAF** | L7 filter | Rule-based filtering (SQLi, XSS, IP/geo block, managed rules) — attaches to CloudFront, ALB, or API Gateway | Not rate limiting by identity, not authN |
| **API Gateway** | L7 API front door | **Rate limiting / throttling (usage plans + API keys)**, request validation, authN/authZ integration (Cognito authorizers, Lambda authorizers, JWT), per-route routing, usage metering | Not a general TCP/HTTP load balancer for arbitrary traffic |
| **ALB** (Application LB) | L7 | HTTP(S) routing by path/host, target groups, TLS termination, **can do OIDC authN** (`authenticate-oidc` action → Okta/Cognito/any OIDC IdP), sticky sessions | No native rate limiting; no request-quota metering |
| **NLB** (Network LB) | L4 | Ultra-low-latency TCP/UDP, static IP, millions of connections | No L7 awareness, no auth |

### The key clarifications (interview gaps)

- **Load balancers don't rate-limit out of the box.** ALB has no request-quota feature. For per-client throttling you need **API Gateway usage plans**, or **WAF rate-based rules** (coarse, IP-based, e.g. "block if >2000 req/5min from an IP"), or a service mesh / app-layer limiter. Common mistake: assuming the ALB covers it.
- **Where auth lives:** authN/authZ can be enforced at:
  - **CloudFront** → Lambda@Edge / CloudFront Functions (token check at edge).
  - **API Gateway** → Cognito authorizer or Lambda authorizer (JWT/OIDC validation) — the cleanest place for API auth.
  - **ALB** → `authenticate-oidc` action offloads login to an OIDC IdP (**Okta**, Cognito, Auth0) *before* traffic hits targets. This is how Okta integrates without app code.
- **Typical layered edge for a public API:**
  `Route 53 → CloudFront (+WAF, +Shield) → API Gateway (throttle + authZ) → ALB (path routing, +OIDC/Okta) → ECS/EKS targets`
  Not every design needs all layers — justify each. A purely internal service may skip CloudFront and API Gateway and use an internal ALB.
- **ALB vs API Gateway decision:** API Gateway when you need managed throttling, API keys, request validation, per-consumer usage plans, or serverless (Lambda) backends. ALB when you're fronting long-lived container/EC2 services with path/host routing and want lower per-request cost at high volume.

---

## Identity, Access & Secrets

- **IAM** — principals (users, **roles**), policies (identity-based, resource-based), least privilege. Prefer **roles** (assumed, temporary STS creds) over long-lived user keys. For workloads: **IAM roles for service accounts / task roles** (ECS task role, EKS IRSA) — never bake keys into images.
- **Cross-account access** — assume-role from a central account into acquired-startup accounts (the multi-account cost-dashboard scenario). Central "collector" role assumes a scoped read-only role in each target account (`sts:AssumeRole` + external ID).
- **Secrets** — **Secrets Manager** (rotation, cross-account resource policies) or **SSM Parameter Store** (cheaper, no auto-rotation). Never env-var secrets in plaintext task defs; reference the secret ARN.
- **Authentication vs Authorization** — authN = who you are (Okta/OIDC/Cognito, JWT); authZ = what you may do (IAM policies, scopes/claims, API Gateway authorizers). Interviewers probe whether you separate these.

### How you authenticate & assume roles (interview gap)

The Ericsson gap: knowing only "IAM user + password." The fuller picture — **who** can authenticate and **how you switch identity**:

- **Principals (entities):** **root** (never use for daily work), **IAM users** (long-lived credentials — console password, or access key/secret for the CLI/SDK), and **IAM roles** (no long-lived creds; assumed to get **temporary STS credentials**). Modern orgs front humans with **IAM Identity Center** (SSO) which itself hands out short-lived role sessions — which is why, if you only ever use Identity Center, you may never have touched raw users/roles.
- **Assuming a role — two ways** (both matter):
  1. **CLI/API:** `aws sts assume-role --role-arn ... --role-session-name ...` returns temp creds, **or** cleaner: a named profile in `~/.aws/config` with `role_arn` + `source_profile` (or `credential_source`), so `aws --profile foo` assumes it automatically. No manual credential exporting.
  2. **Console:** top-right account menu → **"Switch Role"** → enter account ID + role name. Same `sts:AssumeRole` under the hood, but in the UI — this is the mechanism the interviewer wanted named.
- **Same-account vs cross-account:** identical mechanism; the gate is the role's **trust policy** (who is `Principal` allowed to assume it). Cross-account adds the target account as a trusted principal (+ often an **external ID** for third parties). The caller also needs `sts:AssumeRole` permission on their side. Trust policy = *who can assume*; permission policy = *what the role can then do*.
- **Workloads assume roles too, without any keys:** EC2 **instance profile**, ECS **task role**, EKS **IRSA / Pod Identity** — the SDK transparently fetches temp creds. Never bake access keys into an instance or image.

> Hands-on to close this gap: create a user and a role, write a trust policy, and assume the role both via CLI profile and via console Switch-Role — same account and cross-account.

---

## Governance, Audit & Multi-Account

For a platform consumed by many downstream teams (the interview's "governance" gap):

- **AWS Organizations** — multi-account structure; acquired startups → separate accounts under OUs.
- **SCPs** (Service Control Policies) — org-wide guardrails (e.g. deny disabling CloudTrail, restrict regions).
- **CloudTrail** — API-call audit log (who did what, when). Org-level trail → central S3 bucket. This is the answer to "how do we audit."
- **AWS Config** — resource configuration history + compliance rules (drift, policy conformance).
- **Cost side** — **Cost Explorer / CUR (Cost & Usage Report)** to S3, **Cost Categories** + tags per product/account. Cross-account cost data is pulled via CUR export or Cost Explorer API — directly relevant to the cost-dashboard design.
- **Data governance for a shared service** — API contracts/versioning, access logging per consumer, quotas per consumer (API Gateway usage plans), schema/PII classification.

---

## Compute choice: ECS vs EC2 vs EKS vs Lambda

- **EC2** — raw VMs; you manage OS, scaling, patching. Max control, max ops burden.
- **ECS** — AWS-native container orchestration. Two launch types: **Fargate** (serverless, no node management) vs **EC2** (you manage the cluster instances). Good default when the app is **already packaged as images** and you don't need full K8s.
  - *Why ECS over EC2 (the interview point):* if the software ships as container images, ECS manages placement, health, rollouts, and ties directly into the image/deployment pipeline — you don't hand-roll it on bare EC2.
- **EKS** — managed Kubernetes; choose when you need the K8s ecosystem/portability. See [[CKAD]] · [[troubleshoot-k8s-compute]].
- **Lambda** — event-driven, no servers; good for the *ingestion pull jobs* (scheduled cost pulls per account) if runtime < 15 min.

---

## VPC egress — IGW vs NAT gateway, what attaches where (interview gap)

The Ericsson gap: saying "the **IGW** attaches to the public subnet." It doesn't. Getting the *placement* of each construct right is the whole point:

- **Internet Gateway (IGW)** — attaches at the **VPC level** (one per VPC), horizontally-scaled and HA. It is **not** a subnet resource. A subnet is "**public**" purely because its **route table** has `0.0.0.0/0 → igw-xxxx` **and** the instance has a public/Elastic IP. Nothing is "put in" the subnet.
- **NAT gateway** — is **provisioned into a specific subnet** (a *public* one), where it gets an **ENI + Elastic IP**. This is the construct that genuinely "lives in a subnet." It does SNAT so private hosts share its EIP (mechanism → [[Networking#NAT gateway — how it works under the hood (Linux/netfilter)|Networking]]).

**Private-subnet EC2 outbound (download packages):**
1. NAT gateway sits in a **public** subnet (which routes to the IGW).
2. The **private** subnet's route table sends `0.0.0.0/0 → nat-xxxx`.
3. Private instance → NAT GW (SNAT to its EIP) → IGW → internet; replies return via conntrack. The instance stays **unreachable from the internet** (egress-only) — that's the point.
   - Cheaper/native alternative for AWS-service access (no NAT): **VPC gateway/interface endpoints** (S3/ECR/etc. over PrivateLink), so package/image pulls never leave the AWS network.

> One-liner: **IGW = VPC-level (route table makes a subnet public); NAT gateway = lives in a public subnet, gives private subnets egress-only internet.**

---

## Remote access to EC2 with no inbound ports — SSM Session Manager (interview gap)

The question (Lucidity HM): *"connect to EC2 instances but open no inbound ports — no SSH, no VNC."* The answer is **AWS Systems Manager (SSM) Session Manager**. What matters is **why it needs zero inbound**:

- **It's agent-initiated and outbound-only.** The **SSM Agent** (preinstalled on Amazon Linux, Ubuntu, Windows AMIs) runs on the instance and opens an **outbound HTTPS (port 443)** connection *to* the SSM service. When you start a session, traffic is brokered back over that already-open outbound channel (a WebSocket). Nothing ever listens for an inbound connection, so the **security group needs no inbound rule at all** — only egress 443.
- **No bastion, no SSH keys, no public IP** — access is entirely IAM-controlled. This is the modern replacement for bastion hosts.

**What it needs:**
- SSM Agent installed & running on the instance.
- An **IAM instance profile** with `AmazonSSMManagedInstanceCore` (grants `ssmmessages`, `ssm:UpdateInstanceInformation`, `ec2messages`).
- A network path to three regional endpoints — reachable via NAT/IGW, **or**, for a fully private instance with no internet, via **VPC interface endpoints (PrivateLink)** for `ssm`, `ssmmessages`, `ec2messages`.
- The operator holds `ssm:StartSession`.

**The three endpoints:** `ssm` (service API/inventory), `ssmmessages` (the Session Manager data channel — the interactive session), `ec2messages` (control channel). All reached over **outbound 443**.

**Why interviewers like it:** full audit via **CloudTrail** + optional session logging to **S3/CloudWatch Logs**, IAM-scoped per-user access, no key sprawl, and supports **port forwarding** (tunnel RDP/DB ports over the same no-inbound channel).

Reading: [Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) · [How it works](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started.html) · [VPC endpoints for SSM](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-create-vpc.html).

---

## Ingestion patterns (push vs pull)

For "gather cost/metrics from N accounts across AWS + Azure":

- **Pull** — scheduled workers (EventBridge Scheduler → Lambda/ECS task) call each provider's cost API (AWS Cost Explorer / CUR, Azure Cost Management API), assuming a cross-account role per target. Simple, you control cadence; risk = API rate limits & large accounts.
- **Push** — target accounts export CUR to a central S3 bucket / send to a queue. Scales better, less central credential sprawl; risk = you depend on each account configuring the export.
- **Decouple with a queue/stream** — land raw pulls in **SQS/Kinesis → S3 (raw) → transform → warehouse** (Athena/Redshift) so ingestion and processing scale independently and you get replay/durability. This is the "buffer before store" tradeoff to name in design rounds.

### Durable push ingestion — never lose an authenticated request (interview gap)

Scenario: external accounts **push** data to us. Once authenticated we must accept **every** request and never drop it, even under a huge burst or while the ingestion compute is cold/scaling/failing. The instinct "just put SQS after the load balancer" is wrong — **an ALB/NLB cannot write to SQS**; only compute or a service integration can.

The fix is to make the **accept tier a managed, always-on service that writes straight to a durable buffer** — so no cold start or fault sits on the ingest path:

- **API Gateway → SQS/Kinesis/Firehose direct service integration** (AWS integration, no Lambda in the middle). API Gateway authenticates (authorizer), then calls `SendMessage`/`PutRecord`/`PutRecordBatch` directly. It's HA and auto-scaling, so the burst is absorbed and persisted **before** any of your compute runs. This is the clean answer to "LB can't reach SQS" — drop the LB from the accept path and let API Gateway be the front door.
- **Direct-to-S3** (large payloads) — hand the pusher a **pre-signed S3 URL**; they `PUT` straight to S3 (11 9s durable), and an S3 event fans out to SQS/Lambda. Zero custom accept compute.
- **Firehose** — if you only need raw landing in S3/Redshift, Firehose buffers (size/time) and writes with no code.

Then consumers drain at their own pace: **SQS → ECS/Lambda consumers**, scaled independently. Cold starts and faults are harmless — the message stays in the queue (visibility timeout + retries), and poison messages go to a **DLQ**. Use **Kinesis** instead of SQS when you need ordering, replay, or high fan-out to multiple consumers.

- **If you must keep an LB** (e.g. non-HTTP protocol, existing ALB): the accept service behind it has to be **always-warm** (ECS service with `minCount ≥ 1`, or provisioned-concurrency Lambda) and do nothing but validate + enqueue. Still less robust than the managed accept tier above — name it as the fallback, not the default.

Rule of thumb: **accept-and-persist must be a managed durable service; processing is what scales behind the buffer.** Never let your own compute be the single thing standing between an accepted request and durable storage.

---

## Fan-out / pub-sub — one event to many consumers (interview gap)

Scenario: an account crosses a budget (e.g. $500K) → we stop ingesting and must **notify every subscribed consumer** of that event. This is **pub-sub fan-out**, not a work queue.

**The trap: "use SQS with partitions."** SQS has **no partitions** (that's Kinesis *shards* / Kafka *partitions*), and SQS is **point-to-point** — a message is delivered to **one** consumer, then hidden by the visibility timeout and deleted. It cannot show the *same* message to every consumer. Wrong tool for fan-out.

| Service | Model | Use when | Delivery / tracking |
|---|---|---|---|
| **SQS** | Point-to-point work queue | One logical worker pool drains a backlog | One consumer per message; at-least-once; DLQ |
| **SNS** | Pub-sub topic, **push** fan-out | Broadcast one event to many subscribers (Lambda, SQS, HTTP, email/SMS) | Fire-and-forget; retries + delivery-status logging + DLQ, but no per-subscriber "did they read it" |
| **SNS → SQS fan-out** | Topic → **one SQS queue per consumer** | The canonical AWS answer: every consumer gets its **own durable copy**, drains at its own pace | Per-consumer durability, retries, DLQ; you know it reached each queue, and the consumer's delete = consumed |
| **EventBridge** | Event bus with **content filtering** + routing | Different consumers want different subsets; SaaS/AWS-service targets; schema registry | Rules per target, retries, archive + **replay** |
| **Kinesis Data Streams** | Ordered, replayable log; **many independent readers** | High-throughput streaming; each consumer group keeps its own checkpoint and re-reads the same records | Ordered per shard, retention window, replay by iterator |

### The two design axes to name out loud

1. **Do we care whether the consumer received it?**
   - **Fire-and-forget** → **SNS** straight to subscribers (or push to end-user channels). Cheapest, but no durability if a consumer is down.
   - **Guaranteed / at-your-own-pace** → **SNS → SQS fan-out** (or EventBridge → SQS). Each consumer has a durable queue; a down consumer catches up later; failures land in a per-consumer DLQ. This is the answer to "ensure the consumer consumed it."
2. **Same copy to everyone, or a shared log?**
   - Discrete events, per-consumer isolation → **SNS→SQS** or **EventBridge**.
   - Ordered, replayable stream multiple teams re-read independently → **Kinesis**.

### Pushing to *end users* (browser/mobile), not backend services

There was no consumer notification system yet — if consumers are humans/clients, the fan-out sink is a real-time channel: **API Gateway WebSocket API**, **AppSync GraphQL subscriptions**, or **SNS mobile push / email / SMS**. Backend event bus (SNS/EventBridge) still sits behind it; the WebSocket/AppSync layer is just the last hop to the client.

Rule of thumb: **SQS = one worker pool draining work; SNS/EventBridge = broadcast an event to many; SNS→SQS = broadcast *and* give each consumer a durable, independently-paced copy.**

---

Related: [[Terraform Rubric]] (provisioning) · [[CKAD]] (K8s) · [[Istio]] (mesh-level LB/mTLS/rate-limiting analogue) · [[System Design]] playbook
