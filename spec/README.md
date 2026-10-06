# LocalMind canonical specification

Status: **Normative**

Specification version: **0.1.1-draft**

Product target: **LocalMind v0.1**

This directory is the single source of truth for LocalMind product behavior,
interfaces, data semantics, security boundaries, operations, and release
acceptance. Requirements outside this directory are non-normative.

## Authority and precedence

1. Files in this directory are normative unless explicitly marked informative.
2. A requirement with a stable ID is authoritative over prose without an ID.
3. Machine-readable schemas, when added here, must implement the written
   contract. A disagreement is a specification defect and blocks release.
4. A later specification version supersedes an earlier version only after its
   change is recorded in `15-decisions-and-changes.md`.
5. Source code, tests, issues, README files, and generated documentation must
   link to requirement IDs; they do not redefine requirements.

The words **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are used
as normative terms. “Owner” means the one human administrator in v0.1.

## Document map

| File | Canonical subject |
| --- | --- |
| `00-product.md` | Product promise, users, scope, stories, release boundaries |
| `01-system.md` | Architecture, components, deployment, trust and failure boundaries |
| `02-domain.md` | Entities, states, invariants, revisions, temporal semantics |
| `03-data.md` | Persistent schema, indexes, transactions, migration rules |
| `04-auth-policy.md` | Authentication, clients, grants, authorization algorithm |
| `05-ingestion.md` | Capture, normalization, extraction, review, idempotency |
| `06-retrieval.md` | Lexical/semantic search, ranking, abstention, context packs |
| `07-interfaces.md` | REST contracts, errors, concurrency, pagination |
| `08-clients.md` | CLI, MCP, dashboard, compatibility claims |
| `09-security-privacy.md` | Threat model, controls, logging, network behavior |
| `10-lifecycle-portability.md` | Correction, deletion, retention, backup, import/export |
| `11-operations.md` | Configuration, jobs, observability, deployment, recovery |
| `12-quality.md` | Testing, evaluation data, metrics, release gates |
| `13-delivery.md` | Milestones, backlog, definition of done |
| `14-traceability.md` | Requirement-to-verification matrix |
| `15-decisions-and-changes.md` | Binding decisions, assumptions, open changes |
| `16-glossary.md` | Exact project vocabulary |
| `17-references.md` | Informative primary references and verification policy |
| `18-development-process.md` | Mandatory specification-first delivery workflow and gates |
| `19-sprint-plan.md` | Sequential ten-sprint implementation plan |

## Fixed v0.1 baseline

- One owner and one LocalMind installation.
- The service, worker, SQLite database, dashboard, CLI, and MCP adapter run on
  one development PC.
- Ollama MAY run on that PC or on one private inference node.
- Capture is explicit. There is no ambient monitoring.
- Generated memories require owner approval before external retrieval.
- REST, CLI, and MCP share one policy engine and one canonical store.
- Approved-memory reads and lexical search continue while inference is offline.
- Internet or cloud inference fallback is prohibited.

## Requirement lifecycle

Every normative requirement has an ID. Changes MUST:

1. update the owning specification;
2. update affected traceability rows;
3. record the reason and compatibility effect in
   `15-decisions-and-changes.md`;
4. update tests before or with implementation; and
5. increment the specification version when behavior or a public contract
   changes.

A requirement is complete only when its acceptance evidence exists and passes.
Unresolved items are listed as explicit assumptions or change candidates; they
MUST NOT be silently decided in implementation.

## Scope of “everything”

This specification covers the complete v0.1 product: supported users and use
cases, non-goals, logical architecture, data model, authorization, ingestion,
extraction, review, retrieval, context assembly, REST, MCP, CLI, dashboard,
privacy, security, deletion, retention, portability, deployment, configuration,
failure behavior, observability, tests, evaluation, release gates, and delivery.
Later-release ideas are recorded only to define compatibility boundaries.
