# State Document Protocol

Every Startup Skills skill reads and writes `STARTUP-STATE.md` using the protocol below. No skill maintains private state; no skill asks the user to re-summarize what other skills have already captured.

## Default location

- **Claude Code (default):** project-local at `.claude/startup-state.md`. Each project gets its own state file.
- **Override:** set the `STARTUP_STATE_PATH` environment variable to an absolute path.
- **claude.ai:** maintain the document as a Markdown artifact within the project and copy it forward into new conversations.

## Read at activation

1. Read `STARTUP-STATE.md` (default path or `STARTUP_STATE_PATH`).
2. If the file does not exist:
   - If the current skill is `orientation`, create it from `references/templates/state-document-template.md`.
   - Otherwise, stop and hand off: "No state document found. Run `orientation` first so we have a shared anchor."
3. Skim the document fully before responding. Build context from the founder profile, current hypothesis, evidence log, and recent Session Log entries.

## Write at completion

1. Update only the sections this skill owns. Do not blank out sections owned by other skills.
2. Bump the `_Last updated_` header to today's ISO date with the skill name: `_Last updated: 2026-05-12 by founder-context_`.
3. Append a one-line Session Log entry: `- [YYYY-MM-DD] <skill-name> — <one-sentence summary of decisions made and evidence added>`.
4. Bump `_Schema version_` ONLY if the schema itself has changed (sections added, columns added). Routine updates leave it alone.
5. Preserve append-only sections: never edit prior Evidence Log rows or prior One-Liner versions. If a prior entry needs correction, add a new row that points to the original in Notes.

## Concurrency

Startup Skills assumes a single Claude session per state file at a time. If a user opens two Claude conversations against the same project, the last writer wins.

## Failure modes this prevents

- Founders re-introducing themselves every session.
- Skills contradicting earlier decisions because they didn't read prior state.
- Lost evidence between sessions.
- Drift between what was decided and what the next skill assumes.
