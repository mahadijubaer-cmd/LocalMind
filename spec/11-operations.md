# 11 — Operations and deployment specification

## Supported topology

OPS-001: A release MUST supply a reproducible local installation for each OS
listed in its compatibility matrix. The first supported target is Windows 11
x64; other systems are unsupported until their install and smoke tests pass.

OPS-002: Service, worker, and dashboard MUST be startable independently for
development and through one documented owner command for normal use.

OPS-003: Production data, configuration, logs, and temporary artifacts MUST use
separate owner-restricted directories. Repository source directories are not a
default runtime data location.

## Configuration

Configuration precedence, highest first, is CLI flag → environment variable →
configuration file → documented default.

OPS-004: Required configuration includes data directory, API bind/port,
database path, artifact/temp paths, log level, model endpoint, extraction model,
embedding model, context/chunk limits, worker concurrency, retry policy, and
retention settings.

OPS-005: Configuration MUST validate before any listener or worker starts.
Unknown configuration keys and insecure non-loopback binding fail startup.

OPS-006: Secrets MUST not be accepted in the ordinary configuration file when
an OS credential store is available. Diagnostic config output redacts secrets.

OPS-007: Defaults are API `127.0.0.1:8765`, worker concurrency 1, telemetry off,
request-body logging off, log level `INFO`, and local Ollama endpoint
`http://127.0.0.1:11434`.

## Model policy

OPS-008: Candidate initial models are `qwen2.5:3b` for extraction and
`embeddinggemma` for embeddings, but release defaults require benchmark and
license approval. Exact names and immutable digests MUST be recorded.

OPS-009: Extraction context starts at 4,096 tokens. The model runner MUST record
configured context, quantization when available, GPU/RAM use sampled by the
benchmark, and warm/cold latency.

OPS-010: A model digest, preprocessing, or dimension change creates a new
embedding generation. The old generation remains readable for lexical search
but is not mixed into semantic results.

## Worker operations

OPS-011: One worker is the supported default. Multiple local workers MAY run
only after lease-contention and idempotency tests pass.

OPS-012: Worker shutdown MUST stop claiming jobs, allow the current atomic step
to finish within a configured grace period, then release/expire its lease
without corrupting state.

OPS-013: Job payloads contain references and bounded structured metadata, not
unnecessary copies of source text.

OPS-014: `dead_letter` jobs require an owner-visible error code, attempt count,
last time, and safe retry action. Raw model responses and source content are
not shown in logs.

## Health and observability

OPS-015: Liveness checks process responsiveness only. Readiness checks database
access, migrations, FTS availability, data-directory writability, and worker
status. Inference health is reported separately and does not make read-only
service unready.

OPS-016: Metrics are local and disabled from remote export by default. Required
aggregates are request count/latency by route and status, job depth/age by kind,
job outcomes, inference availability/latency, search mode/latency, cleanup
backlog, and database size. IDs and user text are forbidden labels.

OPS-017: Structured logs include timestamp, severity, component, event code,
request ID, and safe opaque target ID when needed. Rotation defaults to ten
10-MiB files. Debug logging still MUST obey `SEC-012`.

OPS-018: Owner dashboard and `localmind doctor` MUST surface database schema,
application version, FTS state, worker heartbeat, inference state, model
digests, queue age, cleanup failures, disk free space warning, and backup age.

## Recovery

OPS-019: Unexpected service restart MUST preserve accepted transactions.
Unexpected worker restart MUST reclaim expired jobs and remain idempotent.

OPS-020: Database corruption response is fail closed: stop mutations, disable
retrieval if integrity is uncertain, preserve the file, report a safe error,
and direct the owner to verified restore/recovery steps.

OPS-021: Disk-full behavior MUST roll back the active transaction, preserve
previous canonical data, stop new imports/jobs, keep safe reads where SQLite
integrity permits, and alert the owner.

OPS-022: Startup MUST refuse a database with a newer unsupported schema. Older
supported schemas require explicit backup followed by migration.

## Installation and upgrade

OPS-023: Installation documentation MUST include prerequisites, ports, data
locations, credential handling, model download sizes, single/two-machine setup,
firewall/tunnel instructions, startup, shutdown, backup, restore, upgrade,
uninstall, and complete data removal.

OPS-024: Upgrade procedure is stop worker → consistent backup → migrate →
start service/worker → readiness and smoke tests. A migration failure must
leave a restorable backup and must not continue partially.

OPS-025: Uninstall MUST distinguish removing binaries from deleting data. Data
deletion requires a separate explicit confirmation and states backup/snapshot
limits.

## Service-level targets

OPS-026: On supported hardware, clean local startup SHOULD be ready within
10 seconds excluding initial model download. Graceful shutdown SHOULD complete
within 30 seconds when no model call is hung.

OPS-027: No request may wait synchronously for extraction. Search may wait for
one query embedding up to the configured semantic timeout (default 3 seconds),
then degrade to lexical with visible status.
