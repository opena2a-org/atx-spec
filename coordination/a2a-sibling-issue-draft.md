# Sibling issue draft: AIP + ATP + ATX spec set for `a2aproject/A2A`

> **Status: DRAFT, DO NOT FILE.** Filing is gated on Abdel's review and explicit go-ahead. Save for record; iterate as needed.

> Target repo: `a2aproject/A2A`. Will be filed as a sibling to:
> - `a2aproject/A2A#1496` (A2A Identity Trust Framework, in flight, not merged)
> - `a2aproject/A2A#1786` (CTEF claim envelopes)
> - `a2aproject/A2A#1575` (APS, Agent Passport System)
> - `a2aproject/A2A#1829` (Envoys signature/v1)

---

## Title

Coordination visibility: AIP + ATP + ATX spec set (OpenA2A). Substrate composition with A2A-IDF.

## Body

OpenA2A maintains an open spec set that composes with the A2A Identity Trust Framework (A2A-IDF, `#1496` in flight) at the identity, credential, and transparency-log layers. **Filing this issue for coordination visibility only.** Not requesting A2A-spec inclusion. Not claiming a Coordination Map row on PR `#1850` today. Not at the maturity bar APS (`#1575`) and CTEF (`#1786`) carry. This issue exists so peers tracking the four-layer split (wire signature, identity framework, identity claims, delegation and continuity) know the work exists and can plan around it.

### What the set is

