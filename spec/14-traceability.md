# 14 — Requirement traceability

This matrix defines the minimum verification class for every requirement
family. Individual test cases MUST cite exact requirement IDs in their names or
metadata. The implementation test plan expands each row before its milestone.

| Requirement family | Primary artifact/component | Required verification |
| --- | --- | --- |
| PRD-001–020 | End-to-end product and release docs | Acceptance scenarios, scope inspection, beta report |
| SYS-001–004 | Backend module boundaries | Architecture test/review; adapter cannot write store directly |
| SYS-005–008 | Deployment configuration | Single/two-machine smoke and bind/network tests |
| SYS-009–015 | Service flows and outage handling | Integration, fault injection, egress test |
| SYS-016–020 | Build and capacity | Dependency inspection and capacity benchmark |
| DOM-001–003 | ORM/policy schemas | Constraint and forged-identity tests |
| DOM-004–007 | Lifecycle service | State-transition property tests |
| DOM-008–012 | Versioning service | Immutability, correction, stale-write tests |
| DOM-013–015 | Temporal query layer | Boundary/current/history/conflict tests |
| DOM-016–019 | Normalizer/evidence validator | Unicode property and exact-span tests |
| DOM-020–024 | Policy/ID/job models | Sensitivity matrix, ID entropy/format, generation races |
| DAT-001–006 | SQLite/migrations | PRAGMA, constraint, transaction, log-content tests |
| DAT-007–011 | FTS/vector/reconciliation | Drift, rebuild, generation and query-plan tests |
| DAT-012–017 | Jobs/backups | Concurrency, crash, idempotency, consistent restore |
| AUTH-001–006 | Bootstrap/token middleware | Setup, hash, storage, revoke, rate-limit tests |
| AUTH-007–014 | Policy engine/query layer | Complete allow/deny matrix and leak probes |
| AUTH-015–020 | Grants/browser/security suite | Cache invalidation, CSRF/CORS and adversarial tests |
| ING-001–012 | Import API/normalizer | Format, size, secrets, idempotency, atomicity tests |
| ING-013–019 | Chunker/model provider | Boundary, context, metadata, no-tool inspection |
| ING-020–024 | Candidate validator | Schema, quote, offset, repair, suppression tests |
| ING-025–031 | Review/jobs | Visibility, atomic approval, retry/dead-letter tests |
| RET-001–008 | Search candidate generation | Filter-before-rank and mode compatibility tests |
| RET-009–016 | Rank/result builder | Golden ranking, dedupe, conflict, abstention tests |
| RET-017–023 | Context/temporal/language | Budget, injection, ambiguity and Unicode tests |
| API-001–006 | HTTP middleware/contracts | OpenAPI schema and error snapshot tests |
| API-007–012 | Endpoints/operations | Pagination, authorization, idempotency, polling tests |
| API-013–014 | Contract publication | Runtime/OpenAPI diff and compatibility review |
| CLI-001–010 | CLI/compatibility | CLI integration, exit/output snapshots, real-client test |
| MCP-001–007 | MCP adapter | Tool schema, read-only, auth/revocation, injection tests |
| UI-001–008 | Dashboard | Browser flows, revision preservation, accessibility checks |
| SEC-001–009 | Threat/network boundary | Threat-model review, bind/firewall/egress tests |
| SEC-010–019 | Secrets/application/model | Static/dynamic security tests and log scan |
| SEC-020–027 | Privacy/audit/security gate | Permission inspection, audit schema, adversarial suite |
| LIFE-001–009 | Correction/deletion service | Cascade, independent evidence, suppression, race tests |
| LIFE-010–017 | Cleanup/retention/backup | Reconciliation, expiry, inventory and restore tests |
| LIFE-018–026 | Portable format | Golden bundles, fuzzing, round trip and old-export tests |
| OPS-001–007 | Packaging/configuration | Clean-host install and config precedence tests |
| OPS-008–014 | Models/worker | Hardware benchmark, leases, shutdown, dead-letter tests |
| OPS-015–022 | Health/recovery | Endpoint, metrics/log schema, crash/disk/corruption drills |
| OPS-023–027 | Upgrade/service behavior | Documentation exercise, migration, latency/fallback tests |
| QA-001–023 | CI/evaluation/release | Pipeline evidence and release checklist inspection |
| DEL-001–007 | Issue/milestone governance | Issue template and milestone audit |
| TRACE-001–004 | Traceability process | Release requirement-coverage report |
| DEC-009, CHG-001–003 | Specification governance | Decision deadline and change-review audit |
| REF-001–006 | External reference policy | Release reference/license verification record |

TRACE-001: No code change is accepted merely because a family row exists.
Exact implemented requirement IDs MUST be linked to exact verification.

TRACE-002: A test may verify multiple IDs, but a MUST requirement without at
least one passing verification item blocks the milestone that owns it.

TRACE-003: Manual verification evidence MUST record tester, date, environment,
steps, result, and artifact link. “Works for me” is not evidence.

TRACE-004: Requirements marked non-applicable to an implementation unit remain
applicable to the release and need a rationale in the release checklist.
