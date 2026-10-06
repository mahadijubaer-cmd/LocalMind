# 04 — Authentication and authorization specification

## Actors and credentials

Actors are `owner`, `client`, and `worker`. The dashboard and owner CLI act as
the owner. MCP integrations and API consumers act as clients. The worker uses
an internal service identity and cannot bypass lifecycle checks.

AUTH-001: Initial setup MUST create one owner credential through a
localhost-only bootstrap flow. Bootstrap MUST become permanently unavailable
after successful owner creation unless a documented offline recovery procedure
is performed.

AUTH-002: Client tokens MUST contain at least 256 bits of cryptographically
secure entropy, be shown exactly once, and be stored server-side only as a
memory-hard password hash with per-token salt and a versioned hashing policy.

AUTH-003: A displayed token MUST have the form `lm_<public_client_id>_<secret>`.
The public portion is a lookup hint, not authorization. Comparisons of secret
verifiers MUST be constant-time.

AUTH-004: Raw tokens MUST be stored by clients in an OS credential store or a
user-restricted configuration file. They MUST NOT appear in URLs, logs, audit
events, process arguments, exports, or browser local storage.

AUTH-005: Token revocation MUST take effect on the next request without a cache
delay. Revoked tokens receive `401 invalid_credentials`.

AUTH-006: Failed authentication MUST use a bounded rate limiter and must not
reveal whether a client ID exists.

## Actions

The policy engine recognizes:

`project.read`, `project.write`, `source.read`, `source.create`,
`memory.read`, `memory.create`, `memory.propose`, `memory.correct`,
`review.manage`, `client.manage`, `grant.manage`, `delete.manage`,
`export.manage`, `import.manage`, and `system.manage`.

AUTH-007: The owner is allowed every action within its installation. This power
is implemented as an explicit owner policy rule, not by skipping policy checks.

AUTH-008: A client is denied unless it has a live grant for the exact target
project and required action. v0.1 client grants MAY allow read and MAY allow
proposal/write, but never review, deletion, client/grant management, export,
import, or system management.

AUTH-009: External clients MUST be read-only in v0.1. Local clients MAY receive
`memory.propose` or source-create permission, but proposed content always enters
pending review.

## Sensitivity policy

Grant limits are `shareable_only` or `through_private`.

| Actor/class | Shareable | Private | Restricted |
| --- | --- | --- | --- |
| Owner | allow | allow | allow |
| Local client, `shareable_only` | allow | deny | deny |
| Local client, `through_private` | allow | allow | deny |
| External client, `shareable_only` | allow | deny | deny |
| External client, `through_private` | allow | allow | deny |

AUTH-010: No client grant can authorize restricted content. Restricted content
is owner-only in v0.1.

## Mandatory decision procedure

AUTH-011: Every object read MUST execute the following order:

1. authenticate and resolve actor/owner;
2. reject a revoked credential;
3. resolve live grant for the requested project and action;
4. constrain the database query by owner, project, allowed lifecycle state,
   sensitivity, temporal mode, and deletion generation;
5. rank only the constrained candidate set;
6. generate counts, snippets, facets, evidence, and context only from that set;
7. apply response size limits; and
8. record content-free audit metadata.

AUTH-012: Post-ranking filtering is forbidden. Unauthorized rows MUST NOT
affect total counts, rank positions, timing-sensitive existence messages,
snippets, suggestions, context budgets, or semantic candidate selection.

AUTH-013: Object lookup by inaccessible ID MUST return `404 not_found`, not
`403`, to avoid confirming existence. A known project action that is forbidden
may return `403 forbidden` only when the caller is already authorized to know
that project exists.

AUTH-014: Request-supplied `owner_id`, actor, class, grants, sensitivity limit,
or approval identity MUST be ignored or rejected; these values come from
authenticated server state.

## Grant changes

AUTH-015: Creating, changing, or revoking a grant requires the owner,
`expected_revision`, and an audit event.

AUTH-016: Grant revocation MUST invalidate authorization caches before the
transaction is acknowledged.

AUTH-017: Revocation prevents future disclosure but cannot retract information
already delivered. Every UI and CLI revocation confirmation MUST state this.

## Browser policy

AUTH-018: Dashboard sessions MUST use an HttpOnly, Secure when TLS is used,
SameSite=Strict session cookie with idle and absolute expiration. If cookie
authentication is implemented, all state-changing requests MUST use CSRF
protection.

AUTH-019: CORS MUST be disabled unless explicitly configured with exact
loopback origins. Wildcard origin, headers, or credentials are forbidden.

## Verification obligations

AUTH-020: Automated security tests MUST cover forged owner/project IDs,
cross-project search, direct ID lookup, counts, snippets, semantic ranking,
historical mode, revoked credentials, sensitivity boundaries, job results, and
context packs.
