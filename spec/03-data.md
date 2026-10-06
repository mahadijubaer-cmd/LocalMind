# 03 — Data and persistence specification

## Database rules

DAT-001: SQLite MUST run with foreign keys enabled, WAL mode, a configured busy
timeout, and integrity checks documented for backup and recovery.

DAT-002: The database file MUST reside on a local filesystem. Network shares,
sync folders, and concurrent multi-host access are unsupported.

DAT-003: Schema changes MUST use ordered Alembic migrations. Every release MUST
test upgrade from the previous supported release and creation from an empty
database.

DAT-004: Canonical mutations and their audit metadata MUST commit atomically.
Source creation and its first ingestion job MUST commit in one transaction.
Memory activation and its FTS row MUST commit in one transaction.

## Canonical tables

All IDs are opaque text IDs; all timestamps are UTC; all mutable aggregates
carry `revision` and `lifecycle_generation`.

| Table | Required columns and constraints |
| --- | --- |
| `owners` | `id` PK, `created_at`, `settings_json`; exactly one active row in v0.1 |
| `projects` | `id` PK, `owner_id` FK, `name`, `slug`, `created_at`, `archived_at`; unique `(owner_id, slug)` |
| `clients` | `id` PK, `owner_id` FK, `name`, `class`, `token_hash`, `token_hint`, `created_at`, `last_used_at`, `revoked_at`, `revision`; class in `local, external` |
| `grants` | `id` PK, `client_id` FK, `project_id` FK, `can_read`, `can_write`, `sensitivity_limit`, `created_at`, `revoked_at`, `revision`; one live grant per client/project |
| `sources` | `id` PK, `owner_id` FK, `project_id` FK, `kind`, `title`, `retention`, `state`, `lifecycle_generation`, `created_at`, `deleted_at`, `revision` |
| `source_versions` | `id` PK, `source_id` FK, `version_no`, `content_sha256`, `normalized_text`, `metadata_json`, `created_at`; unique source/version and immutable |
| `source_imports` | `id` PK, `source_id` FK, `caller_client_id` FK nullable for owner, `idempotency_key`, `request_sha256`, `created_at`; unique caller/key |
| `memories` | `id` PK, `owner_id` FK, `project_id` FK, `kind`, `state`, `sensitivity`, `current_version_id`, `revision`, `lifecycle_generation`, timestamps |
| `memory_versions` | `id` PK, `memory_id` FK, `version_no`, `text`, temporal fields, `source_version_id` FK, `extraction_json`, `created_by`, `created_at`, `superseded_at`; immutable |
| `evidence_spans` | `id` PK, `memory_version_id` FK, `source_version_id` FK, `start_offset`, `end_offset`, `quote`; bounds and exact-substring validation |
| `suppressions` | `id` PK, `source_version_id` FK, `fingerprint`, `reason`, `created_at`; prevents silent recreation after memory-only deletion |
| `embeddings` | `memory_version_id` FK, `model_digest`, `generation`, `dimension`, `vector_blob`, `created_at`; composite PK; vector finite and dimension exact |
| `jobs` | `id` PK, `owner_id` FK, `kind`, `input_type`, `input_id`, `captured_generation`, `state`, `attempts`, `available_at`, `lease_owner`, `lease_until`, `last_error_code`, timestamps |
| `audit_events` | `id` PK, `owner_id`, `actor_type`, `actor_id`, `action`, `target_type`, `target_id`, `outcome`, `request_id`, `created_at`; no content |
| `deletion_operations` | `id` PK, target fields, state, generation, `requested_at`, `completed_at`, `last_error_code` |
| `exports` / `imports` | operation identity, state, manifest metadata, artifact path, error code, timestamps; artifact path never caller-controlled |

DAT-005: Memory text is canonical only in `memory_versions`. The `memories`
table points to the current version but MUST NOT duplicate mutable text.

DAT-006: Source text MUST NOT appear in audit events, job errors, request logs,
or token hints.

## Derived structures

DAT-007: `memory_fts` MUST index only active, approved current versions. It MUST
contain the version ID, normalized searchable text, and permission-filterable
owner/project identifiers.

DAT-008: FTS rows, embedding rows, and cached context are rebuildable. A
reconciliation command MUST detect missing, stale, incompatible-generation, or
orphaned derived rows without changing canonical records unless invoked with an
explicit repair flag.

DAT-009: Embedding vectors MUST be stored in a deterministic binary format with
recorded dtype, dimension, normalized flag, model digest, and index generation.
Non-finite values MUST be rejected.

DAT-010: Vectors of different model digests, dimensions, or index generations
MUST NOT be compared.

## Indexes

DAT-011: At minimum, indexes MUST support:

- live grants by client and project;
- sources and memories by owner/project/state;
- memory versions by memory/version number;
- validity filtering;
- evidence by memory version and source version;
- jobs by state and `available_at`, and expired leases;
- audit events by timestamp and target;
- deletion operations by state;
- imports by caller/idempotency key; and
- embeddings by generation and memory version.

## Concurrency and jobs

DAT-012: API write transactions MUST use optimistic revisions. Worker commits
MUST additionally validate target state and lifecycle generation.

DAT-013: A worker claims a job atomically by setting `running`, a unique lease
owner, and `lease_until`. An expired lease MAY be reclaimed.

DAT-014: Job completion MUST be idempotent. A repeated completion attempt MUST
not create a second source, candidate, memory version, embedding, or cleanup
operation.

DAT-015: Terminal job states are `succeeded`, `cancelled`, and `dead_letter`.
Nonterminal states are `queued`, `running`, and `retry_wait`.

## Backup consistency

DAT-016: Database backup MUST use the SQLite online backup API or a documented
equivalent consistent snapshot. Copying a live database file alone is
forbidden.

DAT-017: Backup metadata MUST include schema version, application version,
creation time, checksums, and included retention boundary. Restore MUST run
integrity, migration, suppression, and deletion-record checks before retrieval
is enabled.
