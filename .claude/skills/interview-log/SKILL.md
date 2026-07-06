---
name: interview-log
description: Process a described interview experience into the InterviewPrep/Upsolve/ system in this repo. Use when the user shares what happened in an interview, a recruiter call, a rejection/offer, or any interview reflection, and wants it logged, routed, and turned into action items. Routes technical gaps to canonical knowledge notes (never duplicates explanations), updates insights/dashboards, and files action items.
---

# interview-log

Turn a raw interview experience into structured entries across `InterviewPrep/Upsolve/`, following the system's one rule: **explanations live only in canonical knowledge notes; this system stores history, reflections, actions, and links.**

Read `InterviewPrep/Upsolve/README.md` first — it defines the lifecycle and folder map. This skill automates that lifecycle.

## When invoked

The user will describe an experience in prose ("Had my Cloudflare round 2, bombed the TCP question, recruiter was pushy about comp…"). Extract what's present; don't demand a form. Ask **at most one** clarifying batch only if company or round is genuinely ambiguous — otherwise proceed and note assumptions.

## Routing workflow

Do these in order. Skip any step with no signal in the input.

1. **Company file — ONE file per company.** `InterviewPrep/Upsolve/01 Companies/<Company>/<Company>.md` holds everything: header (role/status/comp), a small timeline table, and one `## Round N` section per round appended over time. **Do not** split into Timeline/Recruiter/Round N/Feedback/Postmortem/Offer files — that structure is unmaintainable by hand. Each round section is compact:
   - one line of meta (interviewer · duration · felt),
   - questions as short bullets with confidence + `[[canonical note]]` link (no answers),
   - `Feedback` (keep / improve), `Root cause`, `Action items` checkboxes.
   - Fold recruiter notes / offer details into the same file as short lines or timeline rows.
   - Templates are optional scaffolding — inline the content, don't proliferate files.

2. **Log questions** into `04 Question Bank/<Topic>.md` as index rows (Question · Companies · Diff · Conf · Canonical · Round). **Never write the answer here** — only link the canonical note.

3. **Route each technical gap to a canonical note** (top-level `Linux/`, `Kubernetes/`, `Languages/`, `Networking/`, `IaC/`, `AI/`, `AWS/`, …):
   - Note exists and is thin → **update it** with the missing explanation.
   - Note missing → **create it in the correct top-level knowledge folder** (NOT inside InterviewPrep), then link.
   - Known canonical notes: `[[SysAdmin-Troubleshooting]]` `[[Bash compendium]]` `[[CKAD]]` `[[Helm Rubric]]` `[[Istio]]` `[[troubleshoot-k8s-compute]]` `[[Golang]]` `[[Concurrent & Parallel Processing]]` `[[async (Knowledge Gaps)]]` `[[PythonDSA]]` `[[BitManipulation]]` `[[Terraform Rubric]]` `[[AWS]]`. Stub to create on first gap: `[[Networking]]`.
   - Record every canonical edit as one row in `Templates/Knowledge Update Log.md`.

4. **Reflection lives in the round section** (step 1) — summary, what went well/poorly, root cause, action-item checkboxes. No separate postmortem file.

5. **Action items** are checkboxes in the round section with a due date + observable done-criteria; surface the open ones on the Dashboard. No separate per-item files.

6. **Update insights (about me):**
   - Communication/behavioral slip → `02 Insights/Communication Patterns.md` or `Behavioral Insights.md`.
   - Repeated mistake → `02 Insights/Recurring Mistakes.md` (bump count; 3+ → add to relevant playbook).
   - Recruiter signal → `02 Insights/Recruiter Advice.md`.
   - Cross-company pattern → `02 Insights/Interview Trends.md`.
   - Every bombed/shaky topic → adjust confidence + count in `02 Insights/Weakness Dashboard.md`.

7. **Update playbooks** (`03 Playbooks/`) only if a repeated failure mode warrants a new checklist line.

8. **Refresh the Dashboard** (`00 Dashboard/Dashboard.md`): recent-interviews row, open action items, recently-updated notes, top recurring mistakes, current focus = weakest 3.

## Hard rules

- **No technical explanation ever lands inside `InterviewPrep/Upsolve/`.** If you're tempted to explain *how* something works there, stop — put it in the canonical note and link it.
- **Stay lean / human-maintainable.** One file per company; prefer appending a section or a table row over creating a new file. If a human couldn't keep this up without an AI, it's over-built — cut it back. Depth goes in the canonical note, not in more interview files.
- One canonical explanation per topic. Update, don't fork.
- Use `[[wikilinks]]` (Obsidian vault). Forward-references to not-yet-created notes are fine.
- Dates absolute (`YYYY-MM-DD`). Confidence: 🟢 solid / 🟡 shaky / 🔴 bombed.
- Match the terse, table-driven style already in the files. Don't rewrite what you don't need to touch.

## Output

End with a short summary: files created/updated, canonical notes touched, and open action items — so the user can eyeball the routing.
