# 00 — Product specification

## Product promise

LocalMind is a local-first, auditable project-memory service. It lets a
developer preserve approved project facts, decisions, procedures, preferences,
episodes, and tasks; retrieve them across supported AI clients; inspect their
evidence; correct them with history; and revoke or delete them.

The v0.1 wedge is **auditable developer memory with temporal correction,
project-scoped permissions, source provenance, and verified local operation**.

## Users

| ID | User | Primary job |
| --- | --- | --- |
| USR-01 | Solo developer using multiple assistants | Reuse approved project context without repeating it |
| USR-02 | Student or early-career developer | Recover explanations and tested resolutions |
| USR-03 | Developer with private repositories | Control which client receives which project memory |
| USR-04 | Open-source maintainer | Recover decisions, rationale, and superseded alternatives |

PRD-001: v0.1 MUST optimize for one individual owner, not teams.

PRD-002: The product MUST describe REST, MCP, and JSON export as integration
mechanisms, not as a universal memory standard.

## Required user outcomes

PRD-003: The owner MUST be able to explicitly type a memory or import UTF-8
plain text or Markdown.

PRD-004: The owner MUST be able to search by semantic meaning, exact text,
project, memory type, status, and date constraints.

PRD-005: Every externally retrievable generated memory MUST be approved and
MUST expose resolvable source provenance.

PRD-006: The owner MUST be able to approve, edit, reject, restrict, correct,
supersede, and delete memories according to the lifecycle specification.

PRD-007: The owner MUST be able to grant and revoke per-client, per-project read
access subject to sensitivity limits.

PRD-008: The owner MUST be able to export approved memories and import them into
a fresh installation without transferring credentials or vector data.

PRD-009: Source deletion MUST revoke and clean up its derived data as specified
in `10-lifecycle-portability.md`.

PRD-010: Exact-text search, source browsing, and approved-memory CRUD MUST
remain available when inference is offline.

PRD-011: The product MUST visibly distinguish current, historical,
superseded, pending, rejected, restricted, and deleted information.

PRD-012: When evidence is insufficient, retrieval MUST return an empty result
or explicit abstention; it MUST NOT manufacture an answer.

## Demonstration acceptance

PRD-013: The release demonstration MUST use synthetic data and perform this
sequence through two independently authenticated clients:

1. import a Docker-network troubleshooting source;
2. extract and approve the supported fix;
3. retrieve it from client B with source and evidence;
4. correct it and show the prior revision as historical;
5. delete it; and
6. show that neither client can retrieve it through search, direct lookup, or
   context assembly.

## v0.1 scope

Included:

- local service and SQLite persistence;
- explicit memory creation;
- text and Markdown source import;
- durable asynchronous extraction and embedding jobs;
- exact provenance spans and owner review;
- lexical and semantic retrieval with an offline lexical fallback;
- correction, versioning, revocation, deletion, and cleanup reporting;
- client credentials, project grants, sensitivity policy, and audit metadata;
- REST API, owner CLI, local stdio MCP adapter, and browser dashboard;
- portable JSONL export/import;
- tested installation on declared operating systems.

PRD-014: The v0.1 release MUST name every supported operating system and client
version in a compatibility matrix and MUST NOT imply support for untested ones.

## Explicit non-goals

PRD-015: v0.1 MUST NOT include ambient clipboard, microphone, browser-history,
terminal, or whole-workspace monitoring.

PRD-016: v0.1 MUST NOT include multi-user teams, device synchronization,
enterprise identity, organization-wide access, autonomous agents, reminders,
cloud connectors, remote HTTP MCP, a VS Code extension, or graph visualization.

PRD-017: v0.1 MUST NOT require Neo4j, Kubernetes, Redis, a hosted vector
database, a paid API key, or any cloud inference service.

PRD-018: Local storage MUST NOT be marketed as protection from a compromised
owner account, administrator account, operating system, or physical storage
forensics.

## Later boundaries

v0.2 candidates are selected conversation-export adapters, VS Code
selected-text capture, encrypted portable backups, and measured
Bangla/English improvements. v0.3 candidates are graph views, richer temporal
queries, desktop packaging, and additional clients. These are informative and
create no v0.1 implementation obligation.

## Success measures

PRD-019: Closed beta MUST include at least five developers who already use two
assistants. Each participant MUST attempt three real past-decision recovery
tasks before and with LocalMind.

PRD-020: Initial product evidence is successful when at least three of the five
participants are still using LocalMind after two weeks and qualitative results
show reduced repeated-context work without unacceptable false-memory or review
burden.
