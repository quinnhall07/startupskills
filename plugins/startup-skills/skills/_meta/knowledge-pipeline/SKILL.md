---
name: knowledge-pipeline
description: >
  (startup-skills, _meta) Use to ingest a new source (transcript, article,
  video) into the kernel layer, compose new or updated composed references
  from existing kernels, or audit the kernel graph for orphans, conflicts,
  and stale source links. Fires on "ingest this source," "extract kernels
  from X," "compose a reference from these kernels," "audit the knowledge
  base," or "process the new sources."
stages: [any]
topics: [knowledge-management, system-maintenance]
preconditions:
  - "[INGEST] source file path provided"
  - "[COMPOSE] kernel set or domain specified"
  - "[AUDIT] none"
postconditions:
  - "[INGEST] references/sources/INDEX.md appended AND references/kernels/<domain>/ files created or updated"
  - "[COMPOSE] references/composed/ files updated to cite kernels via wikilinks (and possibly new composed reference files created)"
  - "[AUDIT] human-readable audit report produced in chat (no file changes)"
successors:
  - knowledge-pipeline (COMPOSE mode — wire new kernels into composed references after INGEST)
predecessors: []
---

# Knowledge Pipeline

Drives the ingest → kernel → compose flow that keeps the knowledge base
coherent as it grows. Three modes: INGEST, COMPOSE, AUDIT. Always asks
before writing.

## When to activate

- Phrases: "ingest this transcript," "extract kernels from [source]," "process
  the new sources," "compose a reference from these kernels," "audit the
  knowledge base," "any orphan kernels?"
- Whenever a new file appears in `resources/` and the user wants it ingested.
- Periodically (every few sessions of ingestion) to run AUDIT.

## Required reading

- `${CLAUDE_PLUGIN_ROOT}/references/sources/INDEX.md` — what's already
  ingested; allocate next id from here.
- The current state of `${CLAUDE_PLUGIN_ROOT}/references/kernels/` — dedup
  check before creating new kernels.
- The current state of `${CLAUDE_PLUGIN_ROOT}/references/composed/` — what
  references exist and what they cite.
- `${CLAUDE_PLUGIN_ROOT}/references/composed/tone-and-stance.md` — voice
  for any composed references produced.

## Mode selection

Ask the user which mode at the start (unless the phrase makes it
unambiguous). Then run only that mode.

- **INGEST**: a new source needs to become kernels.
- **COMPOSE**: existing kernels need to land in composed references (or
  surface as a new composed reference / skill candidate).
- **AUDIT**: spot-check the knowledge base for drift.

## Mode 1: INGEST

1. **Identify source.** Confirm the local path with the user. Usually under
   `resources/` (gitignored). If the file isn't there, stop and ask.
2. **Allocate id.** Open `references/sources/INDEX.md`, find the next
   `sNNN` integer, draft the new row (title, creator, type, today's
   ISO date, local-path, provenance: raw).
3. **Read the source.** Scan for atomic claims. Each candidate kernel must:
   - have one load-bearing claim,
   - be attributable to a specific moment (note the timestamp or section),
   - be either net-new or a meaningful refinement of an existing kernel.
4. **Dedup check.** Before drafting any kernel, grep
   `references/kernels/` for the concept. Try both the candidate kernel
   filename (kebab-cased concept name, e.g. `charm-pricing`) AND a
   distinctive keyword from the claim. If a near-duplicate exists:
   - surface to the user: "kernel `<name>` already exists, here's its body:
     <quote>. Append this source as a new `sources:` entry instead?"
   - if user agrees: append a new `sources:` row to that kernel (id and a
     `note:` describing what this source adds). Do not modify the body
     unless the new source genuinely refines the claim — in which case
     propose the body edit explicitly.
5. **Draft new kernels.** For each net-new claim, draft a kernel per the
   anatomy in the architecture spec
   (`docs/superpowers/specs/2026-05-18-knowledge-architecture-design.md`).
   Frontmatter fields: name, concept, domain, applies-when, conflicts-with
   (if any), sources, status (active), distilled (false for raw sources),
   superseded-by (null). The kernel body must open with `**The claim:**`
   followed by the one-sentence claim — this convention is load-bearing
   for COMPOSE and AUDIT.
6. **Domain assignment.** If the kernel's domain folder doesn't exist under
   `references/kernels/`, ask before creating.
7. **Surface conflicts.** For every drafted kernel whose claim contradicts
   an existing kernel, set `conflicts-with: [[existing-kernel]]` and write
   the resolution in the kernel body (which band/context does each apply
   in?). If you can't resolve, flag it to the user and ask.
