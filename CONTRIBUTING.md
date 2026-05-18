# Contributing

Thanks for considering a contribution. The system is small, opinionated, and load-bearing for founders — keep that bar in mind.

## Reporting issues

Open a GitHub issue. Include:
- The skill name (e.g. `discovery-coach`) or reference name (e.g. `bias-sentinel.md`)
- What you said to Claude
- What the system did vs. what you expected
- Your install path (Claude Code / claude.ai / other)

For bias-sentinel false positives, paste the user message that fired the bias and the bias name.

## Pull requests

**Hard requirements:**
- Skills stay under 250 lines.
- References cite named sources inline (Mom Test, YC partner, Lenny's Newsletter, etc.) — no unsupported claims.
- YAML frontmatter on every SKILL.md uses `description: >` with a `(startup-skills)` prefix. Format: `(startup-skills) Use when [condition]. Fires on [trigger phrases]. [Key behavior/constraint]. [Key refusal].` See existing skills for examples.
- Decision-gate skills carry a `## HARD-GATE` block near the top of the body.
- No forward-reference debt (`if available`, `until then`, `ships in vN`) — everything must resolve at PR time.

**Reference placement:**
- Pedagogically rich teaching documents belong in `references/composed/`.
- Fill-in-verbatim assets (templates, structural forms) belong in `references/templates/`.
- Atomic concept files go in `references/kernels/<domain>/` and are managed via `knowledge-pipeline` — do not add kernels by hand.
- Skills load references via `${CLAUDE_PLUGIN_ROOT}/references/composed/<file>.md` or `${CLAUDE_PLUGIN_ROOT}/references/templates/<file>.md`. Verify every path you cite actually exists before submitting.

**Validation:**
- Run `claude plugin validate plugins/startup-skills` locally before submitting.

**Bump:**
- `plugins/startup-skills/.claude-plugin/plugin.json` version.
- `CHANGELOG.md` entry with the version + changes.

**Tone:**
- Aggressive Epistemic Auditor voice (see `references/composed/tone-and-stance.md` + `references/composed/aggressive-consultation-archetype.md`). Direct, willing to wound, never cruel.
- No marketing words ("leverage," "transform," "unleash"). Direct verbs.
- Refusal logic must be crisp, with explicit rationale and the evidence that would change the answer.

## What we will reject

- Pre-PMF skills that validate bias-amplifying behaviors (e.g., a skill that helps founders justify staying with a tar-pit idea).
- Methodology without named attribution.
- Skills that produce artifacts without thin-section warnings when state is sparse.
- Sycophantic phrasing.
- Forward-reference debt.
- Kernel files added directly — use `knowledge-pipeline` INGEST mode instead.

## Design rationale

Before proposing a new skill or reference, read:
- [`docs/superpowers/specs/2026-05-18-knowledge-architecture-design.md`](./docs/superpowers/specs/2026-05-18-knowledge-architecture-design.md) — current architecture (canonical).

Open an issue for discussion before opening a non-trivial PR.

## License

By contributing, you agree your contribution is licensed under PolyForm Noncommercial 1.0.0 (matching the project license). Commercial sub-licensing rights remain with the maintainer.
