# LocalMind repository rulebook

This file is an operational pointer for humans and coding agents. It introduces
no product requirements. The sole authority is `spec/README.md`.

## Mandatory order for every change

1. Read `spec/README.md`, `spec/18-development-process.md`, and the active
   sprint in `spec/19-sprint-plan.md`.
2. Identify and state the exact canonical requirement IDs.
3. If behavior is missing, ambiguous, or conflicting, change the specification,
   traceability matrix, and change log first. Do not edit production code.
4. Freeze and commit the spec delta before implementation.
5. Write or update requirement-linked tests/contracts before production code.
6. Implement only the smallest current-sprint vertical slice.
7. Run all directly affected checks and the current sprint gate.
8. Record requirement-to-test evidence before declaring completion.

## Non-negotiable boundaries

- Requirements live only under `spec/`.
- No code may weaken authentication, authorization, provenance, project
  isolation, lifecycle generation, deletion, secret handling, or offline-only
  inference.
- No caller-supplied owner, grant, sensitivity override, or approval identity
  is trusted.
- No generated memory becomes externally retrievable before owner approval.
- No unauthorized record may influence ranks, counts, snippets, context, or
  timing-safe existence responses.
- No deleted or revoked content may reappear through a stale job, cache, FTS,
  vector, backup restore, or adapter.
- No cloud inference fallback is allowed.
- No v0.2+ feature is implemented during the v0.1 sprint plan.

## Stop instead of guessing

Stop implementation and propose a spec/decision change when a requirement is
absent, contradictory, unverifiable, migration-unsafe, or blocked by an open
decision. Follow `DEV-025`.

## Completion report

Every completed task must report:

- requirement IDs;
- spec and implementation commits;
- tests/commands and results;
- migration, security, privacy, deletion, and compatibility effects;
- remaining limitations; and
- the current sprint gate status.

Use `DEL-006` as the definition of done. A task is not complete merely because
code compiles or a happy-path demo works.
