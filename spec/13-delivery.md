# 13 — Delivery plan and definition of done

This file defines implementation order. Calendar estimates never override exit
criteria.

The detailed execution sequence and sprint gates are canonical in
`19-sprint-plan.md`; all work follows `18-development-process.md`.

## Milestones

| Milestone | Deliverable | Mandatory exit |
| --- | --- | --- |
| M0 Feasibility | User interviews; model, hardware, and network spike | Five interviews; hardware/model digest and warm/cold measurements; protected connection proven |
| M1 Foundation | Repository skeleton, configuration, SQLite migrations, projects, sources | Fresh/upgrade migrations pass; idempotent import; authenticated CRUD; project isolation |
| M2 Extraction | Durable worker, chunking, model provider, validation | Restart/retry pass; exact evidence enforced; pending candidates visible; deletion race safe |
| M3 Review | Dashboard review, memory versions, conflict handling | Approve/edit/reject/restrict and stale revision tests pass |
| M4 Retrieval | FTS, embeddings, authorized fusion, contexts | Held-out baseline run; exact search gate; semantic fallback; no unauthorized influence |
| M5 Clients | Owner CLI and stdio MCP | Two authenticated real clients retrieve same evidence; revocation verified |
| M6 Lifecycle | Deletion, retention, backup/restore, portability | All cleanup paths and round trip pass; documented backup limitations |
| M7 Hardening | Security, accessibility, packaging, operations | Security suite, clean install, outage, disk/restart, and accessibility gates pass |
| M8 Beta | Five-developer closed beta | Critical defects fixed; recall usefulness and review effort reported |
| M9 v0.1 | Release artifacts and synthetic demo | Every gate in `12-quality.md`; reproducible release evidence complete |

DEL-001: Provenance, authorization, and lifecycle-generation foundations MUST
precede external integrations.

DEL-002: Correction, revocation, and deletion MUST pass before beta.

DEL-003: Graphs, remote bridges, autonomous capture, team features, and desktop
wrappers MUST NOT displace a v0.1 mandatory exit criterion.

## Work packages

| ID | Priority | Work | Depends on | Acceptance owner |
| --- | --- | --- | --- | --- |
| LM-01 | P0 | Model/network feasibility | none | M0 |
| LM-02 | P0 | Source/version schema and migrations | LM-01 | M1 |
| LM-03 | P0 | Client authentication and project policy | LM-02 | M1 |
| LM-04 | P0 | Durable jobs and lifecycle generations | LM-02 | M2 |
| LM-05 | P0 | Extraction and evidence validator | LM-04 | M2 |
| LM-06 | P0 | Review, memory versions, conflicts | LM-03, LM-05 | M3 |
| LM-07 | P0 | Authorized hybrid retrieval and contexts | LM-03, LM-06 | M4 |
| LM-08 | P0 | CLI, MCP, two-client demonstration | LM-07 | M5 |
| LM-09 | P0 | Deletion, retention, reconciliation | LM-04, LM-06, LM-07 | M6 |
| LM-10 | P1 | Backup, export/import, install docs | LM-09 | M6/M7 |
| LM-11 | P1 | Security, accessibility, packaging | LM-08, LM-10 | M7 |
| LM-12 | P1 | Beta fixes and benchmark report | LM-11 | M8 |
| LM-13 | P2 | VS Code selected-text capture | v0.2 only | later |
| LM-14 | P2 | Graph and multilingual improvements | v0.2+ only | later |

DEL-004: Each work package MUST link implemented requirement IDs and tests.
Closing a package with unverified linked MUST requirements is forbidden.

## Definition of ready

DEL-005: An implementation issue is ready only when it states:

- linked requirement IDs;
- in-scope and out-of-scope behavior;
- input/output or UI contract;
- migration/security/privacy effects;
- acceptance tests;
- dependency and rollout order; and
- unresolved decision, if any.

## Definition of done

DEL-006: A change is done only when:

- implementation matches the canonical spec;
- new/changed behavior has automated tests;
- policy and deletion effects were assessed;
- schema changes include migrations and upgrade tests;
- public interfaces and compatibility artifacts are updated;
- logs contain no prohibited content;
- documentation links the relevant requirement IDs;
- formatting, typing, tests, and scans pass; and
- a spec change record exists when behavior changed.

## Initial schedule assumption

The planning estimate is ten weeks at 12–15 focused hours per week for a
developer already comfortable with APIs and frontend work. Add two to four
weeks for unfamiliar Python, MCP, or packaging. This is informative; milestone
exit evidence is normative.

## First seven implementation days

1. Define the synthetic Docker-fix demo and interview script.
2. Verify hardware; benchmark candidate extraction and embedding models.
3. Establish private PC-to-inference connectivity and outage behavior.
4. Create backend skeleton, migrations, projects, and source versions.
5. Add client authentication, idempotent import, and lexical baseline.
6. Add one durable extraction path with schema and evidence validation.
7. Review feasibility, choose the first MCP client, and freeze v0.1 matrix.

DEL-007: End-of-week proof requires persistent import, exact search, one
pending model candidate, rejected cross-project access, and safe inference
disconnect/recovery.
