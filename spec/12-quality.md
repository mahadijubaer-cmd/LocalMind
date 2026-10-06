# 12 — Quality, evaluation, and release specification

## Test layers

QA-001: CI MUST run formatting, static analysis, type checks, unit tests,
migration tests, API contract tests, policy tests, worker/lifecycle tests,
retrieval evaluation, import/export round trips, frontend tests, MCP/CLI tests,
dependency audit, and secret scanning.

QA-002: Tests MUST be deterministic by default. Model-dependent tests use
recorded fixtures for CI; a separately labeled hardware evaluation runs real
models and records digests and timing.

QA-003: Every normative requirement MUST map to an automated test, inspection,
benchmark, or documented manual evidence in `14-traceability.md`.

## Evaluation corpus

QA-004: Before retrieval tuning, create a synthetic redistributable corpus of
at least 100 sources across at least five projects and 100 labeled queries.
Freeze a held-out query subset before tuning.

QA-005: The query set MUST include at least:

| Slice | Minimum |
| --- | ---: |
| Exact identifiers/error messages | 25 |
| Semantic paraphrases | 25 |
| Temporal/current-vs-historical | 15 |
| No-answer | 15 |
| Bangla or mixed Bangla/English | 10 |
| Ambiguous/conflicting | 10 |

One query may carry multiple labels, but the corpus must contain 100 distinct
queries. Each answerable query lists all acceptable memory IDs; each no-answer
query explicitly lists none.

QA-006: The security corpus MUST contain at least 20 separate authorization,
injection, malformed-input, deletion, race, token-leak, and offline-egress
cases. It is separate from retrieval scoring.

QA-007: Evaluation fixtures MUST contain synthetic secrets only and no personal
or proprietary content.

## Retrieval and extraction metrics

QA-008: Metrics are calculated on the held-out set and reported overall and by
slice:

- Recall@5: fraction of answerable queries with an acceptable memory in top 5;
- no-answer precision: fraction of no-answer queries that abstain;
- irrelevant-memory rate in generated context packs;
- extraction precision against manually labeled supported claims;
- evidence validity by mechanical span resolution;
- latency percentiles with failures counted and reported.

QA-009: v0.1 release gates are:

| Metric | Gate |
| --- | --- |
| Recall@5 | ≥ 85% on answerable held-out queries |
| Exact/error Recall@5 | ≥ 95% |
| No-answer precision | ≥ 90% |
| Evidence validity | 100% of publishable memories |
| Extraction precision | ≥ 90% |
| Permission isolation | Zero unauthorized records or metadata |
| Deletion/revocation | All lifecycle and race tests pass |
| Offline behavior | Zero silent cloud fallback |
| Lexical latency | p95 < 300 ms at 10,000 active memories |
| Warm semantic latency | p95 < 2 s on declared PC/inference link |
| Extraction latency | p95 < 30 s per approximately 1,000-token chunk |

QA-010: Compare lexical-only, semantic-only, and hybrid modes on the same
corpus. Hybrid MUST improve semantic Recall@5 without reducing exact/error
Recall@5 below its gate.

QA-011: Rank/abstention thresholds and RRF changes MUST be selected only using
training/tuning data, recorded in `15-decisions-and-changes.md`, and evaluated
once against the held-out set for release.

## Performance protocol

QA-012: Benchmark reports MUST record CPU, RAM, GPU/VRAM, OS, LocalMind commit,
database size, source/memory counts, language split, model name/digest,
quantization, context, embedding dimension, link type, warm/cold state,
concurrency, repetitions, percentile method, timeouts, and failures.

QA-013: Latency tests use at least 200 lexical queries and 100 warm semantic
queries after 20 warmups. Extraction uses at least 50 representative chunks.
Report p50, p95, maximum, timeout count, and error count.

QA-014: Capacity tests use the `SYS-019` envelope and include mixed projects and
sensitivity so authorization filtering is exercised.

## Required scenario suites

QA-015: Policy suite MUST verify auth filtering before ranks, counts, snippets,
facets, evidence, contexts, direct IDs, historical results, and timing-safe
error semantics.

QA-016: Lifecycle suite MUST cover stale edits, duplicate requests, crash after
claim, lease expiry, retry, delete during extraction, delete during embedding,
delete during context generation, grant revocation, cleanup retry, and restore.

QA-017: Migration suite MUST create a database at every supported prior schema,
upgrade it, validate canonical content and indexes, and exercise rollback from
a deliberately failing migration using backup restore.

QA-018: Portability suite MUST round-trip all supported record types, reject
bad hashes/references/offsets/paths/versions, remap IDs, preserve history, omit
credentials/vectors, and stage imported records.

QA-019: Installation smoke test MUST run on a clean supported OS environment:
install, initialize, import, approve, search, MCP retrieve, correct, delete,
restart, backup, restore, and uninstall binaries without deleting data.

QA-020: Accessibility validation MUST include automated checks and manual
keyboard/screen-reader checks for the six required dashboard screens.

## Release evidence

QA-021: A release candidate is blocked by any failed MUST requirement, any
security blocker in `SEC-027`, unresolved migration loss, invalid provenance,
or missing compatibility/license notice.

QA-022: Release artifacts MUST include test summary, benchmark report,
evaluation report, compatibility matrix, dependency/model licenses, migration
notes, known limitations, checksum list, and reproducible demo instructions.

QA-023: Metrics are engineering results for the measured corpus and hardware,
not universal promises. Reports MUST label targets versus measured values.
