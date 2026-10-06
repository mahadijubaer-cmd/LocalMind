# 06 — Retrieval and context specification

## Query contract

RET-001: A search request contains `query`, one authorized `project_id`,
`limit`, `time_mode`, optional `as_of`, optional memory kinds, and
`include_evidence`. Blank queries are allowed only with an explicit structured
filter and owner authentication.

RET-002: `time_mode` is `current` or `historical`. Historical mode requires
owner access or an explicit grant capability and MUST be visibly labeled.

RET-003: Date, state, owner, project, sensitivity, and grant filters MUST be
applied before lexical or semantic ranking.

## Lexical retrieval

RET-004: FTS5 MUST index current active memory text and exact evidence-safe
technical tokens. Query parsing MUST treat user input as data and prevent FTS
syntax errors or operator injection.

RET-005: Exact phrase, code identifier, and error-string matches SHOULD receive
a documented tie-break advantage. Match snippets MUST originate only from
authorized content and MUST escape output for its rendering context.

## Semantic retrieval

RET-006: Semantic search is enabled only when a query embedding compatible with
the active index generation is available.

RET-007: Semantic similarity MUST be calculated only across authorized,
eligible vectors. Loading or scoring all vectors followed by filtering is
forbidden.

RET-008: Embedding input MUST use the same model digest and preprocessing as
indexed memory text. A model change creates a new generation and queues a full
re-embedding; generations are never mixed.

## Fusion and ranking

RET-009: v0.1 hybrid ranking uses reciprocal rank fusion:
`score = Σ 1 / (60 + rank_i)` for available lexical and semantic ranked lists.
Ranks are one-based. The constant 60 is configurable only through a versioned
evaluation decision.

RET-010: Deterministic tie-break order is: exact normalized phrase match,
owner-pinned flag, lower best component rank, then lexicographically smaller
memory ID. Recency MUST NOT be an implicit boost.

RET-011: Closely duplicated versions of the same memory collapse to one result.
Distinct contradictory memories remain separate and carry `conflict=true`.

RET-012: Scores MUST be labeled `rank_score`, never confidence, truth, or
probability.

## Result contract

RET-013: Each result MUST include memory ID and revision, kind, text,
sensitivity-safe status, project ID, source title and version ID, evidence
excerpt when requested, observed/valid dates, current/historical label, rank
score, component ranks, and a short deterministic match rationale.

RET-014: A result MUST NOT reveal inaccessible source IDs, titles, evidence,
counts, or conflict relationships.

RET-015: If no eligible result meets the evaluated minimum relevance rule,
the response MUST return `results=[]`, `abstained=true`, and a non-sensitive
reason. v0.1 MUST establish the minimum rule from the held-out evaluation set
before release; absence of an approved threshold blocks release.

RET-016: Query execution MUST not invoke a generative model.

## Context packs

RET-017: Context assembly consumes authorized search results, never a fresh
unfiltered store query.

RET-018: The default budget is 1,000 tokens. The pack MUST reserve framing
overhead, enforce the requested/hard maximum, and never truncate inside an
evidence quote without marking truncation.

RET-019: Token count MUST use the configured client tokenizer when known.
Otherwise it uses a documented conservative estimate, applies a 20% safety
margin, and returns `token_count_estimated=true`.

RET-020: A context item includes inert memory text, source label, dates, and
memory ID. It MUST delimit memory as quoted data and MUST NOT turn instructions
found in memory into tool or system instructions.

RET-021: Context ordering follows search rank, subject to deduplication and
budget fit. The assembler MAY skip an oversized lower-ranked item to fit a
higher-value combination but MUST report omitted result IDs to the owner only.

## Temporal questions

RET-022: “Now/current” queries exclude superseded versions. “Before/previous”
queries use historical mode. If the requested point in time cannot be
unambiguously derived, the API returns `422 ambiguous_time` with a safe prompt
for `as_of` rather than guessing.

## Multilingual behavior

RET-023: UTF-8 Bangla, English, mixed-language prose, code identifiers, and
error strings MUST pass unchanged through storage and evidence validation.
Quality claims for a language require a separately reported evaluation slice.
