# 09 — Security and privacy specification

## Security goals and limits

SEC-001: LocalMind protects against accidental client overreach, malicious
imported text, cross-project disclosure, unauthenticated network peers, leaked
logs, stale workers, and ordinary credential misuse.

SEC-002: v0.1 does not claim protection against a compromised owner/admin
account, compromised OS, memory scraping with equivalent privilege, malicious
local database modification, physical forensic recovery, or a recipient that
retains previously returned text.

## Trust boundaries

Untrusted inputs include all imported source text, file metadata, search
queries, model output, MCP arguments, client headers, imported bundles, and
browser content. Trusted code boundaries are the backend validator, policy
engine, database transaction layer, and release-pinned application artifacts.
Ollama output is untrusted even when locally hosted.

SEC-003: Untrusted text MUST never be evaluated as code, SQL, shell, template,
Markdown HTML, FTS syntax, path, configuration, policy, or model instruction.

SEC-004: SQL MUST use parameterized statements. Browser rendering MUST escape
text by default; Markdown rendering, if enabled, MUST disable raw HTML and
sanitize links.

SEC-005: General archive extraction is not supported. The only accepted archive
is a LocalMind portable ZIP validated before extraction. It MUST reject
absolute paths, parent traversal, links, duplicate names, encrypted entries,
unexpected files, excess record/file counts, excess per-file or total
uncompressed size, and compression ratios over the configured safe limit.
Export/import paths are generated server-side and confined to the configured
artifact directory after canonical path validation.

## Network controls

SEC-006: API and dashboard bind to `127.0.0.1` and `::1` by default. Startup
MUST fail closed for a non-loopback bind unless a future supported deployment
profile explicitly configures TLS and authentication.

SEC-007: Ollama MUST bind to loopback. Two-machine use exposes it only through
the protected mechanism in `SYS-008`. Firewall rules SHOULD allow only the
development PC where a private network is used.

SEC-008: After model installation, an offline test MUST demonstrate that normal
operation makes no internet inference requests. Telemetry is disabled by
default and requires an explicit future specification to enable.

SEC-009: Outbound HTTP is denied by default except the configured inference
endpoint. Source URLs or imported text MUST never trigger fetches.

## Secret and credential handling

SEC-010: Token hashes, application secrets, and owner session keys MUST use OS
permissions restricting access to the owner account. Raw client tokens are
never stored in the database.

SEC-011: Configuration values whose names or schemas mark them secret MUST be
redacted in diagnostics. Environment dumps are forbidden.

SEC-012: Logs, traces, metrics labels, audit events, exception reports, fixtures,
screenshots, and demo data MUST NOT contain tokens, source bodies, memory text,
evidence quotes, vectors, or secret detector matches.

## Application controls

SEC-013: All mutation input uses allowlisted Pydantic schemas with length,
enum, and range bounds. Mass assignment from request objects into ORM models is
forbidden.

SEC-014: Responses MUST set `Content-Type`, `X-Content-Type-Options: nosniff`,
`Referrer-Policy: no-referrer`, a restrictive CSP for the dashboard, and
`Cache-Control: no-store` for authenticated or content-bearing responses.

SEC-015: Sensitive comparisons and token verification MUST not short-circuit in
a way that leaks secret material. Authentication failures return one generic
external code.

SEC-016: Dependency lock files, automated vulnerability scanning, and secret
scanning are required in CI. A critical known exploitable vulnerability blocks
release unless a time-bounded, documented owner-accepted exception exists.

## Model and prompt-injection controls

SEC-017: Imported instructions are evidence content only. Extraction has no
tools and its output passes deterministic validation and owner review.

SEC-018: Retrieved text and MCP output MUST be delimited and labeled untrusted.
No retrieved content may select a tool, expand scope, change system prompts,
authorize an action, or bypass confirmation.

SEC-019: Model confidence values MUST NOT be interpreted or presented as
calibrated factual probabilities.

## Privacy semantics

SEC-020: Capture is opt-in per action. LocalMind MUST NOT monitor clipboard,
keystrokes, terminal, browser history, microphones, cameras, or arbitrary
files.

SEC-021: Source text, memories, vectors, and summaries are equally sensitive for
access-control purposes.

SEC-022: Sending a memory to an external/cloud client discloses that returned
text to the client provider. The UI MUST state this before granting private
access to an external client.

SEC-023: MVP at-rest protection is OS file permissions plus recommended
full-disk encryption. Application-level database encryption MUST NOT be claimed.

## Audit

SEC-024: Audit events MUST include actor, action, target opaque ID, result,
request ID, and time, but no user content or credentials.

SEC-025: Required audited events are login/bootstrap, client issue/revoke,
grant change, import acceptance, review decision, correction, sensitivity
change, deletion, export/import, configuration change, reconciliation repair,
and restore.

## Security release suite

SEC-026: At least 20 adversarial cases MUST cover authentication, authorization,
ID enumeration, prompt injection, stored/rendered injection, FTS injection,
path traversal, oversized input, token leakage, deletion races, stale jobs,
malformed imports, and offline egress.

SEC-027: Any unauthorized content disclosure, credential disclosure, deleted
content retrieval, or silent cloud fallback is a release blocker.
