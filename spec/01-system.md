# 01 — System specification

## Logical architecture

| Component | Required responsibility |
| --- | --- |
| API service | Authentication, validation, policy enforcement, transactions, lifecycle, retrieval, REST |
| Worker | Durable extraction, embedding, cleanup, reconciliation, retry |
| SQLite store | Canonical relational state, source text, FTS5, jobs, audit metadata |
| Dashboard | Owner review, search, provenance, revisions, clients, grants, deletion |
| CLI | Owner operations and local client setup |
| MCP adapter | Read-only memory tools over local stdio |
| Model provider | Structured extraction, embeddings, health; Ollama implementation in v0.1 |

SYS-001: The API service and worker MUST be the only components allowed to
write canonical data.

SYS-002: Authorization MUST live in a shared backend policy module. Adapters,
prompts, UI controls, and model output MUST NOT grant or expand authority.

SYS-003: Only the backend model-provider implementation may communicate with
Ollama. Browsers and external clients MUST NOT receive inference-server access.

SYS-004: SQLite is the v0.1 canonical store. FTS, embeddings, caches, and
exports are derived or portable representations and MUST NOT become competing
authorities.

## Deployment profiles

SYS-005: The required single-machine profile MUST run every component and
Ollama on one owner-controlled computer.

SYS-006: The optional two-machine profile MUST keep the service and database on
the development PC and use one owner-controlled inference node for Ollama.

SYS-007: The service MUST bind to loopback by default. Non-loopback service
binding is unsupported in v0.1.

SYS-008: A remote inference node MUST be reached through an authenticated SSH
tunnel to loopback, or through a documented private network plus mutually
authenticated TLS proxy. Unauthenticated LAN or internet exposure is forbidden.

## Canonical flows

SYS-009: Retrieval flow MUST be:
credential authentication → grant resolution → authorized candidate selection
→ ranking → bounded response/context assembly → audit metadata.

SYS-010: Import flow MUST be:
authentication → write policy → input validation → normalization/secret preview
→ atomic source-and-job commit → extraction → deterministic validation →
pending review → approval → indexing.

SYS-011: Deletion flow MUST immediately remove visibility, advance lifecycle
generation, cancel/invalidate work, then perform tracked physical cleanup.

## Availability and degradation

SYS-012: Inference unavailability MUST NOT prevent authentication, project and
grant management, source inspection, approved-memory CRUD, lexical search,
revision inspection, export without vectors, or deletion initiation.

SYS-013: When embeddings cannot be produced for a query, search MUST execute in
lexical-only mode, set `semantic_status` to `unavailable`, and include a
permission-safe warning.

SYS-014: Extraction and embedding jobs MAY wait while inference is unavailable.
They MUST retain attempts and backoff state durably and MUST resume after
recovery without duplicate canonical records.

SYS-015: The system MUST NOT silently route inference or embeddings to a cloud
endpoint.

## Technology constraints

SYS-016: The reference implementation MUST use Python, FastAPI, Pydantic,
SQLAlchemy, Alembic, SQLite with FTS5, a single durable SQLite-backed worker,
React, TypeScript, Vite, and the supported official Python MCP SDK.

SYS-017: Semantic ranking MUST use an authorized in-process cosine scan for
v0.1. A different vector index requires a specification change plus measured
evidence that the current approach violates a release target.

SYS-018: Dependency versions and model digests MUST be pinned in release
artifacts. Protocol/client versions MUST be recorded in the compatibility
matrix.

## Capacity envelope

SYS-019: v0.1 MUST support at least 10,000 active approved memory versions,
100,000 source chunks, 50 projects, and 25 clients in one installation while
meeting the measured gates in `12-quality.md`.

SYS-020: Default maximums MUST be configurable but bounded:

| Item | Default | Hard maximum in v0.1 |
| --- | ---: | ---: |
| Source text per import | 2 MiB UTF-8 | 10 MiB |
| Explicit memory text | 8 KiB UTF-8 | 32 KiB |
| Search query | 2 KiB UTF-8 | 8 KiB |
| Search result limit | 10 | 100 |
| Context budget | 1,000 tokens | 8,000 tokens |
| Concurrent inference jobs | 1 | 4 |

Requests beyond hard limits MUST receive `413 payload_too_large` or
`422 validation_error` as appropriate.
