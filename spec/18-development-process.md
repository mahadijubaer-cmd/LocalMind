# 18 — Specification-first development process

This process is normative for all product, infrastructure, interface, data,
security, and operational work. It converts “spec first” into an enforceable
sequence. The sprint plan in `19-sprint-plan.md` schedules the work; this file
controls how every change is performed.

## Core rule

DEV-001: Production code, migrations, generated contracts, and user-facing UI
MUST NOT be written or changed until the relevant behavior is fully described
by approved requirement IDs in this directory.

DEV-002: No document outside `spec/` may create, weaken, reinterpret, or
override a product requirement. Operational files such as `AGENTS.md`, issue
templates, code comments, and README files may only point to canonical IDs.

DEV-003: If a requested change conflicts with the specification, work MUST stop
at the specification boundary. The specification and its decision/change record
must be updated before implementation resumes.

DEV-004: “Temporary,” prototype, test-only, or internal code MUST NOT bypass
authentication, project isolation, provenance, lifecycle generation, deletion,
secret-handling, or offline-inference rules when it can touch canonical data.

## Change sequence

Every implementation change follows these stages in order.

### Stage 1 — Locate authority

1. Read `spec/README.md`.
2. Identify the owning specification files and exact requirement IDs.
3. Check dependencies, assumptions, decisions, and the active sprint gate.
4. State the IDs in the task or pull request.

DEV-005: A task without linked requirement IDs is not ready for implementation.
If no requirement covers it, proceed only to Stage 2.

### Stage 2 — Specify

Create or revise the smallest complete canonical contract. It must define, as
applicable:

- actors and authorization;
- inputs, outputs, defaults, limits, errors, and idempotency;
- states, transitions, invariants, time semantics, and concurrency;
- data ownership, migration, retention, deletion, and recovery;
- privacy, threat, logging, and network effects;
- degraded/offline behavior;
- observability and operational behavior; and
- measurable acceptance and compatibility impact.

DEV-006: A spec change MUST update the owning requirements,
`14-traceability.md`, and `15-decisions-and-changes.md` in the same spec commit.

DEV-007: New requirement IDs are append-only within their family. Existing IDs
retain their meaning; semantic replacement requires a new ID and a recorded
supersession.

DEV-008: Unresolved design choices MUST be explicit `DEC-*` items with a
deadline and conservative default. Implementation MUST NOT silently choose.

### Stage 3 — Review and freeze

Review the spec delta for contradictions, missing failure behavior, security or
deletion regression, incompatible API/data changes, and unverifiable language.

DEV-009: The spec delta is frozen for implementation only when:

- all affected requirements are testable;
- every MUST has a verification method;
- migrations and backward compatibility are explicit;
- security/privacy/deletion effects are resolved;
- open decisions required by the current sprint are closed; and
- local-link, requirement-ID, and formatting checks pass.

The frozen spec change SHOULD be a distinct commit named
`spec: <behavioral contract>`. Later correction is allowed, but returns the
change to Stage 2.

### Stage 4 — Design verification first

Write or update executable tests, fixtures, schemas, and acceptance scenarios
before production implementation. A failing test is expected when it describes
new behavior.

DEV-010: Each test or evidence record MUST cite its exact requirement IDs in
test metadata, name, docstring, or adjacent manifest.

DEV-011: Tests MUST cover the success path, boundary values, denied path,
invalid state, retry/idempotency, and relevant outage/race/deletion path.

DEV-012: API work starts with request/response/error contract tests and an
OpenAPI delta. Data work starts with schema invariants and migration tests. UI
work starts with states and accessibility acceptance. Security-sensitive work
starts with a deny/leak regression test.

### Stage 5 — Implement the minimum vertical slice

DEV-013: Implementation MUST be the smallest end-to-end slice that satisfies
the frozen requirements and tests. Speculative abstractions and unscheduled
later-release features are forbidden.

DEV-014: The policy engine, canonical store, and lifecycle services MUST remain
the shared path. CLI, MCP, dashboard, and REST adapters may not duplicate core
rules.

DEV-015: New dependencies require a documented need, license/security review,
version pin, and reference verification before merge.

### Stage 6 — Verify

Run the smallest relevant checks during development, then the complete required
gate before closing the task or sprint.

DEV-016: Verification MUST include all directly linked tests plus affected
policy, migration, lifecycle, security, interface, and regression suites.

DEV-017: A failing required check is not “known acceptable” unless the
specification contains a time-bounded exception with owner, reason, exposure,
and removal criterion. Security blockers in `SEC-027` cannot be excepted for
release.

### Stage 7 — Trace and close

DEV-018: Completion evidence MUST record requirement IDs, changed files,
tests/commands and results, migrations, compatibility effects, known limits,
and follow-up work.

DEV-019: `14-traceability.md` or its future generated evidence manifest MUST
show every implemented MUST requirement as verified before its sprint gate can
pass.

DEV-020: A task is closed only when `DEL-006` and the task’s sprint exit
criteria are satisfied. Partial work remains explicitly open.

## Sprint operating rhythm

Each sprint is one focused week at approximately 12–15 hours. Scope may shrink,
but gates may not.

| Phase | Purpose | Required output |
| --- | --- | --- |
| Plan | Select only gate-critical work | Sprint brief with IDs, dependencies, capacity, risks |
| Specify | Resolve behavioral gaps first | Frozen spec delta and decisions |
| Verify design | Define proof before code | Failing contract/acceptance tests and fixtures |
| Build | Implement vertical slices | Reviewed code and migrations |
| Validate | Run full sprint gate | Test, benchmark, security, and manual evidence |
| Review | Demonstrate and decide | Gate result, demo, residual risks, next-sprint decision |

DEV-021: Work in progress is limited to one vertical slice per developer.
Blocked work is made visible; unrelated work is not pulled forward if it
violates dependency order.

DEV-022: A sprint is complete only when its exit gate passes. Unfinished work
does not receive partial credit toward the next gate and is replanned without
weakening requirements.

DEV-023: Emergency defect order is data loss or unauthorized disclosure first,
then deletion/authentication failures, migration/recovery failures, incorrect
memory/provenance, availability, performance, and cosmetic defects.

## Required planning artifacts

### Sprint brief

```text
Sprint:
Goal:
Entry gate:
Requirement IDs:
Decisions due:
In scope:
Out of scope:
Vertical slices in dependency order:
Verification:
Demo:
Risks:
Exit gate:
```

### Task card

```text
Outcome:
Requirement IDs:
Current specified behavior:
Spec delta required: yes/no
Inputs/outputs/errors:
Security/privacy/deletion impact:
Data/migration impact:
Tests to write first:
Implementation boundary:
Evidence required:
```

### Completion evidence

```text
Requirement IDs:
Spec commit:
Implementation commit:
Tests and results:
Manual evidence:
Migration/compatibility:
Known limitations:
Follow-up:
```

DEV-024: Sprint briefs, task cards, and completion evidence MAY live in the
issue tracker, but they MUST link canonical requirements and MUST NOT redefine
them.

## Stop conditions

Work stops and returns to specification when:

- no requirement authorizes the behavior;
- two canonical requirements conflict;
- a required decision is unresolved;
- implementation reveals an unmodeled state, error, permission, or deletion
  path;
- a data migration or API compatibility effect was omitted;
- acceptance cannot be tested objectively; or
- the active sprint’s entry gate has not passed.

DEV-025: When stopped, the next action is a spec/decision proposal, not an
implementation guess.
