# 02 — Domain and lifecycle specification

## Entity ownership

DOM-001: Every project, client, source, memory, job, and operation MUST belong
to exactly one owner, even though v0.1 has only one owner.

DOM-002: Every source and memory MUST belong to exactly one project. “Global
preference” is represented by a reserved owner-created project, not a null
project.

DOM-003: Authentication, not request fields, determines the actor and owner.

## Memory kinds

Allowed v0.1 kinds are `preference`, `decision`, `procedure`, `episode`, `fact`,
and `task`. Unknown kinds MUST be rejected. A task is stored knowledge only;
v0.1 does not schedule or remind.

## Source lifecycle

States are `active`, `deletion_pending`, `deleted`, and `cleanup_failed`.

DOM-004: Valid transitions are:

- `active → deletion_pending`;
- `deletion_pending → deleted`;
- `deletion_pending → cleanup_failed`; and
- `cleanup_failed → deletion_pending` on explicit or scheduled retry.

No transition out of `deleted` is allowed. Restore creates new IDs and passes
through import review.

## Memory lifecycle

The memory aggregate has a stable ID and append-only versions. Its state is one
of `pending`, `active`, `rejected`, `superseded`, `deletion_pending`, `deleted`,
or `cleanup_failed`.

DOM-005: Generated candidates start `pending`. Owner-authored memories MAY
start `active` after deterministic validation and an explicit confirmation.

DOM-006: Valid transitions are:

- `pending → active | rejected | deletion_pending`;
- `active → superseded | deletion_pending`;
- `rejected → pending | deletion_pending` only through an explicit owner action;
- `superseded → deletion_pending`;
- `deletion_pending → deleted | cleanup_failed`;
- `cleanup_failed → deletion_pending`.

DOM-007: Only `active` versions are returned in normal retrieval. Historical
retrieval MAY return `superseded` versions. Pending, rejected,
deletion-pending, deleted, and cleanup-failed content MUST never be returned to
external clients.

## Versions and corrections

DOM-008: Source versions are immutable. Reimporting changed content creates a
new source version; it never edits stored normalized text in place.

DOM-009: An approved memory correction MUST append a memory version, increment
the aggregate revision by one, and record actor, timestamp, reason, and
superseded version.

The corrected memory aggregate remains `active`; its former current version is
historical. Aggregate state `superseded` is used only when one whole memory is
replaced by a different memory aggregate.

DOM-010: Mutation requests MUST supply `expected_revision`. A mismatch MUST
make no change and return `409 revision_conflict` with the current revision if
the caller can access the object.

DOM-011: Each generated memory version MUST reference one source version and at
least one exact evidence span. Additional independent evidence links MAY be
added after review.

DOM-012: An owner-authored memory MUST use an immutable synthetic source version
containing the owner’s exact submitted text.

## Time semantics

Each memory version has:

- `observed_at`: when the claim was observed in its source, nullable;
- `valid_from`: earliest stated validity, nullable;
- `valid_to`: exclusive end of stated validity, nullable;
- `created_at`: system persistence time, required;
- `superseded_at`: system transition time, nullable.

DOM-013: Unknown source event times MUST be null. Import or extraction time MUST
NOT be substituted.

DOM-014: A current query includes active versions whose validity interval
contains query time, treating a null boundary as open. A historical query may
include superseded versions and requires an explicit `as_of` or
`include_history=true`.

DOM-015: Contradictory supported claims MUST remain separately attributable
until the owner resolves them. Similarity or recency alone MUST NOT overwrite a
claim.

## Evidence spans

DOM-016: Normalized source text MUST be UTF-8 and normalized to Unicode NFC with
line endings converted to LF.

DOM-017: Evidence offsets are zero-based, half-open Unicode code-point offsets
`[start, end)` into the stored normalized text.

DOM-018: The substring at every evidence span MUST exactly equal the stored
evidence quote. A candidate that fails this check cannot be approved.

DOM-019: If original-file highlighting is offered, the source version MUST
store or derive a deterministic original-to-normalized offset mapping.

## Sensitivity

Allowed sensitivity levels, from least to most restrictive, are `shareable`,
`private`, and `restricted`.

DOM-020: An external client can receive `shareable` content and MAY receive
`private` content only with an explicit compatible grant. It MUST never receive
`restricted` content.

DOM-021: Local clients receive no implicit access from their class; all
non-owner clients still require project grants.

## Identifier and timestamp rules

DOM-022: Public IDs MUST be opaque, URL-safe, unguessable identifiers with a
type prefix and at least 128 bits of randomness. Sequential database keys MUST
NOT be exposed.

DOM-023: API timestamps MUST be RFC 3339 UTC strings with `Z`. Persistence MUST
retain microsecond precision where available.

DOM-024: Lifecycle generation is a monotonically increasing integer on sources
and memories. Jobs MUST compare their captured generation before committing.
