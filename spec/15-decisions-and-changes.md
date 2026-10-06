# 15 — Decisions, assumptions, and specification changes

## Binding decisions

| ID | Decision | Reason | Compatibility consequence |
| --- | --- | --- | --- |
| ADR-001 | SQLite is the v0.1 canonical store | Portable single-owner operation | No shared/network-drive database |
| ADR-002 | Generated memories require approval | Auditability and false-memory control | Pending content is never externally searchable |
| ADR-003 | Policy filtering precedes ranking | Prevent cross-project metadata leaks | Search implementations must query authorized candidates |
| ADR-004 | Source evidence uses normalized code-point offsets | Language-safe deterministic provenance | Importers must preserve normalized text/mapping |
| ADR-005 | Local stdio is the only v0.1 MCP transport | Avoid premature remote auth/bridge risk | Cloud clients and remote HTTP MCP unsupported |
| ADR-006 | Semantic search uses authorized NumPy cosine scan | Small v0.1 scale and simple deletion | New vector store requires benchmarked spec change |
| ADR-007 | Logical deletion is immediate; physical cleanup is tracked | Honest storage semantics and race safety | API exposes deletion operation state |
| ADR-008 | Export omits raw sources and vectors by default | Privacy and model portability | Imports re-embed after review |
| ADR-009 | Windows 11 x64 is the first release target | Current development environment | Other OS claims require full smoke evidence |
| ADR-010 | The root blueprint is non-canonical | Avoid two sources of truth | All implementation decisions cite `spec/` |

## Current planning assumptions

These are explicit and must be validated; they are not product claims.

| ID | Assumption | Validation |
| --- | --- | --- |
| ASM-001 | Development PC can meet the 10,000-memory latency targets | M4 capacity benchmark |
| ASM-002 | Optional ASUS inference laptop has enough RAM/GPU for candidate models | M0 hardware inventory and benchmark |
| ASM-003 | `qwen2.5:3b` can meet extraction precision/latency | M0/M2 labeled evaluation |
| ASM-004 | `embeddinggemma` provides useful English/Bangla retrieval | M4 language-slice comparison |
| ASM-005 | One inference job is acceptable for v0.1 review throughput | M0/M2 queue and latency measurement |
| ASM-006 | Five initial users experience repeated cross-assistant context loss | M0 interviews |

Failure of an assumption triggers a recorded decision; implementation MUST NOT
silently weaken a requirement.

## Decisions required before their milestone

| ID | Deadline | Decision |
| --- | --- | --- |
| DEC-001 | M0 exit | Exact hardware inventory and supported inference topology |
| DEC-002 | M0 exit | Extraction/embedding model names, digests, licenses, context |
| DEC-003 | M0 exit | First real MCP client/version |
| DEC-004 | M1 exit | Owner bootstrap recovery procedure and OS credential integration |
| DEC-005 | M3 exit | Default source retention UX |
| DEC-006 | M4 exit | Evaluated abstention/minimum-relevance rule |
| DEC-007 | M7 exit | Distribution/packaging method and final supported OS matrix |
| DEC-008 | M9 exit | Open-source license after dependency/model/name review |

DEC-009: An undecided item MUST use the conservative behavior already specified
and cannot be advertised as supported.

## Change log

| Spec version | Date | Change | Migration/API effect |
| --- | --- | --- | --- |
| 0.1.0-draft | 2026-10-06 | Converted product blueprint into complete canonical v0.1 specification set | No implementation exists; initial contract |
| 0.1.1-draft | 2026-10-06 | Added mandatory specification-first workflow and ten-sprint sequential delivery plan | Process-only change; no product/API migration |

## Change procedure

CHG-001: A proposed change must state problem, affected IDs, alternatives,
security/privacy/deletion effects, data/API migration, test changes, and rollout.

CHG-002: Breaking changes require a specification version change and explicit
migration. Security corrections may tighten behavior in-place but MUST be
documented prominently.

CHG-003: New files outside `spec/` cannot introduce requirements. Draft ideas
must be added here as a change candidate before implementation.