8. **Show the draft batch.** Present every drafted kernel and the proposed
   INDEX.md row to the user as a single review. Do not write until
   approved.
9. **Write on approval.** Once approved, write the INDEX.md row and the
   kernel files. Do not stage or commit — leave that to the user.

## Mode 2: COMPOSE

1. **Identify the input.** User points at a kernel set, a domain, or a
   newly-ingested source.
2. **Map to existing composed references.** Grep `references/composed/`
   for the kernel names. List which composed references already cite each
   kernel and which kernels are orphan.
3. **Propose edits to existing composed references.** For each composed
   reference that should cite a kernel but doesn't, draft an edit:
   - replace inline duplication with `[[kernel]]` wikilink
   - add a new framing sentence around the kernel if it's a net-new claim
   - preserve the composed reference's narrative voice
4. **Propose new composed references when needed.** If a cluster of 3+
   load-bearing kernels has no natural home in any existing composed
   reference, propose a new composed reference (in `references/composed/`)
   with name, stub outline, and rationale. Lean toward expanding an
   existing reference until 3+ kernels accumulate.
5. **Propose new skill only with strong justification.** A new skill is
   only correct when the kernels share a stage trigger, have collective
   refuses/postconditions, and form a workflow distinct from existing
   skills. Otherwise: composed reference.
6. **Show the diff batch.** Present every proposed edit / new file to the
   user as a unified review. Do not write until approved.
7. **Write on approval.** Apply edits. Do not stage or commit.

## Mode 3: AUDIT

Produce a single report with these sections. Don't fix anything in this
mode — only surface.

1. **Orphan kernels.** Kernels under `references/kernels/` that no
   composed reference cites. (Grep each kernel name across
   `references/composed/`; report names with zero matches.)
2. **Orphan composed references.** Composed references that cite zero
   kernels. (May be legitimate for narrative-only references like
   `tone-and-stance.md`; flag for human judgment.)
3. **Unresolved conflicts.** Kernels with `conflicts-with:` populated
   whose body does not contain the keyword `Resolution:` — these declared
   a conflict without resolving it. The `Resolution:` keyword is a
   required convention for any kernel with a non-empty `conflicts-with:`
   field (see kernel anatomy in the architecture spec).
4. **Stale source links.** Rows in `INDEX.md` whose `local-path:` is
   under `resources/` but the file no longer exists on disk.
5. **Domain mismatches.** Kernels whose `domain:` frontmatter field
   does not equal their parent subfolder name.
6. **Graph defects.** Skill files whose routing frontmatter
   (`stages`, `topics`, `preconditions`, `postconditions`, `successors`,
   `predecessors`) is missing required fields, references a stage name
   outside the controlled vocabulary, or names a successor/predecessor
   skill that doesn't exist. Additionally, flag any skill not under
   `skills/_meta/` that uses `any` as a stage value — the `any` sentinel
   is reserved for `_meta/` system skills only.
7. **Skill-graph snapshot.** Auto-generate a human-readable summary of
   the current skill graph (which skill points at which) to the chat —
   do not commit it to the repo.

## Hard rules (apply in all modes)

- **Never delete a kernel.** Deprecate via `status: deprecated,
  superseded-by: [[new-kernel]]`. The deprecated kernel stays in place.
- **Never edit a source row in INDEX.md.** Append-only.
- **Never auto-merge kernels** even if their bodies look identical.
  Surface to user; let them confirm.
- **Never write without approval.** Always present the diff first.
- **Never stage or commit.** Leave version control to the user.
- **Canonical-distilled extraction** sets `provenance: canonical-distilled`
  on the source row (`cNNN`) and `distilled: true` on the kernel
  frontmatter. This flags the kernel for potential supersession if the
  raw canonical source is ever ingested later.

## Outputs by mode

- INGEST: new rows in `references/sources/INDEX.md`; new files in
  `references/kernels/<domain>/`; possibly minor updates to existing
  kernels (appended source rows).
- COMPOSE: edits to `references/composed/<file>.md`; possibly new files in
  `references/composed/`.
- AUDIT: a chat-only report; no file changes.

## Tone

Per `${CLAUDE_PLUGIN_ROOT}/references/composed/tone-and-stance.md`. Direct
about conflicts and orphans; refuse to auto-resolve ambiguity. Pipeline
discipline is the system's safeguard against drift — the cost of pausing
to ask is small, the cost of silently merging incompatible kernels is
permanent corruption of the knowledge base.
