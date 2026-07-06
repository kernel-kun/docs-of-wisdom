# Networking

Canonical networking note. Single source of truth for IP/CIDR, L2 vs L3, NAT, and Linux/hypervisor virtual networking. TLS/certs → [[TLS & mTLS]]. AWS VPC specifics → [[AWS]].

---

## Private IP ranges (RFC 1918) — the three blocks have *different* sizes

The interview gap (Lucidity HM): assuming the private ranges are all `/8`. They are **not** — RFC 1918 defines three blocks with three different prefix lengths:

| Block | CIDR | Range | Size |
|---|---|---|---|
| 24-bit | `10.0.0.0/8` | 10.0.0.0 – 10.255.255.255 | 1 × /8 |
| 20-bit | `172.16.0.0/12` | 172.16.0.0 – **172.31**.255.255 | 16 × /16 |
| 16-bit | `192.168.0.0/16` | 192.168.0.0 – 192.168.255.255 | 1 × /16 |

- Key correction: `172.x` is **not** all private — only `172.16`–`172.31` (that's the `/12`). `172.0.0.0/8` as a whole is public.
- Within any block you carve **smaller subnets** (a VPC of `10.0.0.0/16`, subnets `10.0.1.0/24`, etc.). The block size and your subnet size are independent choices.
- Worth naming too: `100.64.0.0/10` (RFC 6598, carrier-grade NAT) and `169.254.0.0/16` (link-local, RFC 3927).

## Switch vs router — L2 vs L3 (the "are you sure?" trap)

The single distinction: **a switch moves frames *within* one network; a router moves packets *between* networks.**

| | Switch | Router |
|---|---|---|
| OSI layer | **L2** (data link) | **L3** (network) |
| Forwards by | **MAC address** (CAM table) | **IP address** (routing table) |
| Scope | One subnet / broadcast domain | Connects **different** subnets |
| Same CIDR both sides? | Yes — that's its whole job | No — needs distinct networks to route between |

- **Two different private subnets → router.** They're different L3 networks; a router (with a route/next-hop for each) forwards between them. This was the right instinct.
- **Same subnet → switch.** Hosts in one CIDR find each other by ARP and a switch forwards by MAC — no router involved. Reaching for a router here is the mistake the "router or switch?" probe was testing.

## Two networks with the *same/overlapping* CIDR — connecting without IP conflict

You **can't route directly** between two networks that share a CIDR: an address like `10.0.1.5` is now ambiguous (which side?). Options:

- **NAT (the usual answer)** — remap one side into a non-overlapping range so addresses are unambiguous across the boundary (SNAT/DNAT, or dual/twice-NAT for both directions). On Linux this is `iptables`/`nftables` (netfilter).
- **Re-IP one network** — cleanest long-term; often impractical.
- **AWS parallel:** VPC **peering** and **Transit Gateway** *refuse* overlapping CIDRs for exactly this reason; you either re-IP or front one side with a **private NAT gateway**.

## NAT gateway — how it works under the hood (Linux/netfilter)

The AWS concept is simple (private-subnet hosts get **outbound** internet without being publicly reachable); the interview gap (Lucidity HM) was the **Linux mechanism**. A NAT gateway is just **stateful source-NAT + connection tracking** in the kernel.

**What NAT does:** rewrite IP-header addresses so many private hosts share one public IP. For outbound it's **SNAT** (source NAT) — really NAPT/PAT, overloaded by port:

- A packet leaves a private host: `src 10.0.1.5:34512 → dst 93.184.x.x:443`.
- The NAT box rewrites the **source** to its own public IP and a chosen source port: `src 52.1.2.3:51000 → dst 93.184.x.x:443`, and forwards it out.
- **conntrack** (netfilter's connection-tracking table) records the mapping `10.0.1.5:34512 ↔ 52.1.2.3:51000 ↔ 93.184.x.x:443`.
- The reply comes back to `52.1.2.3:51000`; conntrack matches it, **reverse-NATs the destination** back to `10.0.1.5:34512`, and delivers it to the original host.

**Why private→out works but internet→in doesn't:** outbound traffic *creates* the conntrack entry; unsolicited inbound has **no matching entry** → dropped. That statefulness *is* the security property (and why a NAT GW is egress-only; inbound needs explicit **DNAT**/port-forward in PREROUTING, which is what a load balancer does).

**The Linux plumbing:**
- Enable forwarding: `net.ipv4.ip_forward=1`.
- SNAT rule in the `nat` table's **POSTROUTING** chain (applied after the routing decision, on the way out):
  - `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE` — **MASQUERADE** picks the outgoing interface's IP dynamically (for dynamic/DHCP public IPs).
  - `... -j SNAT --to-source 52.1.2.3` — **SNAT** to a fixed IP (faster; no per-packet IP lookup). An **AWS NAT gateway ≈ SNAT to its Elastic IP**, made HA/managed.
- DNAT (inbound port-forward, the opposite direction) goes in **PREROUTING**.
- Packet path: private host's default route → NAT box → PREROUTING → route decision → FORWARD → **POSTROUTING (SNAT)** → out; reply → PREROUTING (conntrack reverse-NAT) → FORWARD → back to host.

**AWS mapping:** *public subnet* = route table sends `0.0.0.0/0` to an **Internet Gateway** (the IGW does 1:1 NAT for instances that have a public IP). *Private subnet* = route table sends `0.0.0.0/0` to a **NAT gateway** that lives in a public subnet, holds an EIP, and does exactly the SNAT+conntrack above — so private hosts reach the internet but stay unreachable from it.

## How one Linux host runs several isolated networks (hypervisor level)

The gap: "how does Linux give a machine a private *and* a public network with logical separation?" It's not physical NICs — it's kernel virtual-networking primitives:

- **Network namespace** — an isolated copy of the network stack: its own interfaces, **routing table**, ARP cache, and netfilter rules. This is the kernel primitive that provides the logical separation. (`ip netns`.)
- **veth pair** — a virtual cable: two linked interfaces, one end in each namespace, to wire namespaces together.
- **Linux bridge** — an **in-kernel virtual switch** (L2). Attach interfaces to it and it forwards frames by MAC, exactly like a physical switch. (`ip link add type bridge`; see the *Linux Bridge* PDFs in this folder.)
- **TAP / TUN** — virtual NICs. **TAP** carries L2 Ethernet frames, **TUN** carries L3 IP packets. A hypervisor (KVM/QEMU) hands each VM a **tap** device as its NIC.

**Putting it together (the hypervisor model):** each VM's NIC is a **tap** device on the host, plugged into a **bridge** (virtual switch). Run a *public* bridge (uplinked to the physical NIC) and a *private* bridge (isolated, or NAT'd via netfilter) → two separate L2 domains on one physical machine. That separation is why the same address can exist on two bridges without conflict until you deliberately **route + NAT** between them via the host's routing table. This is precisely how Docker (`docker0` bridge + a veth per container) and cloud VPCs work under the hood.

---

Related: [[TLS & mTLS]] · [[AWS]] (VPC, SSM, NAT gateway) · *Linux Bridge - Part 1/2* PDFs (this folder)
