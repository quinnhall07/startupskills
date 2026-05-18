# Knowledge Architecture — Foundation Implementation Plan (Plan 1 of N)

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Lay the structural foundation for the knowledge architecture: gitignore the local resources/, create the new directory skeleton (composed/, kernels/, sources/, templates/, _meta/), move all existing references and the orientation skill to their new locations, update all reference paths in SKILL.md files, scaffold the sources index, and build the knowledge-pipeline skill that drives ingestion.

**Architecture:** Pure file relocation + new directory creation + path find-replace + one new skill authored. No new code; markdown only. After this plan lands, the system works identically to before — but the architecture is in place for subsequent plans (routing-graph backfill, canonical distillation, raw ingestion, new skills).

**Tech Stack:** Markdown files, git, .gitignore. PowerShell on Windows; commands shown work cross-platform where possible.

**Spec:** `docs/superpowers/specs/2026-05-18-knowledge-architecture-design.md`

---

## File Structure

**Created directories** (each gets `.gitkeep` if empty after this plan):
- `plugins/startup-skills/references/composed/`
- `plugins/startup-skills/references/kernels/`
- `plugins/startup-skills/references/sources/`
- `plugins/startup-skills/references/templates/`
- `plugins/startup-skills/skills/_meta/`
- `plugins/startup-skills/skills/_meta/knowledge-pipeline/`

**Moved files** (23 references + 1 skill):
- Into `references/templates/`: `state-document-template.md`, `email-templates.md`, `pitch-deck-structure.md`, `one-pager-structure.md`
- Into `references/composed/`: the remaining 19 references
- Into `skills/_meta/`: `orientation/` skill directory

**Created files:**
- `plugins/startup-skills/references/sources/INDEX.md`
- `plugins/startup-skills/skills/_meta/knowledge-pipeline/SKILL.md`

**Modified files:**
- `.gitignore` (one line added)
- All 16 existing SKILL.md files (reference paths find-replaced — `orientation/SKILL.md` also touched even though it moves)
- `plugins/startup-skills/references/templates/state-document-template.md` (two new sections added)

**Out of scope for this plan:**
- Routing frontmatter on the 16 existing skills (Plan 2)
- Canonical kernel distillation (Plan 3)
- Per-transcript raw ingestion (Plan 4)
- New skill creation: fundraising-readiness, growth-funnel, scaling-ops (Plan 5)

---

## Task 1: Restructure foundation — gitignore, directory skeleton, file moves, path find-replace, sources index

This task is a single atomic restructure. All steps run together; one commit at the end. Steps are split for verifiability.

**Files:**
- Modify: `.gitignore`
- Create directories listed in File Structure
- Move: 23 reference files
- Move: `plugins/startup-skills/skills/orientation/` directory
- Modify: all 16 files matching `plugins/startup-skills/skills/*/SKILL.md` (path find-replace)
- Create: `plugins/startup-skills/references/sources/INDEX.md`

- [ ] **Step 1: Add `resources/` to .gitignore**

Open `.gitignore` and append one line. The file currently contains:
```
.claude/settings.local.json
.claude/startup-state.md
*.DS_Store
Thumbs.db
```

Append:
```
resources/
```

Verify with:
```powershell
Get-Content .gitignore
```
Expected: 5 lines, last one is `resources/`.

- [ ] **Step 2: Verify the gitignore actually ignores the resources folder**

```powershell
git check-ignore -v resources/
```
Expected output includes `.gitignore:5:resources/	resources/` (or similar — confirms the rule matches).

