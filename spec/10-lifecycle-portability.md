# 10 — Correction, deletion, retention, backup, and portability

## Correction and supersession

LIFE-001: Correction creates a new immutable memory version and retains the old
version as historical until the memory or its source is deleted.

LIFE-002: Correcting text requires owner review of existing evidence. If the
new text is no longer supported, the owner must attach independent source
evidence or create an owner-authored synthetic source.

LIFE-003: The new version becomes current and the old version becomes
superseded in one transaction. FTS switches versions in that transaction;
embedding of the new version is asynchronous and the old embedding becomes
ineligible immediately.

## Immediate logical deletion

LIFE-004: Accepting deletion MUST in one transaction:

1. authorize the exact target;
2. change it to `deletion_pending`;
3. increment lifecycle generation;
4. remove it from every eligible read and FTS path;
5. invalidate authorization/context caches;
6. cancel queued jobs and make running jobs stale;
7. create a deletion operation and cleanup job; and
8. append a content-free audit event.

The API returns only after this transaction commits.

LIFE-005: Running jobs MUST compare state and generation immediately before
each canonical or derived-data commit. A mismatch ends the job as `cancelled`.

## Source deletion

LIFE-006: Source cascade cleanup MUST remove:

- all source versions and normalized text;
- pending/rejected candidates from those versions;
- evidence spans and cached excerpts;
- memories supported solely by those versions;
- FTS rows, embeddings, and context caches for removed memories;
- temporary and operation artifacts containing source content; and
- derived job payloads.

LIFE-007: When a memory has independently reviewed evidence from another
source, source deletion removes the affected evidence and stages the memory for
owner review. The v0.1 implementation MAY instead delete the whole memory, but
MUST NOT retain it as active without valid evidence.

## Memory-only deletion

LIFE-008: Memory deletion removes its versions, evidence links, FTS rows,
embeddings, and caches but may retain the source. The confirmation MUST explain
that the fact can still exist in source text.

LIFE-009: Memory-only deletion MUST add a source-version suppression
fingerprint before cleanup so later re-extraction cannot silently recreate the
claim. The owner may explicitly remove a suppression after reviewing it.

## Physical completion

LIFE-010: Logical revocation is immediate. Physical cleanup is asynchronous and
reported as `queued`, `running`, `completed`, or `cleanup_failed`.

LIFE-011: A cleanup operation is complete only after database rows, FTS,
vectors, caches, job payloads, and managed temporary files are reconciled.
Failures retain no retrieval visibility and retry with durable state.

LIFE-012: SQLite free pages/WAL, SSD behavior, filesystem snapshots, and
unmanaged copies prevent a forensic-erasure guarantee. Documentation MUST
distinguish logical deletion, application cleanup, and media-level erasure.

## Retention

Allowed source retention values are `until_deleted` and `days:<1..3650>`.

LIFE-013: Expiration schedules the same deletion lifecycle as owner deletion.
The owner receives a visible pending-expiration warning at least 24 hours
before a configured timed source expires, unless retention is under 24 hours.

LIFE-014: Audit metadata is retained for 90 days by default, configurable
30–365 days, and contains no content. Idempotency records are retained at least
30 days. Suppressions live as long as their retained source version.

## Managed backups

LIFE-015: v0.1 supports consistent local backup and restore but not
application-encrypted portable backup. Backup destinations rely on owner
filesystem protection and must be labeled sensitive.

LIFE-016: Managed backup inventory records artifact ID, creation time, checksum,
schema version, and expiration. Deletion reports which managed backups may
still contain the target and when they expire.

LIFE-017: Restore MUST apply deletion records and suppressions before enabling
retrieval. Unmanaged copies and old exported bundles are outside deletion
control and MUST be disclosed as such.

## Portable export format

LIFE-018: Portable export is a directory or zip bundle containing:

- `manifest.json`;
- `projects.jsonl`;
- `memories.jsonl`;
- `memory_versions.jsonl`;
- `evidence.jsonl`; and
- optional `sources.jsonl` only when explicitly selected.

LIFE-019: The manifest includes format name, independent schema version,
creation time, LocalMind version, record counts, included data classes, source
policy, model/index metadata for information only, per-file SHA-256, and bundle
checksum.

LIFE-020: Exports MUST exclude owner/client credentials, token hashes, sessions,
system secrets, audit IP/process data, vectors, caches, jobs, and absolute file
paths. Raw sources are excluded by default.

LIFE-021: JSONL is UTF-8 with one canonical JSON object per LF-terminated line.
Dates and IDs retain their defined external form. Records have a `record_type`
and `schema_version`.

## Import

LIFE-022: Import MUST validate format version, byte and record limits, hashes,
references, enums, offsets, evidence equality, path safety, and duplicate IDs
before any canonical record is created.

LIFE-023: Imported IDs are mapped to new destination IDs. Import MUST not
overwrite a project or record. Content/source duplicates are reported in a
staging plan.

LIFE-024: All imported memories are staged for owner review, even if they were
approved in the source installation. Credentials and authorization are never
imported.

LIFE-025: Unsupported major schema versions are rejected. Minor versions may be
accepted only when unknown fields are safely ignorable under a documented
migration. Import is atomic per approved plan or rolls back completely.

LIFE-026: Importing an old bundle may reintroduce deleted content. The UI/CLI
MUST warn before staging and apply matching suppression/deletion records when
the destination has them.
