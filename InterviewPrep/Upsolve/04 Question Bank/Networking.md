# Question Bank — Networking

Index only. Canonical note [[Networking]] does not exist yet — **create it in a top-level `Networking/` knowledge folder** on first real gap, then link here.

| Question | Companies | Diff | Conf | Canonical | Rounds |
|---|---|---|---|---|---|
| TCP `TIME_WAIT` meaning | [[Akamai-SRE]] | med | 🟡 | [[Networking]] |  |
| VoIP uses UDP — why | [[Akamai-SRE]] | easy | 🟢 | [[Networking]] |  |
| HTTPS min port config (client/server) | [[Akamai-SRE]] | easy | 🟢 | [[Networking]] |  |
| Max window size from K sequence bits | [[Akamai-SRE]] | hard | 🔴 | [[Networking]] |  |
| MAC flooding / CAM table exhaustion | [[Akamai-SRE]] | med | 🟡 | [[Networking]] |  |
| Inter-VLAN routing requirement | [[Akamai-SRE]] | med | 🟡 | [[Networking]] |  |
| DNS: `/etc/hosts` before resolv.conf | [[Akamai-SRE]] | easy | 🟢 | [[Networking]] |  |
| Which DNS records change on IP move (A/MX) | [[Akamai-SRE]] | med | 🟡 | [[Networking]] |  |
| What is mTLS and how does it actually work? | [[Lucidity]] | hard | 🔴 | [[TLS & mTLS]] | HM |
| Difference between keys and certificates (not just a keypair) | [[Lucidity]] | hard | 🔴 | [[TLS & mTLS]] | HM |
| Role of the Certificate Authority / chain of trust | [[Lucidity]] | hard | 🔴 | [[TLS & mTLS]] | HM |
| Secure microservice comms across clusters — service mesh mTLS | [[Lucidity]] | hard | 🟡 | [[Istio]] | HM |
| RFC 1918 private ranges & their CIDR sizes (not all /8; 172.16–31 only) | [[Lucidity]] | med | 🔴 | [[Networking]] | HM |
| Switch vs router — L2/MAC vs L3/IP, when each is used | [[Lucidity]] | med | 🔴 | [[Networking]] | HM |
| Connect two same/overlapping-CIDR networks without IP conflict (NAT) | [[Lucidity]] | hard | 🔴 | [[Networking]] | HM |
| How one Linux host isolates public+private networks (namespace/bridge/veth/tap) | [[Lucidity]] | hard | 🔴 | [[Networking]] | HM |
| NAT gateway under the hood — SNAT + conntrack at the Linux/netfilter level | [[Lucidity]] | hard | 🔴 | [[Networking#NAT gateway — how it works under the hood (Linux/netfilter)\|Networking]] | HM |

> `[[Networking]]` is a forward-reference stub until the canonical note exists. PKI/TLS gaps route to the dedicated [[TLS & mTLS]] note.
