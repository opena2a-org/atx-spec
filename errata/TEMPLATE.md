---
id: ATX-E-NNNN
status: proposed
class: clarification
affectsDocument: core.md
affectsWire: false
sections:
  - "§0.0"
oldText: |
  The published text, copied verbatim, with nothing normalised. This is what a
  reader of the released document sees today.
newText: |
  The text that replaces it, copied verbatim. A reader must be able to apply
  this erratum with a copy and a paste, without judgement.
fixtures: []
filed: "2026-01-01"
accepted:
---

# ATX-E-NNNN: one line saying what the published text gets wrong

<!--
Copy this file to the next free errata/ATX-E-NNNN.md, fill in the frontmatter and
the sections below, then regenerate the index:

    python3 scripts/gen_errata_index.py

Frontmatter rules, all enforced by scripts/check_errata.py:

  id               ^ATX-E-\d{4}$, equal to this file's name without .md
  status           proposed | accepted | incorporated
  class            editorial | clarification | security
  affectsDocument  the document the correction lands in, e.g. core.md
  affectsWire      true only if a conforming implementation's bytes change
  sections         list of section numbers the correction touches
  oldText          the published text, verbatim
  newText          the replacement text, verbatim
  fixtures         atx-conformance fixture file names; may be empty only while
                   the erratum is proposed, or when class is editorial
  filed            ISO date the erratum was filed
  accepted         ISO date it was accepted; empty while status is proposed

No other key is allowed, and none of these may be dropped.

Two process rules bite as soon as status leaves `proposed`: a class other than
editorial must name at least one conformance fixture that exists in the suite,
and the erratum id must appear in CHANGELOG.md. Acceptance is a document PATCH on
the MAJOR.MINOR.PATCH-{draft|rcN|final} ladder.

Nothing in this directory names an implementation or reports whether one passes.
State what the specification requires; the conformance suite reports the rest.
-->

## What the text says

Quote the published text in place and point at the ambiguity or the error. Say
which reading a conforming implementation could take today.

## Why it is wrong

Say what breaks. For a `security` class, say what an implementation that follows
the published text as written permits an attacker to do.

## What it should say

The replacement text, matching `newText` above. If the correction reaches the
wire format, say so explicitly and set `affectsWire: true`.

## How it is tested

Name the conformance fixtures that pin the corrected behaviour, and say what each
one asserts. An erratum whose class is not `editorial` is not accepted until the
fixtures exist: the corrected behaviour has to be observable, not just written
down.
