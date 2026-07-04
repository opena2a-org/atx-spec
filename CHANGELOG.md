# Changelog

All notable changes to the ATX architecture specifications are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Document
versions follow the OpenA2A spec-family ladder `MAJOR.MINOR.PATCH-{draft|rcN|final}`;
the ATX **credential wire format** version (`atcVersion` `1.0` / `1.1`) is a separate
identifier registered in `core.md` §14.

## [Unreleased]

### Changed

- §12 and README conformance-suite counts synced to the 18-fixture suite
  (atx-conformance#14 added the three rule-5 degenerate declaredPurpose
  fixtures).

- §1.3a.2 rule 5 pins the degenerate `declaredPurpose` inputs the reference
  verifiers disagreed on (atx-conformance#11): emptiness is a JSON-parse-level
  property (whitespace variants of `{}` are the empty object), and any other
  present value — including non-object values — MUST enter the TBS verbatim,
  so unsigned injected purpose content breaks the signature instead of being
  silently normalized away.
- `schemas/atx-credential-v1.1.schema.json`: `declaredPurpose` now also
  accepts the empty object (wire-tolerated, treated as absent), matching
  rule 5.

### Added

- `schemas/atx-credential-v1.1.schema.json`: machine-readable JSON Schema
  (draft 2020-12) for the ATX credential wire form, derived from §1.1/§1.3a/§1.5
  with the atx-conformance fixtures as ground truth. All 15 fixtures validate as
  intended (14 shape-valid; `malformed-schema` fails on exactly the `atcVersion`
  registry enum).
- `scripts/validate_examples.py` + `schemas/examples-map.json` + CI workflow:
  every schema is metaschema-checked and the §1.1 example is validated against
  the schema on every push and PR.

### Changed

- `core.md` §1.1: the credential illustration is now wire-shape-accurate and
  schema-valid — field name `atcVersion` (previously shown under the `atxVersion`
  alias), bare-hex `contentHash`, provenance-URI `buildAttestation`,
  `scanSummary` with `highFindings` and the wire spellings `cryptoServe` /
  `no-weak-crypto`, `trustScore` on the wire's 0-100 scale, full-DID `keyId`
  values, and the issuance envelope fields `id` / `revoked` / `createdAt`.
- `core.md` §2 and §14: the `did:opena2a` type-prefix set now matches the
  did-method registry — `registry` added; `a2a_agent` documented as a deprecated
  legacy alias of `agent`, not a registered type.

## [1.1.0] - 2026-07-03

### Added

- `core.md`: qualified document version/status header (credential format v1.1
  normative and shipped; ATP wire detail normative in ATP-SPEC v1.0.0-rc1;
  explicit note that this document never upgrades the maturity of the AIP/AAP
  layers beneath it).
- `core.md`: BCP 14 (RFC 2119 / RFC 8174) conventions section anchoring the
  MUST/SHOULD/MAY language that §1.3a and §1.5 already used.
- `core.md` §12 Conformance: three conformance targets (credential, issuer,
  verifier) and the normative link to the `atx-conformance` suite (15 byte-pinned
  fixtures, `jcs-vectors` cross-language byte-agreement gate, machine-readable
  `conformance.json` profile).
- `core.md` §13 Security considerations: consolidated threat analysis with
  agent-threat-matrix technique IDs (forgeable v1.0 fields / T-4001, bearer
  replay / T-5004, build-tooling compromise / T-9006 + T-9003, DID key
  substitution / T-4007, chain abuse / T-4004, revocation staleness, purpose-as-
  alibi, version downgrade, cryptographic agility).
- `core.md` §14 Registry considerations: IANA-style governance table for ATX
  version numbers, signature suites, DID type prefixes, purpose vocabularies,
  taskScope namespaces, capability tokens, and transparency-log entry types.
- This changelog.

### Changed

- `README.md`: status now names ATX 1.1 as the normative wire format in
  production issuance; conformance section updated from the stale 8-fixture
  description to the current 15-fixture suite with the `v1_1-*` family and the
  `jcs-vectors` gate.

## [1.0.2] - 2026-06-08

### Added

- `core.md` §1.5: optional `declaredPurpose` field for ATX 1.1 — publisher-signed
  declaration of agent objective (statement, category, taskScopes,
  capabilityJustification, autonomy, dataScopes, egressScopes), governed
  vocabularies, measured breadth, and verifier guidance (never an authorization
  input, never buys trust). (#5)
- OpenA2A specs family header linking sibling specs and specs.opena2a.org.

### Changed

- `coordination/a2a-sibling-issue-draft.md` refreshed for the post-AIM-PR-#215
  shipped state. (#3)

## [1.0.1] - 2026-06-01

### Added

- `core.md` §1.3a: ATX v1.1 JCS (RFC 8785) canonical signing form — explicit TBS
  projection, determinism rules, frozen v1.0 legacy form, cross-implementation
  byte-agreement mandate, authorization-requires-v1.1 rule, and the version
  transition/downgrade-resistance rule. (#4)

## [1.0.0] - 2026-05-23

### Added

- Initial import of the ATX architecture documents (`core.md`, `scalability.md`,
  `sovereign-federation.md`) from the aim-roadmap tree, superseding ATC
  architecture v2.0 (March 2026).
- DID method unified to `did:opena2a`; AIM identity cross-linked to AIP.
- README cross-link to the `atx-conformance` suite. (#2)
