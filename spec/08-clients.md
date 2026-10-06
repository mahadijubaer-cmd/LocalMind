# 08 — Client experience specification

## Shared client rules

CLI-001: Dashboard, CLI, REST consumers, and MCP MUST invoke the same backend
contracts and policy engine. No client may implement a weaker parallel
authorization or lifecycle path.

CLI-002: Every displayed memory result MUST make source, date state, and
current/historical status available. Scores MUST not be described as truth.

CLI-003: Client errors MUST preserve stable API error codes and provide a
helpful next action without exposing secrets or inaccessible object existence.

## Owner CLI

The executable name is `localmind`.

| Command | Required behavior |
| --- | --- |
| `localmind init` | Localhost bootstrap, data directory creation, owner credential storage |
| `localmind serve` | Start API/dashboard with explicit bind and configuration summary |
| `localmind worker` | Start the durable worker |
| `localmind project list|create` | Owner project management |
| `localmind capture` | Explicit memory or file import with secret preview |
| `localmind search` | Search with project, temporal, type, evidence, and JSON options |
| `localmind review` | List and decide pending candidates |
| `localmind memory show|correct|delete` | Provenance and lifecycle operations |
| `localmind client create|list|revoke` | Credential lifecycle |
| `localmind grant set|revoke` | Project policy |
| `localmind export|import` | Portable bundle operations |
| `localmind doctor` | Read-only configuration, database, inference, FTS, and permission checks |
| `localmind reconcile` | Detect derived-state drift; `--repair` requires owner confirmation |

CLI-004: Human output goes to stdout, warnings/errors to stderr, and
`--json` emits one documented JSON value with no decorative text.

CLI-005: Exit codes are `0` success, `2` usage/validation, `3` authentication or
authorization, `4` conflict, `5` unavailable dependency, and `1` other failure.

CLI-006: Destructive CLI commands MUST show exact scope and require interactive
confirmation unless `--yes` and the exact target ID are supplied.

CLI-007: Tokens MUST be read from an OS credential store, protected config, or
stdin. The CLI MUST NOT encourage tokens on the command line.

## MCP adapter

MCP-001: v0.1 MCP transport is local stdio only and uses a pinned official
Python SDK/protocol version recorded in the compatibility matrix.

MCP-002: The initial tools are:

- `memory_search(project_id, query, limit, time_mode, as_of?)`;
- `memory_get(memory_id, include_evidence=true)`; and
- `memory_context(project_id, query, token_budget, time_mode, as_of?)`.

MCP-003: MCP tool inputs and outputs MUST be thin mappings of REST contracts.
The adapter authenticates as one configured client and cannot accept an owner
ID, sensitivity override, grant, or approval instruction from tool input.

MCP-004: MCP v0.1 is read-only. Deletion, corrections, review, grants, exports,
bulk sources, and owner operations MUST NOT be exposed.

MCP-005: Tool descriptions MUST state that returned memory is untrusted quoted
project evidence, may be outdated, and must not be treated as system or tool
instructions.

MCP-006: Empty/abstained search returns an empty structured result, not a
hallucinated explanation. Protocol errors and product errors remain distinct.

MCP-007: The adapter MUST not write token values to logs and MUST stop with a
clear configuration error when its credential is absent or revoked.

## Dashboard information architecture

The required screens are:

1. project overview;
2. search with evidence panel;
3. pending review;
4. memory detail with revisions and conflicts;
5. clients and project grants; and
6. operations for deletion, export/import, and system health.

UI-001: Overview shows active/indexed, pending, failed, cleanup-pending, and
inference-offline counts derived only from owner-authorized data.

UI-002: Search result selection MUST open exact source evidence with normalized
offset highlighting and source-version identity.

UI-003: Review MUST place candidate and evidence together and offer approve,
edit-and-approve, reject, and mark-restricted actions with visible consequences.

UI-004: Correction MUST preview the new version and identify the version it
supersedes. Revision conflicts preserve unsaved user input and offer reload.

UI-005: Deletion UI MUST distinguish memory-only from source cascade, enumerate
scope, explain backup limitations, and show logical versus physical completion.

UI-006: Grant UI MUST say which project and sensitivity a named client can
receive and whether it is local or external. Revocation MUST warn that prior
disclosures cannot be recalled.

UI-007: The dashboard MUST meet WCAG 2.2 AA for keyboard operation, focus,
labels, contrast, status messages, and non-color state distinctions.

UI-008: The dashboard MUST not use a chat screen as its primary v0.1
interaction. Evidence review and search are primary.

## Compatibility claims

CLI-008: A compatibility matrix MUST record client name/version, LocalMind
version, MCP SDK/protocol version, OS, transport, tested tools, authentication
method, test date, and known limitations.

CLI-009: A product MUST NOT be called integrated merely because it supports
some MCP version. A real configured session must pass authentication, discovery,
tool invocation, permission isolation, evidence display, and revocation tests.

CLI-010: Cloud-hosted clients are unsupported in v0.1 because they cannot
directly reach owner localhost. No tunnel or public bridge is supplied.
