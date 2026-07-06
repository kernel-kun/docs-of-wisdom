# Q2 Software

- **Role:** DevOps Engineer
- **Status:** `Onsite` — passed Round 1 (system design)
- **Comp target:** _tbd_

History and reflections only. Technical answers live in [[AWS]] (created from this round).

## Timeline
| Date | Event | Result |
|---|---|---|
| _prior_ | Online Assessment (OA) | passed |
| 2026-07-03 | Round 1 — System Design | ✅ positive |
| 2026-07-06 | Recruiter relayed official feedback | — |

---

## Round 1 — System Design — 2026-07-03
- **Interviewer:** Rajdeepak Sharma · **Duration:** 1h 15m · **Felt:** 👍 (confirmed by recruiter)

**Q1 — proudest/hardest project?** 🟢 GitHub-issue project: fixed latency + image-pull issues, sped up the pipeline, cut costs (Spark). Smooth.

**Q2 — design a multi-cloud cost platform.** 🟡 → [[AWS]] · [[03 Playbooks/System Design|playbook]] · ![[q2-software-system-design.png]]
Scenario: acquired startups, ~30 products in AWS + 20 in Azure, each own account. Ingest **cost** now (later latency/CPU/mem), push- or pull-based, microservices, clarify volume first. My approach: clarified scope → compute/storage/networking → LB at front → **ECS over EC2** (image-based packaging). → banked in [[04 Question Bank/AWS]] & [[04 Question Bank/System Design]].

**Feedback (via recruiter, official):**
- ✅ Keep: design **format**, clarifying questions, end-to-end deployment + architecture knowledge, ECS-vs-EC2 reasoning.
- ⚠️ Improve — all one theme, the AWS edge/security/governance layer → [[AWS]]:
  1. Security too abstract — IAM, secrets, authN/authZ.
  2. Rate limiting / edge — ALB ≠ rate limiting; needed API Gateway. Unsure how API GW / WAF / CloudFront / ALB layer, where authN/authZ sits, how Okta/OIDC integrates.
  3. Audit & governance thin — for a widely-consumed downstream platform.
  4. Durable push ingestion — couldn't cleanly answer "accept every authenticated request, never drop under burst/cold-start." Proposed SQS-after-LB (LB can't write to SQS). Better: API Gateway → SQS/Kinesis direct integration; consumers drain behind the buffer. → [[AWS#Durable push ingestion — never lose an authenticated request (interview gap)|AWS]]

**Root cause:** knowledge gap (canonical [[AWS]] didn't exist), not communication — format was a strength.

**Action items:**
- [ ] Whiteboard AWS edge chain (Route53→CloudFront/WAF→API GW→ALB→ECS) with rate limiting + authN/authZ — due 2026-07-13 → [[AWS]]
- [ ] Explain Okta/OIDC integration points (ALB `authenticate-oidc`, API GW authorizers) — due 2026-07-13
- [x] Add security+governance pass to [[03 Playbooks/System Design|System Design playbook]]

Recruiter signal: relays detailed, actionable feedback — mine it. → [[Recruiter Advice]]. Knowledge edit logged in [[Knowledge Update Log]].
