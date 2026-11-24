# Clean ChatGPT Flavor

> **中文说明**：请查看 [README_CN.md](README_CN.md)。

A self-contained Agent Skill for countering overly defensive "ChatGPT flavor" in docs, copy, prompts, code, tests, JSON schemas, LLM payloads, and data pipelines.

## What Problem It Solves

ChatGPT-style generation often drifts into a "forensic auditor / CYA lawyer" posture — treating internal trusted steps and ordinary human readers as hostile auditors:

1. **Anxious prose & stacked disclaimers**: Legalistic hedges (`"does not by itself"`, `"is not proof"`, `"cannot establish"`, `"不能证明"`, `"并不等于"`), raw 64-character SHA-256 digests shown to readers, and self-justifying compliance logs wired into live docs.
2. **Unnecessary hashing & verification theater**: Hashing ordinary scripts/configs into manifests (`program_sha256`, `sourceHashes`), locking editable source or UI files to stored hash baselines in tests, standalone `verify_*.py` replay scripts, and blocking `--overwrite`/`--resume` behind code hashes.
3. **CYA schema fields & polluted LLM payloads**: Baking `limits`, `*_review`, or unused `*_sha256` fields into machine-readable JSON and forwarding defensive natural-language caveats into downstream LLM calls, which wastes tokens and primes downstream models to hedge.
4. **Defensive multi-stage pipeline rituals**: Splitting a direct read or query into a mandatory `export -> prepare -> verify -> query` chain solely to prove intermediate files did not drift.

## How It Works (Two-Phase Default)

Editing prose and modifying code carry very different blast radius. By default, this skill separates them into two steps:

- **Phase A (Default) — Clean text directly + inventory defensive code**:
  - Directly cleans Markdown docs, READMEs, skills, prompt templates, and standalone user-facing copy.
  - Scans code, tests, schemas, LLM payload builders, and pipelines **without modifying them**, traces cross-boundary consumers (such as frontend `?v=` cache tags and test assertions), and hands you a structured inventory report.
- **Phase B (After confirmation or explicit request) — Govern code & schemas**:
  - Safely replaces reused cache/version tags, removes unasked hashing and CYA fields, strips defensive wrappers from external/LLM payloads, and verifies real end-to-end execution paths.

Real safety boundaries stay intact: path traversal guards, secret/PII stripping, destructive/paid action confirmations, explicit auth/signing or release checksums, and concise factual provenance statements.

## Install & Use

This repository is ready to use directly as a standalone Agent Skill folder (`SKILL.md` lives at the repository root).

### Codex (`~/.codex/skills/`)

```bash
git clone https://github.com/aaaAlexanderaaa/clean-chatgpt-flavor.git ~/.codex/skills/clean-chatgpt-flavor
```

### Project-local Agent Skills (`.agents/skills/`)

```bash
git clone https://github.com/aaaAlexanderaaa/clean-chatgpt-flavor.git .agents/skills/clean-chatgpt-flavor
```

### Claude Code (`~/.claude/skills/`)

```bash
git clone https://github.com/aaaAlexanderaaa/clean-chatgpt-flavor.git ~/.claude/skills/clean-chatgpt-flavor
```

### Example Prompts

- **Default pass (clean docs/prose directly + inventory defensive code)**:
  > Use `clean-chatgpt-flavor` on this repository: clean anxious docs and disclaimers directly, and give me an inventory of defensive code, hashing, and CYA schema fields before touching code.
- **Explicit full cleanup (Phase A + Phase B)**:
  > Use `clean-chatgpt-flavor` to clean both docs and defensive code/LLM payloads in `src/` and `tests/`, and verify end-to-end.
- **Authoring / review check**:
  > Review this PR against `clean-chatgpt-flavor` before merging.

## Repository Layout

| Path | Role |
|---|---|
| [SKILL.md](SKILL.md) | Main skill instructions, root-cause principle, two-phase workflow, Keep-vs-Cut table, and self-check |
| [references/catalog.md](references/catalog.md) | Concrete before/after rewrites across all four surfaces plus a sample Phase A code-inventory report |
| [agents/openai.yaml](agents/openai.yaml) | Optional Codex UI metadata (`display_name`, `short_description`, `default_prompt`) |
| [README.md](README.md) / [README_CN.md](README_CN.md) | Standalone English and Chinese documentation |
| [LICENSE](LICENSE) | MIT License |
