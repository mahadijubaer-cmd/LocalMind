# 07 — REST interface specification

Base path: `/v1`  
Media type: `application/json; charset=utf-8`  
Authentication: `Authorization: Bearer <token>`  
Request correlation: client MAY send `X-Request-ID`; server always returns one.

## Common rules

API-001: Unknown JSON properties MUST be rejected on mutation requests.
Responses MAY add fields in a backward-compatible minor specification release.

API-002: IDs are opaque strings. Integers are JSON integers, not numeric
strings. Timestamps follow `DOM-023`. Request and response bodies are UTF-8.

API-003: Mutating create/action requests require `Idempotency-Key`. Updating or
deleting an existing aggregate additionally requires `expected_revision` in
the body or `If-Match: "<revision>"`.

API-004: A successful resource response has:

```json
{"data": {}, "meta": {"request_id": "req_...", "api_version": "v1"}}
```

API-005: An error response has:

```json
{
  "error": {
    "code": "revision_conflict",
    "message": "The resource changed after it was read.",
    "request_id": "req_...",
    "details": {}
  }
}
```

Messages MUST be safe for the caller. Stack traces, SQL, paths, credentials,
source text, and inaccessible identifiers are forbidden.

## Status and error mapping

| Status | Code | Meaning |
| ---: | --- | --- |
| 400 | `invalid_request` | Malformed HTTP or JSON |
| 401 | `invalid_credentials` | Missing, invalid, expired, or revoked token |
| 403 | `forbidden` | Known scope but action denied |
| 404 | `not_found` | Absent or inaccessible object |
| 409 | `revision_conflict` | Stale expected revision |
| 409 | `idempotency_conflict` | Key reused for different request |
| 409 | `state_conflict` | Invalid lifecycle transition |
| 413 | `payload_too_large` | Configured/hard byte limit exceeded |
| 422 | `validation_error` | Schema or field semantics invalid |
| 422 | `ambiguous_time` | Temporal intent requires explicit time |
| 429 | `rate_limited` | Auth or request rate limit |
| 503 | `inference_unavailable` | Operation requires unavailable inference |
| 503 | `service_unavailable` | Required local subsystem unavailable |

API-006: `429` and retryable `503` responses MUST include `Retry-After`.

## Pagination

API-007: List endpoints use opaque cursor pagination with `limit` default 50,
maximum 100. Sort order is endpoint-defined and stable, with ID as final
tie-break. Cursors are integrity-protected and expire after 24 hours.

API-008: Pages contain `meta.next_cursor`; absence or null means completion.
Unauthorized objects do not contribute to page size or counts.

## Endpoints

| Method and path | Purpose | Actor/action | Success |
| --- | --- | --- | --- |
| `GET /health/live` | Process liveness, no dependency detail | unauthenticated loopback | 200 |
| `GET /health/ready` | Owner-safe dependency readiness | owner/system.manage | 200/503 |
| `POST /projects` | Create project | owner/project.write | 201 |
| `GET /projects` | List visible projects | authenticated/project.read | 200 |
| `POST /sources` | Import source | source.create | 202 |
| `GET /sources/{id}` | Source metadata/content if allowed | source.read | 200 |
| `GET /ingestions/{id}` | Import/extraction status | project.read | 200 |
| `POST /memories` | Explicit memory | memory.create | 201 |
| `GET /memories/{id}` | Memory, revision, provenance | memory.read | 200 |
| `PATCH /memories/{id}` | Correct metadata/text | memory.correct | 200 |
| `POST /memories/{id}/approve` | Approve pending version | review.manage | 200 |
| `POST /memories/{id}/reject` | Reject pending version | review.manage | 200 |
| `POST /search` | Ranked evidence retrieval | memory.read | 200 |
| `POST /context` | Bounded context pack | memory.read | 200 |
| `POST /clients` | Issue client credential | client.manage | 201 |
| `GET /clients` | List clients without token hashes | client.manage | 200 |
| `POST /clients/{id}/revoke` | Revoke token | client.manage | 200 |
| `PUT /clients/{id}/grants/{project_id}` | Create/replace grant | grant.manage | 200 |
| `DELETE /clients/{id}/grants/{project_id}` | Revoke grant | grant.manage | 204 |
| `DELETE /sources/{id}` | Revoke and purge source tree | delete.manage | 202 |
| `DELETE /memories/{id}` | Revoke and purge memory | delete.manage | 202 |
| `GET /deletions/{id}` | Cleanup status | delete.manage | 200 |
| `POST /exports` | Build portable bundle | export.manage | 202 |
| `GET /exports/{id}` | Export status/download metadata | export.manage | 200 |
| `GET /exports/{id}/download` | Stream a completed bundle | export.manage | 200 |
| `POST /imports` | Validate/stage bundle | import.manage | 202 |
| `GET /imports/{id}` | Import status/conflicts | import.manage | 200 |

API-009: `/health/live` reveals only `{status:"alive"}`. Detailed health,
versions, paths, models, and dependency errors require owner authentication.

## Core request shapes

`POST /sources`:

```json
{
  "project_id": "prj_...",
  "kind": "markdown",
  "title": "Docker troubleshooting",
  "content": "...",
  "retention": "until_deleted",
  "observed_at": null,
  "secret_handling": "cancel_on_detection"
}
```

`POST /memories`:

```json
{
  "project_id": "prj_...",
  "kind": "decision",
  "text": "Use SQLite for the v0.1 canonical store.",
  "sensitivity": "private",
  "observed_at": null,
  "valid_from": null,
  "valid_to": null,
  "confirm_activate": true
}
```

`POST /search`:

```json
{
  "project_id": "prj_...",
  "query": "How did we fix the Docker database connection?",
  "limit": 5,
  "time_mode": "current",
  "as_of": null,
  "kinds": [],
  "include_evidence": true
}
```

`POST /context` adds `token_budget` and `tokenizer` to the search fields.

`PATCH /memories/{id}`:

```json
{
  "expected_revision": 3,
  "text": "Corrected supported claim.",
  "kind": "fact",
  "sensitivity": "private",
  "valid_from": null,
  "valid_to": null,
  "reason": "Owner correction after source review"
}
```

API-010: Optional nullable fields MUST distinguish omission (“leave unchanged”
for PATCH) from explicit null (“clear value”).

## Asynchronous operations

API-011: Accepted ingestion, deletion, export, and import return `202` with an
operation resource and `Location` header. Operation states are `queued`,
`running`, `needs_review`, `succeeded`, `failed`, and `cancelled`.

API-012: Polling an operation MUST be safe and idempotent. Error details use
stable codes and sanitized messages. Operation content follows the same
authorization as its target.

Completed export downloads MUST use an attachment filename generated from the
opaque export ID, set an exact byte length and SHA-256 in metadata, use
`application/zip`, and return `404` after artifact expiry.

## Contract publication

API-013: The implementation MUST generate and commit an OpenAPI document that
matches this specification. Contract tests MUST compare representative runtime
responses with that document. The generated file is subordinate to this
directory and a mismatch blocks release.

API-014: `/v1` remains backward compatible for the lifetime of v0.1 patch/minor
releases. Removing a field, changing meaning, tightening an accepted enum, or
changing authorization requires a new API version or an explicitly documented
security correction.
