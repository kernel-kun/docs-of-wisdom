# Interview Dashboard

The cockpit. Everything else feeds this. Update after each interview + weekly.

## 🎯 Current focus
Top 3 from [[Weakness Dashboard]]:
1. 🔴 AWS edge/security — API GW vs ALB, WAF, IAM, authN/authZ, Okta → [[AWS]]
2. 🔴 AWS governance/audit — CloudTrail, Config, Organizations → [[AWS]]
3. 🟡 System design security pass (do it unprompted) → [[03 Playbooks/System Design|playbook]]

## 🔴 Weakest topics
See [[Weakness Dashboard]].

## 🗓️ Recent interviews
| Date | Company | Round | Result |
|---|---|---|---|
| 2026-07-03 | [[Q2 Software]] | R1 System Design | ✅ passed, positive feedback |

## ✅ Open action items
_Aggregated from postmortems. Triaged in [[05 Reviews/Weekly Review|Weekly Review]]._
- [ ] Whiteboard AWS edge chain (Route53→CloudFront/WAF→API GW→ALB→ECS) w/ rate limiting + authN/authZ — due 2026-07-13 → [[AWS]]
- [ ] Explain Okta/OIDC integration points (ALB `authenticate-oidc`, API GW authorizers) — due 2026-07-13

## 📚 Recently updated knowledge notes
From [[Knowledge Update Log]]:
- 2026-07-06 — [[AWS]] created (edge/security/governance/ECS/ingestion)

## 🔁 Most frequent recurring mistakes
Top 3 from [[Recurring Mistakes]]:
1. Security stays abstract when probed (IAM, secrets, authN/authZ)
2. Skip edge layer / assume LB does rate limiting
3. Don't cover audit/governance for multi-consumer platforms

## ⏰ Upcoming reviews
- Weekly: [[05 Reviews/Weekly Review]]
- Monthly: [[05 Reviews/Monthly Review]]
- Quarterly: [[05 Reviews/Quarterly Review]]

## ⭐ High-priority concepts
- 

---
### Navigation
[[README]] · [[01 Companies/README|Companies]] · [[Weakness Dashboard]] · [[Interview Trends]] · [[04 Question Bank/Linux|Question Bank]] · [[03 Playbooks/System Design|Playbooks]]