If the resources folder is currently tracked (it shouldn't be — at session start it appeared as untracked `?? resources/`), run:
```powershell
git status resources/
```
Expected: empty output (the folder is ignored and untracked).

- [ ] **Step 3: Create the new directory skeleton**

Create the following directories. Add a `.gitkeep` file inside each empty directory so git tracks it.

```powershell
New-Item -ItemType Directory -Path "plugins/startup-skills/references/composed" -Force | Out-Null
New-Item -ItemType Directory -Path "plugins/startup-skills/references/kernels" -Force | Out-Null
New-Item -ItemType Directory -Path "plugins/startup-skills/references/sources" -Force | Out-Null
New-Item -ItemType Directory -Path "plugins/startup-skills/references/templates" -Force | Out-Null
New-Item -ItemType Directory -Path "plugins/startup-skills/skills/_meta" -Force | Out-Null
```

The `kernels/` directory will be empty at this stage. Add a `.gitkeep`:
```powershell
New-Item -ItemType File -Path "plugins/startup-skills/references/kernels/.gitkeep" -Force | Out-Null
```

Other new directories will be populated by subsequent steps so they don't need `.gitkeep`.

Verify:
```powershell
Get-ChildItem "plugins/startup-skills/references" -Directory
```
Expected: `composed`, `kernels`, `sources`, `templates` all listed.

```powershell
Get-ChildItem "plugins/startup-skills/skills" -Directory
```
Expected: `_meta` is among the listed directories.

- [ ] **Step 4: Move the four template-type references into `references/templates/`**

These are the references whose primary content is a skeleton/template used verbatim. Use `git mv` (preserves history):

```powershell
git mv "plugins/startup-skills/references/state-document-template.md" "plugins/startup-skills/references/templates/state-document-template.md"
git mv "plugins/startup-skills/references/email-templates.md" "plugins/startup-skills/references/templates/email-templates.md"
git mv "plugins/startup-skills/references/pitch-deck-structure.md" "plugins/startup-skills/references/templates/pitch-deck-structure.md"
git mv "plugins/startup-skills/references/one-pager-structure.md" "plugins/startup-skills/references/templates/one-pager-structure.md"
```

Verify:
```powershell
Get-ChildItem "plugins/startup-skills/references/templates"
```
Expected: 4 .md files listed.

- [ ] **Step 5: Move the 19 composed-type references into `references/composed/`**

```powershell
git mv "plugins/startup-skills/references/bias-sentinel.md" "plugins/startup-skills/references/composed/bias-sentinel.md"
git mv "plugins/startup-skills/references/case-studies.md" "plugins/startup-skills/references/composed/case-studies.md"
git mv "plugins/startup-skills/references/cofounder-frameworks.md" "plugins/startup-skills/references/composed/cofounder-frameworks.md"
git mv "plugins/startup-skills/references/evidence-weighting-matrix.md" "plugins/startup-skills/references/composed/evidence-weighting-matrix.md"
git mv "plugins/startup-skills/references/external-resources.md" "plugins/startup-skills/references/composed/external-resources.md"
git mv "plugins/startup-skills/references/false-signal-detection.md" "plugins/startup-skills/references/composed/false-signal-detection.md"
git mv "plugins/startup-skills/references/landing-page-patterns.md" "plugins/startup-skills/references/composed/landing-page-patterns.md"
git mv "plugins/startup-skills/references/mom-test-principles.md" "plugins/startup-skills/references/composed/mom-test-principles.md"
git mv "plugins/startup-skills/references/mvp-examples.md" "plugins/startup-skills/references/composed/mvp-examples.md"
git mv "plugins/startup-skills/references/pmf-scoring.md" "plugins/startup-skills/references/composed/pmf-scoring.md"
git mv "plugins/startup-skills/references/pricing-frameworks.md" "plugins/startup-skills/references/composed/pricing-frameworks.md"
git mv "plugins/startup-skills/references/research-playbook.md" "plugins/startup-skills/references/composed/research-playbook.md"
git mv "plugins/startup-skills/references/sales-funnel-math.md" "plugins/startup-skills/references/composed/sales-funnel-math.md"
git mv "plugins/startup-skills/references/scoring-rubrics.md" "plugins/startup-skills/references/composed/scoring-rubrics.md"
git mv "plugins/startup-skills/references/state-document-protocol.md" "plugins/startup-skills/references/composed/state-document-protocol.md"
git mv "plugins/startup-skills/references/tar-pit-detection.md" "plugins/startup-skills/references/composed/tar-pit-detection.md"
git mv "plugins/startup-skills/references/tone-and-stance.md" "plugins/startup-skills/references/composed/tone-and-stance.md"
git mv "plugins/startup-skills/references/tool-recommendations.md" "plugins/startup-skills/references/composed/tool-recommendations.md"
git mv "plugins/startup-skills/references/validation-techniques.md" "plugins/startup-skills/references/composed/validation-techniques.md"
```

Verify:
```powershell
Get-ChildItem "plugins/startup-skills/references/composed" | Measure-Object
```
Expected: Count = 19.

```powershell
Get-ChildItem "plugins/startup-skills/references" -File
```
Expected: empty (all .md files moved; only subdirectories remain at this level).

- [ ] **Step 6: Move the orientation skill into `skills/_meta/`**

```powershell
git mv "plugins/startup-skills/skills/orientation" "plugins/startup-skills/skills/_meta/orientation"
```

Verify:
```powershell
Get-ChildItem "plugins/startup-skills/skills/_meta"
```
Expected: `orientation` directory listed.

```powershell
Test-Path "plugins/startup-skills/skills/orientation"
```
Expected: `False`.

- [ ] **Step 7: Find-replace the four template paths across all SKILL.md files**

The Edit tool's `replace_all: true` works per-file. Use it on each of these strings, across every SKILL.md in `plugins/startup-skills/skills/`.

For each SKILL.md file (the engineer can use Grep to find which files contain these strings first to avoid wasted edits):

Find: `${CLAUDE_PLUGIN_ROOT}/references/state-document-template.md`
Replace: `${CLAUDE_PLUGIN_ROOT}/references/templates/state-document-template.md`

Find: `${CLAUDE_PLUGIN_ROOT}/references/email-templates.md`
Replace: `${CLAUDE_PLUGIN_ROOT}/references/templates/email-templates.md`

Find: `${CLAUDE_PLUGIN_ROOT}/references/pitch-deck-structure.md`
Replace: `${CLAUDE_PLUGIN_ROOT}/references/templates/pitch-deck-structure.md`

Find: `${CLAUDE_PLUGIN_ROOT}/references/one-pager-structure.md`
Replace: `${CLAUDE_PLUGIN_ROOT}/references/templates/one-pager-structure.md`

To find which files contain each pattern, use Grep:
```
Grep pattern: "references/state-document-template.md" glob: "**/SKILL.md"
```

Repeat the Grep for each template filename; then for each match, apply the Edit with `replace_all: true`.

- [ ] **Step 8: Find-replace all remaining `${CLAUDE_PLUGIN_ROOT}/references/<file>.md` patterns to `${CLAUDE_PLUGIN_ROOT}/references/composed/<file>.md`**

After Step 7, every remaining `${CLAUDE_PLUGIN_ROOT}/references/<X>.md` path (where X is not under a subdirectory) refers to a composed reference.

Use Grep to enumerate every SKILL.md that still contains `${CLAUDE_PLUGIN_ROOT}/references/` followed by a filename (not a subdirectory). For each such file, edit each occurrence individually, changing:

`${CLAUDE_PLUGIN_ROOT}/references/<filename>.md` → `${CLAUDE_PLUGIN_ROOT}/references/composed/<filename>.md`

The 19 composed reference filenames to look for:
`bias-sentinel.md`, `case-studies.md`, `cofounder-frameworks.md`, `evidence-weighting-matrix.md`, `external-resources.md`, `false-signal-detection.md`, `landing-page-patterns.md`, `mom-test-principles.md`, `mvp-examples.md`, `pmf-scoring.md`, `pricing-frameworks.md`, `research-playbook.md`, `sales-funnel-math.md`, `scoring-rubrics.md`, `state-document-protocol.md`, `tar-pit-detection.md`, `tone-and-stance.md`, `tool-recommendations.md`, `validation-techniques.md`.

For each filename, run Grep with pattern `references/<filename>` glob `**/SKILL.md`, then for each matching file use Edit with `replace_all: true`:
- old_string: `${CLAUDE_PLUGIN_ROOT}/references/<filename>.md`
- new_string: `${CLAUDE_PLUGIN_ROOT}/references/composed/<filename>.md`

- [ ] **Step 9: Verify no stale paths remain**

```
Grep pattern: "\$\{CLAUDE_PLUGIN_ROOT\}/references/[a-z][a-z-]*\.md" glob: "**/SKILL.md" output_mode: content
```

Expected output: empty (all matches should now have either `/composed/` or `/templates/` between `references/` and the filename).

If anything matches, it's a stale path that was missed; fix the corresponding file.

Also verify the new paths exist on disk by spot-checking one composed and one template path:
```powershell
Test-Path "plugins/startup-skills/references/composed/pricing-frameworks.md"
Test-Path "plugins/startup-skills/references/templates/email-templates.md"
```
Expected: both `True`.

- [ ] **Step 10: Create the sources index**

Create `plugins/startup-skills/references/sources/INDEX.md` with the following content (single table, the 5 raw sources + initial canonical rows derived from `external-resources.md` cited authorities):

```markdown
# Sources Index

Append-only. One row per ingested source. `provenance: raw` = transcript in
gitignored local `resources/`. `provenance: canonical-distilled` = source we
don't have raw access to; kernels distilled from existing v0.4 composed
references with `distilled: true`.

To find what came from a source: grep for `id: sNNN` (or `id: cNNN`) under
`plugins/startup-skills/references/kernels/`.

To add a row: append at the bottom. Never edit existing rows. Never delete.

## Raw sources (transcripts in gitignored `resources/`)

| id | title | creator | type | ingested | local-path | provenance |
|---|---|---|---|---|---|---|
| s001 | How to charge more (the psychology of pricing) | Brian Lange / Future Commerce | youtube | 2026-05-18 | resources/How to charge more (the psychology of pricing).txt | raw |
| s002 | 6 EASY Design Tips to Make Any Site Look 10x Better | (creator TBD from transcript) | youtube | 2026-05-18 | resources/6 EASY Design Tips to Make Any Site Look 10x Better.txt | raw |
| s003 | How to Actually Make $1M | Micaia Rson | youtube | 2026-05-18 | resources/How to Actually Make $1M.txt | raw |
| s004 | How to Raise Startup Funding EVERYTHING You Need to Know | Slidebean | youtube | 2026-05-18 | resources/How to Raise Startup Funding EVERYTHING You Need to Know.txt | raw |
| s005 | The Most Valuable Ecommerce Funnel Training You'll Ever Watch | Max | youtube | 2026-05-18 | resources/The Most Valuable Ecommerce Funnel Training You'll Ever Watch.txt | raw |

## Canonical sources (raw not in repo; kernels distilled from v0.4 composed references)

| id | title | creator | type | ingested | local-path | provenance |
|---|---|---|---|---|---|---|
| c001 | The Mom Test | Rob Fitzpatrick | book | 2026-05-18 | — | canonical-distilled |
| c002 | The Lean Startup | Eric Ries | book | 2026-05-18 | — | canonical-distilled |
| c003 | The Four Steps to the Epiphany | Steve Blank | book | 2026-05-18 | — | canonical-distilled |
| c004 | YC Startup School | Graham, Seibel, Caldwell, Alstromer, Friedman, Manalac, Bhat | video-series | 2026-05-18 | — | canonical-distilled |
| c005 | Sean Ellis 40% Rule | Sean Ellis | framework | 2026-05-18 | — | canonical-distilled |
| c006 | Superhuman PMF Engine | Rahul Vohra | article | 2026-05-18 | — | canonical-distilled |
| c007 | Pre-Mortem | Gary Klein | framework | 2026-05-18 | — | canonical-distilled |
| c008 | Thinking, Fast and Slow / inside-outside view | Daniel Kahneman, Amos Tversky, Dan Lovallo | book / paper | 2026-05-18 | — | canonical-distilled |
| c009 | Lenny's Newsletter (GTM, first customers, pricing) | Lenny Rachitsky | newsletter | 2026-05-18 | — | canonical-distilled |
| c010 | Marc Andreessen on PMF | Marc Andreessen | blog | 2026-05-18 | — | canonical-distilled |
```

Verify file content:
```powershell
Get-Content "plugins/startup-skills/references/sources/INDEX.md" -TotalCount 5
```
Expected: first five lines match the header above.

- [ ] **Step 11: Commit the restructure**

```powershell
git status
```
Expected: see modifications/renames for .gitignore, all 23 reference moves, orientation skill move, 16 SKILL.md edits (path find-replaces), and the new INDEX.md.

```powershell
git add .gitignore
git add plugins/startup-skills/references/
git add plugins/startup-skills/skills/
git commit -m @'
restructure references into composed/templates/kernels/sources; move orientation to _meta

Phase 1 of knowledge-architecture spec (docs/superpowers/specs/2026-05-18-knowledge-architecture-design.md).
No semantic changes: all reference paths in SKILL.md updated to new locations;
sources INDEX.md scaffolded; gitignore now excludes resources/.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
'@
```

```powershell
git log -1 --stat
```
Expected: commit summary showing the rename + edits.

---

## Task 2: Add Inferred Stage and Session Pointers sections to state-document-template

Two new sections in the canonical state document template, supporting the skill graph routing.

**Files:**
- Modify: `plugins/startup-skills/references/templates/state-document-template.md`

- [ ] **Step 1: Edit the state document template to add the two new sections**

Open `plugins/startup-skills/references/templates/state-document-template.md`. Find the existing line near the start of the template block:

```
_Schema version: 1.0_
```

Change it to:

```
_Schema version: 1.1_
```

Then find the existing `## Session Log` section near the end of the template block:

```
## Session Log
- [date] [skill] — [one-sentence summary, decisions made, evidence added]
```

Replace that block with the following (which keeps Session Log and adds two new sections immediately before it):

```
## Inferred Stage

(Auto-derived hint. Written by skills as they complete; read by orientation
to filter candidate skills. One of: idea-genesis, problem-validation,
discovery, mvp-scoping, first-customers, pmf-measurement, at-pmf-threshold,
ready-to-scale, growth, scaling, fundraising-prep, pivot-evaluation.)

Current: [unset until first skill writes]
History:
- [date] [skill] — set to [stage] because [reason]

## Session Pointers

(Forward-looking nudges written by skills when they finish. Orientation
reads these first when selecting the next skill. Append-only within a
window of 5; older pointers age out as new ones land.)

- (none yet)

## Session Log
- [date] [skill] — [one-sentence summary, decisions made, evidence added]
```

- [ ] **Step 2: Verify the edit applied cleanly**

```powershell
Select-String -Path "plugins/startup-skills/references/templates/state-document-template.md" -Pattern "Inferred Stage|Session Pointers|Schema version"
```
Expected: three matches — `_Schema version: 1.1_`, `## Inferred Stage`, `## Session Pointers`.

- [ ] **Step 3: Commit the template change**

```powershell
git add plugins/startup-skills/references/templates/state-document-template.md
git commit -m @'
add Inferred Stage and Session Pointers sections to state-document-template

Supports the skill-graph routing layer of the knowledge-architecture spec.
Schema version bumped 1.0 -> 1.1. New sections are read by orientation
and written by every skill on completion.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
'@
```

---

## Task 3: Write the knowledge-pipeline skill

Author the skill that drives kernel extraction, composition, and audit.

**Files:**
- Create: `plugins/startup-skills/skills/_meta/knowledge-pipeline/SKILL.md`

- [ ] **Step 1: Create the skill file**

Create `plugins/startup-skills/skills/_meta/knowledge-pipeline/SKILL.md` with the following content:

````markdown
---
name: knowledge-pipeline
description: >
  (startup-skills, _meta) Use to ingest a new source (transcript, article)
  into the kernel layer, compose new or updated composed references from
  existing kernels, or audit the kernel graph for orphans, conflicts, and
  stale source links. Fires on "ingest this source," "extract kernels from
  X," "compose a reference from these kernels," "audit the knowledge base,"
  or "process the new transcripts."
stages: [any]
topics: [knowledge-management, system-maintenance]
preconditions:
  - source file path provided (for INGEST mode) OR
  - kernel set or domain specified (for COMPOSE mode) OR
  - no preconditions (for AUDIT mode)
postconditions:
  - references/sources/INDEX.md appended (INGEST mode) and
  - references/kernels/<domain>/ files created or updated (INGEST mode) OR
  - references/composed/ files updated to cite kernels via wikilinks (COMPOSE mode) OR
  - human-readable audit report produced (AUDIT mode)
successors:
  - knowledge-pipeline COMPOSE (after INGEST, to wire new kernels into composed references)
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
   `references/kernels/` for the concept (try both the short name and a
   keyword from the claim). If a near-duplicate exists:
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
   superseded-by (null).
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
4. **Propose new composed references when needed.** If a cluster of 3+ load-
   bearing kernels has no natural home in any existing composed reference,
   propose a new composed reference (in `references/composed/`) with name,
   stub outline, and rationale. Lean toward expanding an existing reference
   until 3+ kernels accumulate.
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
   whose body does not contain the string "Resolution:" (or equivalent
   framing) — these declared a conflict without resolving it.
4. **Stale source links.** Rows in `INDEX.md` whose `local-path:` is
   under `resources/` but the file no longer exists on disk.
5. **Domain mismatches.** Kernels whose `domain:` frontmatter field
   does not equal their parent subfolder name.
6. **Graph defects.** Skill files whose routing frontmatter
   (`stages`, `topics`, `preconditions`, `postconditions`, `successors`,
   `predecessors`) is missing required fields, references a stage name
   outside the controlled vocabulary, or names a successor/predecessor
   skill that doesn't exist.
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
````

- [ ] **Step 2: Verify the file**

```powershell
Test-Path "plugins/startup-skills/skills/_meta/knowledge-pipeline/SKILL.md"
```
Expected: `True`.

```powershell
Select-String -Path "plugins/startup-skills/skills/_meta/knowledge-pipeline/SKILL.md" -Pattern "^## Mode" | Measure-Object
```
Expected: Count = 3 (three mode sections).

- [ ] **Step 3: Commit the skill**

```powershell
git add plugins/startup-skills/skills/_meta/knowledge-pipeline/SKILL.md
git commit -m @'
add knowledge-pipeline skill (INGEST, COMPOSE, AUDIT modes)

Implements Phase 2 of the knowledge-architecture spec. Skill drives
ingestion of new sources into kernels, composition of kernels into
composed references, and periodic audit for orphans / conflicts /
stale links. All three modes require user approval before writing.

Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>
'@
```

---

## Task 4: Smoke-test the knowledge-pipeline skill

Verify INGEST mode end-to-end on a tiny synthetic snippet. Throwaway: results get reverted at the end.

**Files:**
- Create (temporary): `resources/_smoke-test-snippet.txt`
- Created and then deleted by the test: `plugins/startup-skills/references/kernels/_smoke/charm-pricing-test.md`
- Modified and then reverted: `plugins/startup-skills/references/sources/INDEX.md`

- [ ] **Step 1: Create the synthetic test snippet**

```powershell
New-Item -ItemType File -Path "resources/_smoke-test-snippet.txt" -Force | Out-Null
Set-Content -Path "resources/_smoke-test-snippet.txt" -Encoding utf8 -Value @'
Prices ending in 9 — $9.99, $59, $69 — consistently drive higher volume
in mass-market and value categories. The 1-cent difference between $9.99
and $10 is psychologically much larger than it looks. In premium categories
the opposite is true: rounded prices like $10 and $100 signal that the
seller isn't trying to manipulate the buyer.
'@
```

Verify:
```powershell
Test-Path "resources/_smoke-test-snippet.txt"
git check-ignore -v "resources/_smoke-test-snippet.txt"
```
Expected: file exists and is gitignored.

- [ ] **Step 2: Invoke the knowledge-pipeline skill in INGEST mode**

In a Claude Code session, type:
```
/skill knowledge-pipeline ingest resources/_smoke-test-snippet.txt
```

Or describe naturally: "Ingest the smoke-test snippet at resources/_smoke-test-snippet.txt using knowledge-pipeline."

Expected pipeline behavior:
1. Reads the snippet.
2. Proposes a new source row (will be `s006` since s001-s005 already exist) with title like "Smoke test snippet" or similar, creator "(test)", type "snippet", provenance "raw".
3. Identifies one candidate kernel: something like `charm-pricing` or `charm-vs-rounded-pricing`.
4. Performs dedup check; finds no existing `charm-pricing` kernel (none have been ingested yet); proposes net-new.
5. Asks whether to create `references/kernels/pricing/` subdirectory (yes for the test).
6. Presents the drafted kernel body and INDEX.md row for approval.

Approve the proposal. Verify files are written.

- [ ] **Step 3: Verify the test files were created correctly**

```powershell
Test-Path "plugins/startup-skills/references/kernels/pricing"
Get-ChildItem "plugins/startup-skills/references/kernels/pricing"
```
Expected: the pricing/ directory exists with at least one kernel file.

```powershell
Select-String -Path "plugins/startup-skills/references/sources/INDEX.md" -Pattern "s006"
```
Expected: one row matching s006.

Open the new kernel file and verify it has the expected frontmatter fields (`name`, `concept`, `domain: pricing`, `applies-when`, `sources` referencing s006, `status: active`, `distilled: false`). Body should lead with `**The claim:**`.

- [ ] **Step 4: Re-run INGEST on the same snippet to verify dedup behavior**

```
/skill knowledge-pipeline ingest resources/_smoke-test-snippet.txt
```

Expected pipeline behavior:
- Detects that source `s006` already exists for that local path; either reuses or surfaces "source already ingested as s006, proceed anyway?"
- For the candidate kernel: detects that a charm-pricing kernel already exists; proposes appending a new source-row to it (with a different note) rather than creating a new file.

Decline the proposal (this is just verifying the behavior surfaces correctly).

- [ ] **Step 5: Clean up test artifacts**

```powershell
Remove-Item "resources/_smoke-test-snippet.txt"
Remove-Item -Recurse -Force "plugins/startup-skills/references/kernels/pricing"
```

Then revert the INDEX.md change. If the only change to INDEX.md was the added s006 row:
```powershell
git checkout -- plugins/startup-skills/references/sources/INDEX.md
```

Verify clean state:
```powershell
git status plugins/startup-skills/references/
```
Expected: clean (no modifications, no untracked files in references/).

If the pipeline also created the kernels/pricing/ directory and you removed it above, also re-add the kernels root `.gitkeep`:
```powershell
Test-Path "plugins/startup-skills/references/kernels/.gitkeep"
```
Expected: `True` (it should still be there from Task 1; if not, recreate it).

- [ ] **Step 6: Record the smoke-test result**

No commit. The smoke test is verification only.

If any step failed, the issue is in the knowledge-pipeline SKILL.md from Task 3 — return to Task 3 step 1, fix the SKILL.md content, re-commit (new commit, not amend), then re-run Task 4.

---

## Self-review

Inline check after writing this plan:

**Spec coverage:**
- gitignore for resources/ → Task 1, Step 1 ✓
- New directory skeleton (composed/, kernels/, sources/, templates/, _meta/) → Task 1, Step 3 ✓
- Move 23 references to composed/ + templates/ → Task 1, Steps 4-5 ✓
- Move orientation to _meta/ → Task 1, Step 6 ✓
- Find-replace reference paths → Task 1, Steps 7-9 ✓
- Sources INDEX.md scaffold → Task 1, Step 10 ✓
- State document template gains Inferred Stage and Session Pointers → Task 2 ✓
- knowledge-pipeline skill with INGEST/COMPOSE/AUDIT modes → Task 3 ✓
- Smoke test → Task 4 ✓
- Routing frontmatter on existing 16 skills → **explicitly out of scope; Plan 2** ✓
- Canonical distillation → **explicitly out of scope; Plan 3** ✓
- Per-transcript raw ingest → **explicitly out of scope; Plan 4** ✓
- New skills (fundraising-readiness etc.) → **explicitly out of scope; Plan 5** ✓

**Placeholder scan:**
- One instance of `(creator TBD from transcript)` in the INDEX.md content (Step 10) — accurate placeholder because the design transcript truly does not identify the creator; this is data, not a plan defect.
- All other steps have concrete commands, expected outputs, and content.

**Type consistency:**
- File path conventions consistent throughout (forward slashes; full paths from repo root).
- `references/composed/`, `references/templates/`, `references/kernels/`, `references/sources/` used consistently.
- Frontmatter field names (`name`, `concept`, `domain`, `applies-when`, `conflicts-with`, `sources`, `status`, `distilled`, `superseded-by`) match the spec.

Plan is complete and self-consistent. Ready for execution.