| Spec | Scope | Source |
|---|---|---|
| **AIP**, Agent Identity Protocol, v1.0.0-draft | Identity, capabilities, verification, trust scoring, governance, lifecycle, audit | [`opena2a-org/agent-identity-protocol`](https://github.com/opena2a-org/agent-identity-protocol) |
| **ATP**, Agent Trust Protocol, v1.0.0-rc1 | Trust proofs, RFC 6962 transparency log, federation, discovery, revocation | [`opena2a-org/agent-trust-protocol`](https://github.com/opena2a-org/agent-trust-protocol) |
| **ATX**, Agent Trust eXtension credential format, v1.0 | Signed self-contained credential carried by every agent. Local verification under 5ms target. Spec mandates Ed25519 plus ML-DSA-65 hybrid signatures at v1. | [`opena2a-org/atx-spec`](https://github.com/opena2a-org/atx-spec) |

All three use the unified DID method `did:opena2a:<type>:<id>` where `<type>` is one of `agent`, `authority`, `publisher`, `mcp_server`, `a2a_agent`, `skill`, `ai_tool`, `llm`. DID method unification across the three specs is the most recent reconciliation work; commits landed today on the working branches linked above.

### Composition with A2A-IDF (`#1496`)

| A2A-IDF surface | OpenA2A spec | Notes |
|---|---|---|
| v1.0 §1 to §4 identity verification (Level 0 / 1 / 2) | AIP §3 Identity plus AIP §5 Verification | AIP levels (`Local`, `Managed`, `Federated`) describe deployment topology and are orthogonal to A2A-IDF levels (`SELF_ASSERTED`, `DOMAIN_VERIFIED`, `ORGANIZATION_VERIFIED`), which describe provenance of identity binding. Both apply independently. AIP §2.1 documents the orthogonality explicitly. |
| v1.0 §6 wire signature (Ed25519, RFC 9421) | ATX §1.1 signature block | ATX signature base is RFC 9421-compatible. Spec mandates Ed25519 plus ML-DSA-65 hybrid at v1. |
| v1.0 attestation envelope | ATX schema (`scanSummary`, `behavioralProfile`, `signatures`) | Direct shape match. |
| v1.1 vouching attestations (this PR) | AIP §6 Trust Scoring | Adjacent. AIP §6 defines the interface (factor set, weights, composite rule). The AIM `TrustCalculator` is the named reference number. |
| v1.2 federated revocation log | ATP §5 Transparency Log plus ATP §6 Federation | RFC 6962 Merkle tree. Architecturally aligned with the design A2A-IDF v1.2 sketches. |
| v2.0 PQC algorithm agility | ATX hybrid Ed25519 plus ML-DSA-65 at v1 | ATX spec mandates hybrid at v1. Reference implementation issues Ed25519-only today; hybrid issuance call-site flip is imminent. The spec mandate and the reference-implementation status are two distinct statements; both are accurate as written. |

### What this set does that APS, CTEF, and Envoys do not

This differentiation is why the set exists alongside the peers in the Coordination Map, not as a parallel claim.

- **Federated revocation log (ATP §5 plus §6).** APS v1.2 composes with an external append-only revocation registry by reference, not as the registry itself. ATP §5 specifies the registry: RFC 6962 binary Merkle tree, signed tree heads, monitor protocol, federation cosigning. This is the artifact the v1.2 federated revocation row in PR `#1850` needs at the protocol level.
- **ATX credential lifecycle (ATX core §1.2).** Build-plugin issuance bound to content hash plus scan results, 7-day expiry forces rescan as a hygiene primitive, push-propagation revocation under 60 seconds. CTEF carries claim envelopes. APS carries delegation chains and bilateral receipts. ATX carries a signed credential the agent presents in every request. Different artifact at a different layer.
- **Hybrid PQC at v1 in the spec.** Every ATX SHALL carry Ed25519 plus ML-DSA-65 signatures per ATX core §1.1. A2A-IDF v2.0 schedules PQC as a 2027 cycle. APS v2.0 plans PQC as a profile update. ATX puts hybrid in v1 of the spec. Reference-implementation status is tracked separately (see "Honest coverage scoping" below).
- **Local-verify target under 5ms (ATX core §1.3).** No issuing node on the verification hot path. This is the scaling discipline the four-layer composition needs to deploy at billion-agent scale. Implementation today exists in the Go verifier package; cross-language SDK coverage is on the roadmap.

None of these conflict with APS, CTEF, or Envoys. They are substrate at a different layer of the same composition.

### Reference implementation

- **AIM** ([`opena2a-org/agent-identity-management`](https://github.com/opena2a-org/agent-identity-management)) is the reference implementation of AIP and the credential issuer for ATX.
- **OpenA2A Registry** ([`opena2a-org/opena2a-registry`](https://github.com/opena2a-org/opena2a-registry)) operates the CA infrastructure (issuance service, threshold signing, RFC 6962 transparency log, CRL service, federation).

### Honest coverage scoping (the reference implementation is not all-shipped)

AIP-SPEC Appendix A.1 names this explicitly. The summary:

| AIP / ATP / ATX section | AIM and Registry status | Notes |
|---|---|---|
| AIP §3 Identity (Ed25519, agent ID, DID) | Shipped | Server-side keygen, registered Ed25519 public key. |
| AIP §3 Hybrid Ed25519 plus ML-DSA-65 signing | Spec mandate at v1; partial in implementation | Full hybrid signing stack (CIRCL `mldsa65`, hybrid wrapper, verifier hybrid path) is implemented and tested. The issuance call site does not yet invoke `HybridSign()`; issued credentials are Ed25519-only today. Call-site flip is imminent. |
| AIP §4 Capabilities and FGA enforcement | Shipped | 5-step blocking FGA engine (capability, attribute, context, chain, intent). |
| AIP §4 JIT capability grants with TTL | Partial | TTL today is for PAM (Privileged Access Management) emergency-escalation only. Routine capability grants are static. |
| AIP §5 Verification (challenge-response) | Shipped | `/authorize` endpoint exists. Direct SDK callers are pending. |
| AIP §6 Trust Scoring (9-factor) | Shipped | `TrustCalculator` with audited weights summing to 100. |
| AIP §6.5 ATP integration | Not yet integrated | ATP-SPEC v1.0.0-rc1 is recent. Cross-wiring of AIP trust scores into ATP transparency log is upcoming work. |
| AIP §9 Audit | Shipped, not cryptographically signed | Append-only at the repository layer. Hash-chain or signed-log fields are not present today; integrity rests on database access control. |
| ATX §1.1 schema | Shipped under prior code name (ATC) | ATC to ATX code rename is open and gated on a separate todo. The on-the-wire schema matches the published spec; the type identifier in code is the rename surface. |
| ATX §1.2 lifecycle (build-plugin issuance) | Plugin not yet shipped as code | Specified in ATX core §3.1; reference implementation pending. |
| ATP §5 Transparency Log (RFC 6962) | Shipped (binary Merkle tree, signed tree heads, consistency proofs) | Endpoint serving live signed tree heads and consistency proofs is in the registry. |
| ATP §6 Federation | Single-authority today | Federation routes and bilateral cosignature flow exist. Multi-authority operating consensus is the next federation milestone. |
| Local offline verification | Go-only | Standalone Go verifier package exists. TypeScript, Python, and Java SDKs do not yet ship a local-verify library. |

### Where this set does not yet meet the (a)(b)(c) bar this Coordination Map applies

The bar the discussion thread on PR `#1850` settled around for new Coordination Map entries:

- **(a)** Independent implementation of the wire format by a different party.
- **(b)** Reference plugin landed and exercised.
- **(c)** Peer-cosigned conformance fixture set comparable to APS and CTEF (`aim-did-rfc9421/*` and equivalents).

Today this spec set does not fully meet that bar.

- **(a) Independent implementation:** AIM is the only implementation today. Recruitment of a second-party implementer of ATX issuance and verification is on the path.
- **(b) Reference plugin landed:** the ATX build plugin (per ATX core §3.1) is specified but not yet shipped as code.
- **(c) Peer-cosigned conformance fixtures:** none yet exist for AIP, ATP, or ATX comparable to the `aim-did-rfc9421/*` fixtures that CTEF and APS cite. Building these is the next concrete substrate work.

When all three land, the matrix-inclusion claim becomes legitimate. Until then, this issue is the substrate visibility post, not the matrix entry.

### Coordination posture

Apache 2.0 across all three spec repos. No normative requirement on A2A-IDF. Reference implementation exists, is named explicitly, and is not privileged in the conformance suite design.

Open to peer review at the same maturity bar APS and CTEF carry. Independent implementations welcome. Spec text is open for redlines.

cc reviewers from the PR `#1496` and PR `#1850` threads for visibility: `@aeoess`, `@kenneives`, `@jschoemaker`, `@lawcontinue`.

---

## Filing checklist (for Abdel)

Before this issue is filed on `a2aproject/A2A`, the following should be true. None are blocking for the draft itself, but the public-facing claim improves materially if each is checked.

- [ ] Decision 1 (DID method unification) PR on `agent-identity-protocol` merged to `main`.
- [ ] Decision 1 (DID method unification) PR on `atx-spec` merged to `main`.
- [ ] Decision 2 / 3 / 4 edits (AIM as reference, level orthogonality, 9-factor) merged to `main` on `agent-identity-protocol`.
- [ ] Confirm OTel SemConv issue link is `open-telemetry/semantic-conventions-genai#180` if cited in any follow-up comment.
- [ ] Confirm "PR `#1496` is in flight, not merged" framing wherever the issue references it.
- [ ] HMA-related references: no specific check counts cited in the issue body or follow-up comments.
- [ ] em dashes scanned out of body if any new edits are made.

## Out of scope for the issue itself (do not include in body)

- The four [CHIEF-CA] decisions internal rationale.
- AIM source code paths.
- Detailed implementation timeline for the (a)(b)(c) gates above. The body acknowledges the gaps; specific dates are a separate forum.
- ATC to ATX code rename mechanics. The issue references the rename neutrally without exposing the internal todo path.
