# 19 — Sequential sprint plan

## Planning model

The preferred delivery model is ten gated one-week sprints. It is sequential
because provenance, policy, and lifecycle correctness are foundations for every
adapter. A sprint may take longer than a week; the next sprint starts only when
the current exit gate passes.

SPR-001: Sprint order is S0 through S9. Parallel work is allowed only inside a
sprint when work items have no unmet dependency and do not define competing
contracts.

SPR-002: Each sprint MUST follow `18-development-process.md`: specification
delta → spec review/freeze → tests/contracts → implementation → verification →
traceability/gate review.

SPR-003: Every sprint ends with a runnable vertical demonstration, a gate
result, and recorded completion evidence. A slide deck or code presence alone
does not pass a gate.

## S0 — Feasibility and baseline decisions

**Goal:** prove that the problem, hardware, models, network, and first client
are viable before building product infrastructure.

**Entry:** canonical v0.1 draft exists.

**Requirements:** PRD-001–002, PRD-013–018, SYS-005–008, OPS-008–010,
QA-004–007, REF-001–005.

**Work, in order:**

1. Create the synthetic Docker-fix demo corpus and interview script.
2. Conduct five developer interviews and record problem evidence.
3. Inventory development PC and optional inference-node hardware.
4. Verify protected single-machine and optional two-machine connectivity.
5. Benchmark candidate extraction/embedding models with exact digests.
6. Test disconnect, sleep, timeout, and reconnect behavior.
7. Spike one real MCP client and record compatibility evidence.
8. Resolve DEC-001, DEC-002, and DEC-003.

**Exit gate:** M0 evidence exists; model/license/network feasibility is
acceptable; five interviews completed; first client chosen; no required S0
decision remains open.

**Demo:** one source is sent through the selected local model, returns a
schema-shaped candidate with resolvable evidence, and survives an inference
disconnect without cloud traffic.

## S1 — Engineering skeleton and canonical persistence

**Goal:** establish reproducible tooling, contracts, configuration, migrations,
and canonical source/version storage.

**Entry:** S0 passed.

**Requirements:** SYS-001–004, SYS-016–018, DOM-001–003, DOM-008,
DOM-016–019, DOM-022–024, DAT-001–006, DAT-011–012, API-001–009,
OPS-001–007, QA-001–003, QA-017.

**Work, in order:**

1. Create pinned Python/backend and TypeScript/dashboard workspaces.
2. Configure formatting, typing, test, security, and CI commands.
3. Implement validated configuration and restricted runtime directories.
4. Create SQLAlchemy models and first Alembic migration.
5. Implement opaque IDs, timestamps, normalization, evidence span primitives.
6. Add common API envelopes, errors, request IDs, health endpoints, and
   generated OpenAPI baseline.
7. Add empty-database, upgrade, transaction, and integrity tests.

**Exit gate:** fresh install schema and upgrade tests pass; configuration fails
closed; normalized immutable source versions persist; OpenAPI/common errors
match the spec.

**Demo:** initialize a clean database, create a project and immutable source
version through an authenticated development harness, restart, and read the
same normalized content and metadata.

## S2 — Identity, projects, grants, and source acceptance

**Goal:** make explicit capture safe, idempotent, and project-isolated before
any extraction exists.

**Entry:** S1 passed; resolve DEC-004 during spec phase.

**Requirements:** AUTH-001–020, ING-001–012, PRD-003, PRD-007,
API project/source/client/grant endpoints, SEC-003–016.

**Work, in order:**

1. Specify and implement localhost owner bootstrap and recovery decision.
2. Implement client token issuance, hashing, lookup, revocation, and rate limit.
3. Implement projects, grants, sensitivity limits, and the policy engine.
4. Make authorized query scoping reusable before results/counts/snippets.
5. Implement text/Markdown validation, normalization, size limits, and secret
   preview/handling.
6. Implement idempotent atomic source acceptance and ingestion operation.
7. Add adversarial cross-project, ID-enumeration, revoked-token, duplicate-key,
   and secret/log-leak tests.

**Exit gate:** authenticated source import works; same request is idempotent;
changed reuse conflicts; forged IDs cannot expand access; zero unauthorized
records or metadata appear in the S2 suite.

