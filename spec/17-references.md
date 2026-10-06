# 17 — References and external verification

This file is informative where it describes third-party products. The
verification requirements are normative. External documentation never
overrides the LocalMind requirements.

## Primary references

| Ref | Subject | Primary source |
| --- | --- | --- |
| R1 | SQLite FTS5 | https://www.sqlite.org/fts5.html |
| R2 | SQLite backup API | https://www.sqlite.org/backup.html |
| R3 | SQLite WAL | https://www.sqlite.org/wal.html |
| R4 | Model Context Protocol specification | https://modelcontextprotocol.io/specification/2026-07-28 |
| R5 | MCP authorization guidance | https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/authorization |
| R6 | MCP security practices | https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices |
| R7 | Ollama structured outputs | https://docs.ollama.com/capabilities/structured-outputs |
| R8 | Ollama embeddings | https://docs.ollama.com/capabilities/embeddings |
| R9 | Ollama networking/FAQ | https://docs.ollama.com/faq |
| R10 | Qwen 2.5 3B model listing | https://ollama.com/library/qwen2.5:3b |
| R11 | EmbeddingGemma model listing | https://ollama.com/library/embeddinggemma |
| R12 | Mem0 project | https://github.com/mem0ai/mem0 |
| R13 | Letta stateful-agent concepts | https://docs.letta.com/v1-sdk/concepts/stateful-agents |
| R14 | WCAG 2.2 | https://www.w3.org/TR/WCAG22/ |
| R15 | OpenAPI Specification | https://spec.openapis.org/oas/latest.html |

Product and competitor references support context only. They do not establish
market demand, differentiation, compatibility, security, or LocalMind quality.

## Verification policy

REF-001: Before a dependency or protocol is pinned, its official documentation,
release notes, license, supported runtime, and security notices MUST be checked
and the date/version recorded.

REF-002: Before release, every URL above that supports implemented behavior
MUST be rechecked. Moved or versioned documentation must be updated without
silently changing LocalMind behavior.

REF-003: The MCP SDK and protocol versions MUST be mutually supported and
verified against the selected real client. “Latest” is not a valid pin.

REF-004: Each model requires exact artifact/digest, license, redistribution
terms, context limit, embedding dimension where applicable, quantization, and
benchmark record. A model listing is not proof of fitness.

REF-005: SQLite runtime MUST be tested for FTS5, WAL, backup API behavior, and
the exact library version distributed with LocalMind.

REF-006: Marketing or README claims about integrations, languages, privacy,
speed, scale, security, or adoption MUST cite LocalMind’s own measured release
evidence rather than infer claims from third-party documentation.
