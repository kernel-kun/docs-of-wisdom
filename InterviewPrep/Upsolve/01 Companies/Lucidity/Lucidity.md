# Lucidity

- **Role:** _tbd_
- **Status:** `Onsite` — multiple rounds; documenting the Hiring Manager (final) round
- **Comp target:** _tbd_

History and reflections only. Technical answers live in [[TLS & mTLS]], [[Istio]], [[AWS]], [[Networking]].

## Timeline
| Date | Event | Result |
|---|---|---|
| _prior_ | Earlier rounds | — |
| 2026-06-08 | Hiring Manager round (final) | ⚠️ fell short — security deep-dive |

---

## Hiring Manager round — 2026-06-08
- **Interviewer:** Sharad Singla (Hiring Manager) · **Felt:** 👎 (weakest round)

**Q — secure microservice-to-microservice communication.** 🔴 → [[TLS & mTLS]] · [[Istio]]
Scenario: services must communicate securely — same cluster, across clusters, or even pod ↔ on-prem / no-cluster. I scoped it down to **cluster-to-cluster** (deliberately excluded bare-metal), and proposed **Istio** for the secure transport because it supports **mTLS**. Good call on the tool; the deep-dive is where it fell apart.

**⚠️ Knowledge gaps (all one theme — certificate-based auth):**
1. **What mTLS actually is.** I said "each side exchanges certificates and they communicate securely," then when probed on *how*, I described a generic **TLS handshake / key exchange** — the wrong framing. It doesn't capture what the certificate + mutual verification add. → [[TLS & mTLS#Plain TLS (one-way) vs mTLS (mutual)|TLS & mTLS]]
2. **Keys vs certificates.** Explained certificates using the **GPG/PGP public-private keypair** model — missed that a certificate is a **public key + identity signed by a CA**, not just a keypair. → [[TLS & mTLS#Keys vs certificates — the distinction that matters|TLS & mTLS]]
3. **Certificate Authority.** Completely missed the **CA and chain of trust** — the whole reason certificates are trustworthy at scale. → [[TLS & mTLS#The Certificate Authority (CA) and the chain of trust|TLS & mTLS]]

**Root cause:** knowledge gap (PKI fundamentals), not communication. Chose the right tool (Istio/mTLS) but couldn't explain the primitive underneath it. Canonical [[TLS & mTLS]] created from this round.

**Action items:**
- [ ] Be able to state cold: *key proves possession, certificate proves identity (public key + name, CA-signed)* — due 2026-07-13 → [[TLS & mTLS]]
- [ ] Explain the TLS 1.3 handshake split — CA-signature check (identity) vs `CertificateVerify` (ownership), done mutually for mTLS — due 2026-07-13 → [[TLS & mTLS]]
- [ ] Explain how Istio delivers mTLS (istiod = CA, SPIFFE identity, PeerAuthentication STRICT, shared root across clusters) — due 2026-07-13 → [[Istio]]

---

### Q — connect to EC2 with no inbound ports open. 🔴 → [[AWS]]
Answer: **SSM Session Manager**. Knew the *name* but not the mechanism. Gap: didn't know it's **agent-initiated, outbound-443-only** (no inbound rule needed), the IAM instance profile requirement, or the `ssm`/`ssmmessages`/`ec2messages` endpoints (incl. PrivateLink for fully-private instances). → [[AWS#Remote access to EC2 with no inbound ports — SSM Session Manager (interview gap)|AWS]]

### Q — networking fundamentals (fell short hardest). 🔴 → [[Networking]]
1. **RFC 1918 CIDR sizes** — thought the private ranges were uniform `/8`. Correct: `10.0.0.0/8`, `172.16.0.0/12` (only 172.16–172.31, not all of 172), `192.168.0.0/16`. → [[Networking#Private IP ranges (RFC 1918) — the three blocks have *different* sizes|Networking]]
2. **Router vs switch** — couldn't state the difference or when each applies; interviewer challenged "router or switch?". Correct: switch = **L2, within** a subnet (MAC); router = **L3, between** subnets (IP). → [[Networking#Switch vs router — L2 vs L3 (the "are you sure?" trap)|Networking]]
3. **Same/overlapping CIDR interconnect** — how two same-CIDR networks talk without IP conflict. Answer: can't route directly → **NAT** (or re-IP); AWS peering/TGW reject overlaps. → [[Networking#Two networks with the *same/overlapping* CIDR — connecting without IP conflict|Networking]]
4. **Linux/hypervisor network isolation** — how one host runs a public + private network with logical separation. Answer: **network namespaces** (isolated stack), **bridge** (virtual L2 switch), **veth**, **tap** NICs per VM. → [[Networking#How one Linux host runs several isolated networks (hypervisor level)|Networking]]
5. **NAT gateway under the hood** — how private/public subnets communicate at the Linux level. Answer: stateful **SNAT + conntrack** in netfilter (POSTROUTING MASQUERADE/SNAT); outbound creates conntrack state, unsolicited inbound has none → dropped. → [[Networking#NAT gateway — how it works under the hood (Linux/netfilter)|Networking]]

**Action items:**
- [ ] Recite RFC 1918 blocks + sizes cold; explain why `172.0.0.0/8` isn't all private — due 2026-07-13 → [[Networking]]
- [ ] State switch (L2/MAC) vs router (L3/IP) in one line + the overlapping-CIDR→NAT case — due 2026-07-13 → [[Networking]]
- [ ] Sketch the hypervisor model: VM tap → bridge → namespace isolation, route+NAT between bridges — due 2026-07-13 → [[Networking]]
- [ ] Read AWS SSM Session Manager docs (linked in [[AWS]]) — due 2026-07-13 → [[AWS]]

More questions from this round to be added. Knowledge edits logged in [[Knowledge Update Log]].
