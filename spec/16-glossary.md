# 16 — Glossary

**Active memory** — The approved current memory version eligible for retrieval
subject to policy and time filters.

**Actor** — Authenticated owner, client, or internal worker identity performing
an operation.

**Approval** — Explicit owner decision that makes a supported pending memory
active.

**Canonical data** — Owner/project/source/memory/version/policy/lifecycle state
stored in relational tables. Derived indexes and exports are not canonical.

**Candidate** — A validated but not owner-approved extracted memory.

**Client** — A named credentialed integration. It is classified local or
external and receives explicit project grants.

**Cleanup** — Asynchronous removal and reconciliation after immediate logical
revocation.

**Context pack** — A token-bounded, source-labeled set of authorized memory
items for use by a client.

**Current** — Active and valid at the query time; not merely the newest
creation timestamp.

**Deletion** — Immediate nonretrievability followed by tracked physical cleanup.
It is not a forensic erasure claim.

**Derived data** — Rebuildable FTS rows, embeddings, caches, snippets, and job
artifacts created from canonical data.

**Evidence span** — A zero-based half-open code-point interval in one immutable
normalized source version whose substring exactly supports a memory.

**External client** — A client whose provider/process may receive data beyond
the owner-controlled machine. It is read-only in v0.1.

**Grant** — Owner-created authorization connecting one client, one project,
allowed actions, and a sensitivity limit.

**Historical** — A superseded, still-retained version intentionally requested
through historical mode.

**Idempotency key** — Caller-chosen request identifier that makes safe retries
return the original operation and rejects different request content.

**Inference node** — Owner-controlled machine running Ollama; not a source of
authorization or canonical data.

**Lifecycle generation** — Monotonic counter invalidating work captured before
a deletion or equivalent lifecycle transition.

**Local client** — A client running in the owner-controlled local environment.
It still requires explicit grants.

**Memory** — Stable aggregate for one small attributable claim with immutable
versions and lifecycle state.

**Memory version** — Immutable text, temporal fields, provenance, and metadata
for one revision of a memory.

**Normalized source** — Strict UTF-8 source after LF line-ending and NFC Unicode
normalization; evidence offsets address this text.

**Owner** — The sole v0.1 human administrator and authority for review, grants,
deletion, export, import, and system management.

**Project** — Mandatory authorization and organization boundary for sources,
memories, and grants.

**Provenance** — Source version, exact evidence span, extraction metadata, and
review record explaining where a memory came from.

**Restricted** — Owner-only sensitivity. No client can receive it in v0.1.

**Revision** — Monotonic aggregate concurrency number used for optimistic
mutation checks; distinct from lifecycle generation and version number.

**Source** — Imported or synthetic origin aggregate belonging to one project.

**Source version** — Immutable normalized content and metadata for one version
of a source.

**Suppression** — Persistent fingerprint preventing a deleted memory from being
silently recreated from a retained source.

**Version number** — Monotonic number of immutable source or memory versions.
It does not authorize or establish current validity.
