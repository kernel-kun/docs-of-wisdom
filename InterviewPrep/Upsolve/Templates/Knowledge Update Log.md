# Knowledge Update Log

The audit trail proving interviews → better canonical notes. One row per edit.

| Date | Trigger (interview/round) | Canonical note | What changed |
|---|---|---|---|
| 2026-07-06 | [[Q2 Software]] R1 | [[AWS]] | **Created note.** Edge layer (API GW/ALB/WAF/CloudFront), rate limiting, IAM/secrets, authN/authZ + Okta/OIDC, governance (CloudTrail/Config/Orgs/SCP), ECS vs EC2, push/pull ingestion |
| 2026-07-06 | [[Q2 Software]] R1 | [[AWS]] | Added **durable push ingestion** section — API GW→SQS/Kinesis direct integration, pre-signed S3, DLQ, always-warm-consumer fallback; the "LB can't write to SQS / accept without dropping" gap |

> Rule: the *explanation* goes in the canonical note. This log only records **that** it happened.
