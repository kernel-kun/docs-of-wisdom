# Question Bank — AWS

Index only. Answers live in [[AWS]] (canonical). IaC provisioning → [[Terraform Rubric]].

| Question | Companies | Diff | Conf | Canonical | Round |
|---|---|---|---|---|---|
| ALB vs API Gateway — where does rate limiting live? | [[Q2 Software]] | med | 🔴 | [[AWS]] | R1 |
| Edge layering: CloudFront / WAF / API GW / ALB — order & purpose | [[Q2 Software]] | hard | 🔴 | [[AWS]] | R1 |
| Where do authN/authZ sit? Okta/OIDC integration | [[Q2 Software]] | hard | 🔴 | [[AWS]] | R1 |
| IAM & secret management in a design | [[Q2 Software]] | med | 🟡 | [[AWS]] | R1 |
| Audit/governance for a multi-consumer platform (CloudTrail/Config/Orgs) | [[Q2 Software]] | med | 🔴 | [[AWS]] | R1 |
| ECS vs EC2 (image-based packaging) | [[Q2 Software]] | med | 🟢 | [[AWS]] | R1 |
| Push vs pull ingestion across N cloud accounts | [[Q2 Software]] | med | 🟡 | [[AWS]] | R1 |
| Durable push ingestion — accept every request, no drops under burst/cold-start (LB can't reach SQS) | [[Q2 Software]] | hard | 🔴 | [[AWS#Durable push ingestion — never lose an authenticated request (interview gap)\|AWS]] | R1 |
| Fan-out one event to many consumers — SQS vs SNS vs EventBridge vs Kinesis (SQS has no partitions, is point-to-point) | [[Q2 Software]] | hard | 🔴 | [[AWS#Fan-out / pub-sub — one event to many consumers (interview gap)\|AWS]] | R1 |
| Connect to EC2 with no inbound ports open — how SSM works (outbound-443, agent-initiated) | [[Lucidity]] | hard | 🔴 | [[AWS#Remote access to EC2 with no inbound ports — SSM Session Manager (interview gap)\|AWS]] | HM |
| How do you authenticate to AWS / assume a role (CLI + console switch-role) | [[Ericsson]] | med | 🔴 | [[AWS#How you authenticate & assume roles (interview gap)\|AWS]] | R1 |
| Private-subnet EC2 outbound — IGW (VPC-level) vs NAT gateway (in a subnet) | [[Ericsson]] | med | 🟡 | [[AWS#VPC egress — IGW vs NAT gateway, what attaches where (interview gap)\|AWS]] | R1 |