**Demo:** two clients with different grants attempt the same source and project
operations; only the authorized client succeeds, and revocation is immediate.

## S3 — Durable extraction and evidence validation

**Goal:** turn accepted sources into safe pending candidates through durable,
restartable jobs.

**Entry:** S2 passed.

**Requirements:** DAT-012–015, ING-013–024, ING-030–031, SYS-010,
SYS-014–015, SEC-017–019, OPS-011–014.

**Work, in order:**

1. Implement job claim, lease, retry, backoff, cancellation, and dead-letter.
2. Implement offset-preserving structural/token chunking.
3. Implement the provider interface and pinned Ollama provider.
4. Implement constrained extraction prompt/schema and metadata capture.
5. Implement deterministic schema, kind, date, secret, quote, and offset
   validation plus one allowed schema repair.
6. Implement duplicate/conflict flags and suppression checks.
7. Test crash, expired lease, repeated completion, malformed output, prompt
   injection, delete-during-extraction, and offline recovery.

**Exit gate:** restart resumes work without duplicates; unsupported quotes are
rejected; pending candidates are durable; stale/deleted generations cannot
commit; no cloud fallback occurs.

**Demo:** import a malicious-looking source, extract only its supported claim,
show exact evidence, restart the worker during processing, and complete once.

## S4 — Review, memory versions, and owner dashboard

**Goal:** let the owner inspect evidence and safely publish, correct, reject, or
restrict memories.

**Entry:** S3 passed; resolve DEC-005 during spec phase.

**Requirements:** DOM-004–015, DOM-020–021, ING-025–029, PRD-005–006,
UI-001–008, LIFE-001–003, relevant memory/review API endpoints.

**Work, in order:**

1. Implement memory aggregate/version state machines and optimistic revision.
2. Implement pending review queries and decisions.
3. Atomically activate memory plus FTS row and queue embedding work.
4. Implement correction, supersession, conflict visibility, and audit metadata.
5. Build overview, review, memory detail, revision, and evidence panels.
6. Add keyboard/focus/label tests and stale-edit preservation.

**Exit gate:** approve/edit/reject/restrict flows pass; generated content is
invisible before approval; old revisions cannot overwrite new ones; every
active generated memory resolves exact evidence.

**Demo:** review one candidate beside its evidence, edit and approve it, attempt
a stale correction, then show current and historical versions.

## S5 — Authorized retrieval and context assembly

**Goal:** deliver evidence-linked lexical and semantic recall without allowing
ranking or metadata leaks.

**Entry:** S4 passed; evaluation corpus frozen; resolve DEC-006 from tuning data.

**Requirements:** DAT-007–010, RET-001–023, PRD-004, PRD-010–012,
SYS-012–013, search/context API endpoints, QA-008–014.

**Work, in order:**

1. Implement safe FTS indexing/querying and exact-match behavior.
2. Implement embedding jobs, generations, authorized vector loading, and
   in-process cosine ranking.
3. Implement RRF, deterministic ties, deduplication, conflict preservation.
4. Implement evaluated abstention and temporal/current/historical filtering.
5. Implement result provenance and bounded injection-safe context packs.
6. Implement semantic timeout and visible lexical-only degradation.
7. Benchmark lexical, semantic, and hybrid modes on the frozen corpus.

**Exit gate:** Recall/exact/no-answer gates pass or an explicit product decision
replans the sprint; unauthorized data has zero influence; lexical latency meets
target; inference outage degrades correctly.

**Demo:** exact error, paraphrase, temporal, no-answer, mixed-language, conflict,
and unauthorized queries with sources and ranking metadata.

## S6 — CLI, MCP, and cross-client proof

**Goal:** expose the verified backend through owner CLI and one real read-only
MCP integration.

**Entry:** S5 passed.

**Requirements:** CLI-001–010, MCP-001–007, PRD-013, SYS-002–003,
compatibility portions of QA-019.

**Work, in order:**

1. Implement owner CLI output, exit codes, token handling, and confirmations.
2. Implement search/review/correct/client/grant operational commands.
3. Implement thin stdio MCP tools against the same authenticated backend.
4. Validate tool schemas, inert output labeling, empty results, and revocation.
5. Test and publish the selected client compatibility matrix entry.
6. Run the cross-client Docker-fix demonstration through correction.

