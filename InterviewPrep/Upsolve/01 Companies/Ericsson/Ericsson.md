# Ericsson

- **Role:** _tbd_
- **Status:** `Interviewing` — Round 1 done
- **Comp target:** _tbd_

History and reflections only. Technical answers live in [[AWS]] and [[EKS]].

## Timeline
| Date | Event | Result |
|---|---|---|
| _recent_ | Round 1 | ⚠️ humbling — exposed IAM / VPC / EKS day-2 gaps |

---

## Round 1 — 2026-07 (a few days ago)
- **Felt:** 😬 humbled — three questions, all real knowledge gaps

### Q1 — how do you authenticate to AWS (user or entity)? 🔴 → [[AWS]]
Only knew IAM user + username/password (my company uses Identity Center, so I couldn't describe the base mechanism). Missed **role assumption** — both the CLI path (`sts:AssumeRole` / named profiles) **and the console "switch role" (top-right)** the interviewer wanted. Flagged as a **high-priority hands-on gap**: create users/roles, trust policies, same-account + cross-account assume. → [[AWS#How you authenticate & assume roles (interview gap)|AWS]]

### Q2 — private-subnet EC2 outbound (download packages)? 🟡 → [[AWS]]
Described NAT gateway + `0.0.0.0/0` route → NAT + IGW correctly, **but wrongly said the IGW attaches to the public subnet.** Correction (fact-checked, true): **IGW attaches at the VPC level**; a subnet is "public" only because its **route table** points at the IGW. A **NAT gateway** *does* live in a (public) subnet. → [[AWS#VPC egress — IGW vs NAT gateway, what attaches where (interview gap)|AWS]]

### Q3 — have you upgraded a cluster? 🔴 → [[EKS]]
Gave the **wrong order** (add-ons → control plane → nodes). Interviewer: add-ons-first breaks connectivity — **control plane must go first** (version-skew: API server is always highest). Exposed that I don't know **EKS day-2 operations**. → [[EKS#Cluster upgrades — order matters (interview gap)|EKS]] · security → [[EKS#Securing an EKS cluster (day-2 security checklist)|EKS]]

**Root cause:** knowledge + hands-on gaps (IAM, VPC constructs, EKS day-2), not communication.

**Action items:**
- [ ] **Hands-on IAM lab:** create users & roles, trust policies, assume same-account + cross-account via CLI *and* console switch-role — due 2026-07-13 → [[AWS]]
- [ ] State cold: IGW = VPC-level, subnet is public via route table; NAT GW lives in a subnet — due 2026-07-13 → [[AWS]]
- [ ] Memorize EKS upgrade order (control plane → nodes → add-ons, one minor at a time) + why — due 2026-07-13 → [[EKS]]
- [ ] Learn the EKS day-2 catalog + security checklist end-to-end — due 2026-07-20 → [[EKS]]

Knowledge edits logged in [[Knowledge Update Log]].
