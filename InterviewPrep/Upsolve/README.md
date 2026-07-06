# Interview Improvement System

A feedback loop, not a note pile. Interviews generate action items → action items improve **canonical** knowledge notes → better notes improve future interviews. Value compounds.

## The one rule
**Technical explanations live in canonical knowledge notes only** (top-level folders: `Linux/`, `Kubernetes/`, `Languages/`, `Networking/`, `IaC/`, `AI/`, …). This system stores **history, reflections, actions, and links** — never a second copy of an explanation. Weakness exposed → update the canonical note, then backlink from here.

## Lifecycle
```
Interview → append a Round section to the company file (Q&A + feedback + reflection + action items)
   → update canonical note → log in Knowledge Update Log
   → update Recurring Mistakes / Weakness Dashboard
   → update Playbook if needed → Weekly Review
```
Prose experience → this whole loop is automated by the `interview-log` skill.

## Map
| Area | Purpose |
|---|---|
| [[00 Dashboard/Dashboard\|Dashboard]] | Cockpit: focus, weaknesses, open actions |
| [[01 Companies/README\|Companies]] | Per-company history (no tech) |
| [[Recurring Mistakes]] · [[Weakness Dashboard]] · [[Interview Trends]] | Insights — about me |
| [[03 Playbooks/System Design\|Playbooks]] | Operational checklists |
| [[04 Question Bank/Linux\|Question Bank]] | Index of asked questions → canonical answers |
| [[05 Reviews/Weekly Review\|Reviews]] | Weekly / monthly / quarterly retros |
| `Templates/` | Reusable scaffolds |

## Canonical notes referenced
[[SysAdmin-Troubleshooting]] · [[Bash compendium]] · [[CKAD]] · [[Helm Rubric]] · [[Istio]] · [[troubleshoot-k8s-compute]] · [[Golang]] · [[Concurrent & Parallel Processing]] · [[async (Knowledge Gaps)]] · [[PythonDSA]] · [[BitManipulation]] · [[Terraform Rubric]] · [[AWS]] · [[TLS & mTLS]] · [[Networking]] · [[EKS]]

## Pre-existing prep files (folded in)
- [[preparation-resources]] — external study links
- [[questions-for-interviewer]] — questions to ask them
- [[Akamai-SRE]] — source question set, migrated into the [[04 Question Bank/Linux\|banks]]
- `Resume&CoverLetters/` — application materials
