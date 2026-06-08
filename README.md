> **OpenA2A specs** · [did:opena2a](https://specs.opena2a.org/specs/did-opena2a) · [AIP](https://specs.opena2a.org/specs/aip) · **ATX** · [ATP](https://specs.opena2a.org/specs/atp) · [AAP](https://specs.opena2a.org/specs/aap) · [AIM](https://specs.opena2a.org/specs/aim) · [all specs ↗](https://specs.opena2a.org)

# atx-spec

Architecture specifications for the **Agent Trust eXtension (ATX)** credential format and the **Agent Trust Protocol (ATP)** that issues, verifies, and revokes it.

ATX is a signed, self-contained credential carried by every AI agent. It is analogous to a TLS certificate but contains agent identity, scan results, capabilities, and behavioral profile. Local verification under 5ms. Ed25519 + ML-DSA-65 hybrid signatures mandatory at v1.

## Documents

| Doc | Scope |
|---|---|
| [`core.md`](core.md) | ATX schema and lifecycle, ATP protocol surface (5 planes), local-verify algorithm, federation, transparency log, product integration map |
| [`scalability.md`](scalability.md) | Scaling properties: revocation propagation, CRL caching, transparency log monitor protocol |
| [`sovereign-federation.md`](sovereign-federation.md) | Multi-authority cosigning, sovereign-node operation, cross-org trust |

## Status

Architecture specifications, v1.0 (May 2026). Reference implementation tracked in [`opena2a-org/agent-identity-management`](https://github.com/opena2a-org/agent-identity-management) (AIM).

ATX/ATP/AIP cross-reference: see [`opena2a-org/agent-trust-protocol`](https://github.com/opena2a-org/agent-trust-protocol) for the ATP wire protocol spec and [`opena2a-org/agent-identity-protocol`](https://github.com/opena2a-org/agent-identity-protocol) for the AIP identity spec.

## Conformance

Byte-stable conformance fixtures and SDK-independent reference verifiers for ATX v1.0 live at [`opena2a-org/atx-conformance`](https://github.com/opena2a-org/atx-conformance). The suite ships eight fixtures (baseline valid, hybrid Ed25519 plus ML-DSA-65, threshold 2-of-3 cosignature, revoked, expired, wrong-issuer, tampered-signature, malformed-schema), each pinned by SHA-256 in `MANIFEST.sha256`, plus two reference verifiers: Go (full hybrid via Cloudflare CIRCL) and Python (Ed25519 only; post-quantum verification out of scope for the Python stdlib stack). Both verifiers report 8 of 8 PASS against the shipped fixture set.

Second-party implementations of ATX issuance or verification are tracked on [a2aproject/A2A#1876](https://github.com/a2aproject/A2A/issues/1876).

## License

Apache 2.0. See [LICENSE](LICENSE).
