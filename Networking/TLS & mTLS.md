# TLS & mTLS

Canonical note for certificate-based authentication: keys vs certificates, the Certificate Authority, and how the TLS / mTLS handshake actually proves identity. Mesh-level usage → [[Istio]]. Edge TLS termination → [[AWS]].

The interview trap (Lucidity HM round): explaining mTLS as "each side swaps a public/private keypair like GPG/PGP." That's the **keys** story and it misses the whole point — the **certificate** and the **CA**. Below is the correct framing.

---

## Keys vs certificates — the distinction that matters

- A **key pair** (public + private) gives you *cryptography*: sign with private, verify with public; encrypt with public, decrypt with private. But a bare public key proves only **"I hold the matching private key."** It says **nothing about who you are.** Anyone can generate a keypair in one command — a raw public key has no identity and no trust attached. (GPG/PGP is exactly this: keypairs bound to identity by an informal *web of trust*, not an authority.)
- A **certificate** = **public key + identity + a CA's signature over both.** The identity is the Subject (a DNS name like `api.acme.com`, or a workload identity like a SPIFFE ID). The certificate is a notarized statement: *"The CA vouches that this public key belongs to this identity, valid until this date."* The binding of key↔identity, attested by a trusted third party, is precisely what a raw keypair lacks.

> One-liner for the interview: **a key proves possession; a certificate proves identity.** A certificate is a public key that a CA has signed a name onto.

## The Certificate Authority (CA) and the chain of trust

- The **CA** is the trusted third party whose job is to sign certificates. Both parties are pre-configured to trust a common **root** (its cert lives in their trust store), usually via one or more **intermediate** CAs.
- Trust is **transitive**: I trust the CA → the CA signed your cert → therefore I trust that your public key really is yours. No prior direct relationship between the two peers is needed — that's the entire value of a CA over web-of-trust: it **scales**.
- Validating a presented cert = four checks: (1) its signature chains up to a **trusted root**, (2) not expired, (3) not **revoked** (CRL / OCSP), (4) the **Subject matches the identity you expected**. All four must pass.

## Plain TLS (one-way) vs mTLS (mutual)

| | Presents a cert | Verifies the peer | Who's anonymous |
|---|---|---|---|
| **TLS** (HTTPS) | **Server only** | Client verifies server | The **client** — its identity is established later at the app layer (password, token, session) |
| **mTLS** | **Both sides** | Each verifies the other | Nobody — both identities are cryptographically proven **before any app data** |

mTLS is just TLS with the client **also** presenting and proving a certificate. That mutual, up-front, cryptographic identity is why mTLS is the default for **zero-trust service-to-service** auth: neither service trusts the network, each proves who it is by certificate.

## What the handshake actually does (correcting the muddle)

The TLS handshake bundles two *separate* things people conflate:

1. **Key agreement** (e.g. ECDHE) → derives a shared **session key** to encrypt the channel. This is symmetric-crypto plumbing and is the same with or without client certs.
2. **Authentication via the certificate** → this is the part the certificate is for, and it's **two distinct steps**:
   - **Identity is authentic** — the peer sends its cert; you verify the **CA's signature** on it (the chain-of-trust checks above). This proves the *name* is vouched for.
   - **The sender actually owns it** — a cert can be copied, so the sender must prove it holds the matching **private key**. In TLS 1.3 it signs the handshake transcript (`CertificateVerify`) with its private key; the peer verifies that signature with the **public key inside the cert**. Only the true key-holder can produce it.

**mTLS = do step 2 in both directions.** Server proves its identity+ownership to the client, and the client proves its identity+ownership to the server, each validated against the trusted CA.

> So the corrected answer isn't "they exchange certificates and a TLS handshake happens." It's: **each side presents a CA-signed certificate (public key + identity), the peer verifies the CA's signature to trust the identity, and the presenter signs part of the handshake with its private key to prove it truly owns that certificate — done mutually.** The CA is what makes it trustworthy; the private-key signature is what makes it un-forgeable.

---

Related: [[Istio]] (mesh mTLS — istiod as CA, SPIFFE identities) · [[AWS]] (ACM, edge TLS termination) · [[Networking]] (TCP/TLS transport)
