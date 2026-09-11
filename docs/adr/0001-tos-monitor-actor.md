# ADR-0001: ToSMonitor-LLM ⊣ ToSArchiveGovernor -- a governed actor layered on this archive

- Status: Accepted (2026-07-24)
- Related: [`com-junkawasaki/root` ADR-2607110300](https://github.com/com-junkawasaki/root/blob/main/90-docs/adr/2607110300-cloud-itonami-lei-corporate-tos-catalog.edn)
  (the archive-only design this repo was created under -- unchanged by this
  ADR); [`com-junkawasaki/root` ADR-2607241900](https://github.com/com-junkawasaki/root/blob/main/90-docs/adr/2607241900-cloud-itonami-lei-tos-monitor-actor-pilot.edn)
  (the original 1-repo pilot, on `cloud-itonami-lei-2572ibtt8cczw6au4141`,
  P&G); [`com-junkawasaki/root` ADR-2607242500](https://github.com/com-junkawasaki/root/blob/main/90-docs/adr/2607242500-cloud-itonami-lei-tos-monitor-actor-batch10-round4.edn)
  (the fourth 10-repo validation batch this repo is part of).

## Context

This repository archives the publicly published Vendor Terms and
Conditions of The Williams Companies, Inc., per
ADR-2607110300 -- a read-only reference archive. As part of a fourth 10-repo
validation batch extending the `cloud-itonami-lei-2572ibtt8cczw6au4141`
pilot (see ADR-2607241900/ADR-2607242500 for the full fleet-level design
rationale), this repo gains a governed actor layer on top of the unchanged
archive.

## Decision

Identical design and code to the pilot and every other repo in this batch
(`src/tosmonitor/{governor,phase,operation,registry,advisor}.cljc` are
byte-for-byte identical across all of them) -- see ADR-2607241900 for the
full rationale of each of the six HARD governor checks, the single
always-escalate `:tos/change-proposal` actuation, and the mock-advisor-only
scope. Only `tosmonitor.store`'s company/baseline demo data is specific to
this repo:

- **Company**: The Williams Companies, Inc., LEI
  D71FAKCBLFS2O0RBPG08, website `https://www.williams.com`.
- **Baseline provenance** (real, from this repo's own `80-data/public/
  tos.journal.edn`): source-url `https://www.williams.com/vendor-terms-conditions/`,
  retrieved-at `2026-07-10T06:28:13Z`, doc-type `:vendor-terms-and-conditions`.
- **Baseline full text**: a short, hand-written representative excerpt (not
  the real, nav-menu-heavy archived page), with a self-consistent SHA-256
  computed from that excerpt itself -- matching the pilot's own convention
  (ADR-2607241900), not a claim that this is the verbatim archived text.

The archive-of-record (`80-data/public/tos.journal.edn`) is never touched;
`commit-record!` only writes to this actor's own Store.

### First `:vendor-terms-and-conditions` doc-type company

Every prior company in this actor family has an archived doc-type of
`:terms-of-service`, `:terms-of-use`, `:privacy-policy`, or
`:terms-of-carriage`. This repo's archived document is Vendor Terms and
Conditions -- procurement/supplier-facing terms rather than a
consumer/end-user-facing ToS -- reflecting that Williams's public legal page
targets vendors and contractors doing business with an energy-infrastructure
company, not site visitors generally. `:vendor-terms-and-conditions` was
already present in `tosmonitor.registry/known-doc-types`'s enumerated
vocabulary before this repo (anticipating exactly this shape), so
`doc-type-unknown-violations` treats it as a normal, recognized doc-type --
not a novel or invalid one requiring a registry change.

## Consequences

Same as the pilot (ADR-2607241900) and the batch (ADR-2607242500) --
proves the actor pattern holds for another, independently different
company/domain shape.

## Run

```bash
kbb -M:dev:run     # walk a clean lifecycle + all six HARD-hold checks + a phase-0 hold + a backend swap
kbb -M:dev:test    # governor contract · phase invariants · store parity · advisor smoke
kbb -M:lint        # clj-kondo (errors fail; CI mirrors this)
```
