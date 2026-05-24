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

## License

Apache 2.0. See [LICENSE](LICENSE).
