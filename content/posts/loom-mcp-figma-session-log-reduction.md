---
title: Cutting Figma MCP session logs ~10× with a DSL intermediate
description: Loom MCP replaces raw Figma JSON responses with a compact DSL plus server-side caching, shrinking Claude session logs from 491 KB to 53 KB on the same task.
tags: [mcp, llm, dsl, figma, claude-code]
draft: false
date: 2026-05-03
---

## TL;DR

Same Figma → React task on Claude Code: Figma MCP produced a **491 KB** session log, Loom MCP produced **53 KB** (−89%, ~1/10). Behind that: a compact DSL intermediate + server-side caching of generated code.

## Background

Figma MCP returns raw design JSON via `get_design_context`, and that response lands directly in the LLM context — a single call clocked **56.6k tokens** in our run. Even the *tool definitions* shipped on every turn ate ~9.8k tokens. Session logs balloon and budget burns fast.

## Approach

Loom replaces the JSON-blob pattern with two changes:

- **DSL intermediate.** Figma's verbose node tree is compressed into notation like `F.col.w390.bg(255,255,255)` — readable by humans and LLMs. The LLM only adds semantic annotations (`.as("Card")`, `.tag("nav")`, `.repeat(3)`).
- **References, not payloads.** `generate-react` caches React components server-side and returns just component names. The client fetches bodies on demand via `get-component`.

```
Figma URL → generate-dsl → DSL → (LLM annotates) → generate-react → get-component
                                                         ↓
                                                  server-side cache
```

**Structure transforms run as code, semantics come from the LLM.** Each side does what it is good at.

## Results

Single Enter on the same Figma frame:

| Metric                       | Figma MCP    | Loom MCP   | Reduction |
| ---------------------------- | ------------ | ---------- | --------- |
| Main tool response           | 56,600 tok   | 43 tok     | 99%       |
| Tool definitions per turn    | 9,800 tok    | 501 tok    | 95%       |
| Session log (`session.jsonl`)| 491 KB       | 53 KB      | 89%       |

Longest line in each session log:

```
# Figma MCP
452,469 bytes ← get_design_context response

# Loom MCP
4,165 bytes ← generate-dsl response
```

## Trade-offs

| Limitation                              | Note                                         |
| --------------------------------------- | -------------------------------------------- |
| Server-side cache is per-MCP-session    | Cache is lost if the MCP process restarts    |
| Auto Layout assumed                     | Absolute positioning not yet supported       |

## References

- Repo: <https://github.com/keisuke-na/loom>
- npm: `@keisuke-na/loom-mcp`
- Install: `claude mcp add loom --scope user -- npx @keisuke-na/loom-mcp`