**Exit gate:** two independently authenticated clients retrieve the same
approved evidence; MCP is read-only; revocation works on the next request;
CLI/MCP cannot bypass policy.

**Demo:** save/review with owner CLI, retrieve from the selected MCP client,
inspect the same source through the dashboard, then revoke access.

## S7 — Deletion, retention, reconciliation, and portability

**Goal:** complete the trust promise before beta by making revocation, cleanup,
backup, export, and import testable.

**Entry:** S6 passed.

**Requirements:** LIFE-004–026, DAT-008, DAT-016–017, PRD-008–009,
SYS-011, deletion/export/import API endpoints, QA-016–018.

**Work, in order:**

1. Implement immediate logical deletion and lifecycle generations.
2. Implement source cascade, memory-only suppression, cleanup, and retries.
3. Implement reconciliation detection and explicit repair.
4. Implement timed retention warnings and expiration.
5. Implement consistent managed backup inventory and verified restore.
6. Implement portable bundle export, hardened ZIP import, validation, ID
   remap, deduplication plan, and staged review.
7. Exercise worker/cache/FTS/vector/restore deletion races.

**Exit gate:** deleted content is absent from every retrieval path immediately;
all cleanup/race tests pass; backup/restore is consistent; portable round trip
preserves allowed history without credentials or vectors.

**Demo:** delete the Docker-fix source while a derived job runs, prove empty
results in both clients, show cleanup state, then round-trip unrelated data.

## S8 — Operational and security hardening

**Goal:** make the complete product installable, diagnosable, recoverable, and
safe on the first declared platform.

**Entry:** S7 passed.

**Requirements:** SEC-001–027, OPS-015–027, PRD-014, QA-015,
QA-019–020, SYS-019–020.

**Work, in order:**

1. Complete headers, browser session/CSRF/CORS controls, logging redaction, CSP,
   dependency/secret scanning, and outbound-network denial tests.
2. Implement health, local metrics, structured log rotation, doctor, and
   owner-visible failure states.
3. Test crash, disk full, corruption fail-closed, graceful shutdown, migration
   failure, backup recovery, and newer-schema refusal.
4. Package clean install/upgrade/uninstall on Windows 11 x64.
5. Run accessibility checks and capacity/service-level benchmarks.
6. Complete security suite, license review, and operational documentation.

**Exit gate:** security suite has zero blockers; clean install and recovery pass;
capacity/latency is reported; required accessibility checks pass; supported
platform claim is evidenced.

**Demo:** clean-machine install through import/search, forced outage and
recovery, upgrade from previous schema, and safe binary uninstall retaining
data.

## S9 — Closed beta and v0.1 release

**Goal:** validate actual usefulness, fix release blockers, and publish a
reproducible evidence-backed v0.1.

**Entry:** S8 passed; resolve DEC-007 and DEC-008.

**Requirements:** PRD-019–020, QA-021–023, DEL-001–007, TRACE-001–004,
REF-006, all remaining v0.1 requirements.

**Work, in order:**

1. Onboard five consented developers who use two assistants.
2. Run three baseline and LocalMind recall tasks per participant.
3. Measure recall usefulness, unwanted memories, review effort, and retention.
4. Triage using `DEV-023`; route behavioral fixes through spec first.
5. Run full CI, security, migration, portability, performance, installation,
   compatibility, and demonstration suites.
6. Produce release notes, checksums, licenses, known limits, benchmark and
   evaluation reports, compatibility matrix, and traceability coverage.
7. Tag only after the release gate review passes.

**Exit gate:** all MUST requirements are verified; no security, deletion,
migration, provenance, or compatibility blocker remains; release evidence is
complete; the synthetic cross-client demo is repeatable.

**Demo:** the complete `PRD-013` sequence on a clean supported installation.

## Scope and replanning

SPR-004: When capacity is insufficient, remove optional work or extend the
sprint. Do not split an invariant, weaken a gate, skip tests, or start a
dependent sprint early.

SPR-005: Defects discovered in a later sprint return to the earliest owning
requirement and test. The plan is updated only after the spec records any
behavioral change.

SPR-006: Sprints S0–S9 are v0.1 scope. LM-13, LM-14, and every v0.2/v0.3 idea
remain outside this plan.
