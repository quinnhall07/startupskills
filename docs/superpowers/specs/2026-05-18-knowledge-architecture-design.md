# Startup Skills — Knowledge Architecture Design

**Date:** 2026-05-18
**Status:** approved, ready for implementation planning
**Scope:** repo-wide architecture for ingesting external knowledge sources, organizing skills/references, and routing users through the system based on state rather than navigation.

---

## Problem

The repo (v0.4) has 16 skills and 23 references organized flat. The maintainer plans to ingest external knowledge sources (transcripts, articles) aggressively — dozens per month. The current architecture has four scaling problems:

1. **No atomic concept layer.** When the same concept (e.g., charm pricing) appears in multiple sources, there's no canonical home for it; it either gets re-explained inline across references or omitted.
2. **No ingestion protocol.** New material would be ad-hoc appended to references, causing drift, duplication, and contradictory advice over time.
3. **No source attribution discipline for non-canonical sources.** Existing references cite Y Combinator, Mom Test, etc.; there's no convention for citing YouTube creators or operator content alongside.
4. **Folder-by-phase doesn't fit.** Many skills validly apply at multiple stages (pricing, artifact-building). Forcing a pre-PMF / post-PMF folder split would either duplicate skills or misclassify them. Routing should be driven by founder state, not by skill location.

## Constraints (from brainstorming)

1. **Pre-PMF stays the system's center of gravity** — but post-PMF material is welcome as the system expands.
2. **Aggressive ingestion velocity** (dozens of sources per month over the next year).
3. **Flat citation** — no source-authority tiers. A YouTube creator and a YC partner get cited the same way.
4. **Raw transcripts gitignored locally.** A committed sources index tracks provenance; raw files live only on the maintainer's machine.
5. **Original v0.4 references were built from canonical sources** (YC videos, Mom Test, Lean Startup, etc.) that we do not have raw transcripts for. Migration must distill from existing reference text, with `provenance: canonical-distilled` and a `distilled: true` flag so future ingestions of the raw canonical source (if ever obtained) can supersede.

## Architecture

### Directory layout

```
plugins/startup-skills/
  skills/
    _meta/                          # system skills (orientation, knowledge-pipeline)
    <skill-name>/SKILL.md           # all other skills flat, no phase folders
  references/
    composed/                       # the existing 23 refs (renamed/moved) + new ones
    kernels/                        # atomic concepts, organized by domain subfolder
      pricing/
      discovery/
      fundraising/
      growth/
      ...
    sources/
      INDEX.md                      # append-only sources table
    templates/                      # narrative-free assets used verbatim by skills:
                                    # state-document-template, email-templates,
                                    # pitch-deck-structure, one-pager-structure,
                                    # landing-page-patterns (skeleton portions only).
                                    # Composed references and kernels live elsewhere.
resources/                          # gitignored: raw transcripts, local-only
.gitignore                          # adds resources/
```

The `composed/` vs `kernels/` distinction in `references/` is load-bearing:
- **Composed references** are pedagogically rich teaching documents. Skills load them via `${CLAUDE_PLUGIN_ROOT}/references/composed/<file>.md`.
- **Kernels** are atomic concepts (one claim per file, source-cited). Composed references cite kernels via `[[wikilink]]` rather than duplicating their content.
- Skills do not load kernels directly. Skills load composed references; composed references resolve through kernels.

**Kernel `domain:` field must equal the kernel's parent subfolder name** (e.g., a kernel in `references/kernels/pricing/` must have `domain: pricing`). The AUDIT mode flags violations.

### Stage vocabulary

Stage names referenced in skill frontmatter and STARTUP-STATE.md must come from this controlled vocabulary. Extending it requires a documented change:

- `idea-genesis` — no concrete idea yet, exploring spaces
- `problem-validation` — has hypothesis, pressure-testing and ICP-sharpening
- `discovery` — running customer conversations
- `mvp-scoping` — designing the smallest thing to build
- `first-customers` — recruiting initial paying users
- `pmf-measurement` — measuring against the 40% rule
- `at-pmf-threshold` — straddling the PMF line, polarity analysis
- `ready-to-scale` — PMF confirmed, deciding next bottleneck
- `growth` — scaling acquisition/retention systematically
- `scaling` — scaling org and operations
- `fundraising-prep` — preparing to raise
- `pivot-evaluation` — considering whether/how to pivot

Skills may list multiple stages. STARTUP-STATE.md's `# Inferred Stage` resolves to exactly one at any time.

### Kernel anatomy

Each kernel is `references/kernels/<domain>/<kernel-name>.md`:

```markdown
---
name: charm-pricing
concept: prices ending in .99 lift volume in mass/value categories; rounded prices signal premium
domain: pricing
applies-when: setting a price point for a B2C or SMB product
conflicts-with: [[price-as-quality-signal-band]]
sources:
  - id: s001
    note: "primary, ~2:30 mark on charm pricing"
status: active                      # active | deprecated
distilled: false                    # true if extracted from existing v0.4 reference,
                                    # not the raw canonical source
superseded-by: null                 # populated when deprecated
---

# Charm pricing

**The claim:** Prices ending in 9 ($9.99, $59, $69) consistently drive higher
volume in mass and value categories. The 1¢ difference is psychologically
much larger than it looks.

**Where it applies:** B2C, SMB SaaS, e-commerce, anything price-sensitive
in the mass/value band.

**Where it inverts:** Premium and luxury categories — rounded numbers ($10,
$100) signal "we're not trying to trick you." Apple, Hermès, Aesop use
rounded pricing.

**Lost price points:** Once you cross a psychological threshold ($79 → $99),
you might as well go all the way. $69 is "much more than $50, looking for $49."

**Conflict with [[price-as-quality-signal-band]]:** Charm pricing maximizes
volume at known low-end positioning. Price-as-quality-signal says underpricing
destroys perceived value. Resolution: charm pricing is correct *within* a
mass-market band; if the product's positioning is premium, the framework
changes entirely. Pick the band first.
```

**Frontmatter field rationale:**
- `concept:` — one-line summary scannable by composer skills without loading body.
- `applies-when:` — explicit context, defends against "true but in wrong situation" misuse.
- `conflicts-with:` — declares contradictions explicitly; resolution lives in the kernel body.
- `sources:` is a list — same concept may come from multiple sources over time. Pipeline appends rather than creates duplicates.
- `status` / `superseded-by:` — kernels are never deleted; deprecate and forward.
- `distilled:` — flags canonical-distilled kernels for potential future supersession when raw source becomes available.

**Body convention:** always starts with `**The claim:**` so composers can quote it reliably.

### Sources index

`references/sources/INDEX.md` — single table, append-only:

```markdown
| id | title | creator | type | ingested | local-path | provenance |
|---|---|---|---|---|---|---|
| s001 | How to charge more (the psychology of pricing) | Brian Lange | youtube | 2026-05-18 | resources/How to charge more...txt | raw |
| s002 | 6 EASY Design Tips to Make Any Site Look 10x Better | (TBD) | youtube | 2026-05-18 | resources/6 EASY Design Tips...txt | raw |
| s003 | How to Actually Make $1M | Micaia Rson | youtube | 2026-05-18 | resources/How to Actually Make $1M.txt | raw |
| s004 | How to Raise Startup Funding | Slidebean | youtube | 2026-05-18 | resources/How to Raise Startup Funding...txt | raw |
| s005 | The Most Valuable Ecommerce Funnel Training | Max | youtube | 2026-05-18 | resources/The Most Valuable Ecommerce Funnel...txt | raw |
| c001 | The Mom Test | Rob Fitzpatrick | book | 2026-05-18 | — | canonical-distilled |
| c002 | Sean Ellis 40% Rule | Sean Ellis | framework | 2026-05-18 | — | canonical-distilled |
| c003 | YC Startup School (Graham, Seibel, Caldwell, et al.) | Y Combinator | video-series | 2026-05-18 | — | canonical-distilled |
| ... | | | | | | |
```

Kernels reference sources by `id` only:

```yaml
sources:
  - id: s001
    note: "primary, ~2:30 mark on charm pricing"
```

This keeps source metadata in one place and kernels small. To find what came from a source: grep `id: sXXX` inside `references/kernels/`.

### Composed reference convention

Existing 23 references stay as pedagogically rich teaching documents. They evolve from inline duplication to wikilink composition:

**Before (current):**
```
1. **Charge from day one.** Pre-revenue users are not validation.
   Scribd grew users free for four years; when they started charging,
   they lost 90% of their user base...
```

**After (composed reference cites kernel):**
```
1. **Charge from day one.** See [[charge-from-day-one]]. Scribd's four-year
   free experiment is the canonical anti-pattern.
```

**Hard rule:** a composed reference must not re-explain a kernel inline. If the author is paraphrasing the kernel body, link to it instead. The composed reference's job is sequencing, framing, and synthesis — not duplication.

### The `knowledge-pipeline` skill

Lives at `plugins/startup-skills/skills/_meta/knowledge-pipeline/SKILL.md`. Three modes:

**Mode 1: INGEST (transcript → kernels)**
1. Read source file (path provided by user, usually in gitignored `resources/`).
2. Identify next `sNNN` id; append row to `sources/INDEX.md`.
3. Scan transcript for atomic claims. Each candidate kernel must: have a single load-bearing claim, be source-attributable to a specific moment, be net-new or a meaningful refinement of an existing kernel.
4. **Dedup check first.** Grep `references/kernels/` for the concept. If a near-duplicate exists, append the new source to that kernel's `sources:` list with a `note:` rather than create a new kernel. Surface to user: "kernel `X` already exists, appending source — agree?"
5. Write new kernels into `references/kernels/<domain>/`. If `<domain>` doesn't exist, ask before creating.
6. For each kernel, check `conflicts-with:` field — surface conflicts to user explicitly. Don't auto-resolve.
7. Show user the list of kernels created/updated; ask for review before any composition into references/skills.

**Mode 2: COMPOSE (kernels → composed reference or skill update)**
1. User points at a set of kernels or a domain.
2. Identify which composed references already cite (or should cite) those kernels.
3. Propose edits: replace inline re-explanation with `[[kernel]]` links; add new framing where a kernel is net-new.
4. If kernels don't fit any existing composed reference, propose either a new composed reference or a new skill. Lean toward composed reference until the topic has 3+ load-bearing kernels.
5. Never auto-write. Always present diff, ask for approval.

**Mode 3: AUDIT (sanity check)**
1. Orphan kernels (no composed reference links to them).
2. Orphan composed references (don't cite any kernels — should they?).
3. Unresolved `conflicts-with:` chains (kernels declare conflict but neither body addresses resolution).
4. Stale source links (`local-path:` no longer exists on disk).
5. **Graph consistency:** skills with missing or contradictory routing frontmatter; orphan entry-point skills (no preconditions, no predecessors); dead-end postconditions no skill consumes.
6. Report. User decides what to fix.

**Hard rules in the skill:**
- Never delete a kernel; deprecate via `status: deprecated, superseded-by: [[new-kernel]]`.
- Never edit a source row in `INDEX.md`; append-only.
- Never auto-merge kernels even if they look identical — surface to user.
- When extracting from `provenance: canonical-distilled`, kernels get `distilled: true` so the raw canonical source can later supersede.

### Skill graph + state-predicate routing

Drop folder-by-phase. Skills stay flat in `skills/` (plus `skills/_meta/` for system skills). Right skill surfaces from state via four mechanisms:

**1. Each SKILL.md gains structured routing frontmatter:**

```yaml
---
name: pricing-model
description: >
  (existing description...)
stages: [problem-validation, mvp-scoping, first-customers, growth, fundraising-prep]
topics: [pricing, monetization, revenue-model]
preconditions:
  - state has archetype defined
  - state has ICP defined
postconditions:
  - state.pricing section fully populated
successors:
  - mvp-architect (pricing changes MVP shape)
  - outreach-engine (pricing changes funnel math)
  - growth-funnel (post-PMF: pricing changes acquisition cost ceiling)
  - fundraising-readiness (pricing × volume drives valuation story)
predecessors:
  - problem-focus (need sharp ICP before pricing)
  - market-intel (need competitor pricing context)
---
```

**2. STARTUP-STATE.md gains `# Inferred Stage`** — auto-derived from which sections are filled and what numbers are written (e.g., Pricing empty → pre-pricing; PMF score = 35% → at-pmf-threshold; 50+ paying customers + 45% PMF score → ready-to-scale). Hint, not gate.

**3. STARTUP-STATE.md gains `# Session Pointers`** — every skill writes 1-3 forward-looking pointers when it completes. Accumulate across sessions. Orientation reads them as the freshest routing signal.

**4. The `orientation` skill becomes a graph router** — reads STARTUP-STATE.md, queries skill graph, filters by satisfied preconditions, ranks by (a) explicit session pointer match, (b) stage match, (c) topic match against user's most recent message, surfaces top 1-3 with rationale.

**Preconditions are natural language** (Claude evaluates by reading STARTUP-STATE.md), not a structured schema. Convention: predicates reference STARTUP-STATE.md section names directly, in the form `<Section> is <state>` or `<Section> has <field>`. Examples:

- `Pricing section is fully populated`
- `Founder Context has archetype defined`
- `PMF Audit shows score >= 40`
- `Discovery Log has at least 10 sessions`

Ambiguous wording (e.g., "pricing is figured out") is treated as a graph defect and surfaced by AUDIT.

**No central `SKILL-GRAPH.md` committed.** Generated on demand by the knowledge-pipeline AUDIT mode for human inspection. Source of truth = SKILL.md frontmatter.

## Migration plan (five phases)

### Phase 1: Restructure existing without changing semantics
- Add `resources/` to `.gitignore`.
- Create empty `references/kernels/`, `references/sources/`, `skills/_meta/`.
- Move existing 23 references into `references/composed/`. Do a single find-replace across all 16 SKILL.md files updating `${CLAUDE_PLUGIN_ROOT}/references/<file>.md` → `${CLAUDE_PLUGIN_ROOT}/references/composed/<file>.md`. One mechanical diff, easy to review.
- Move `orientation` into `skills/_meta/`. Other 15 skills stay flat in `skills/` (no pre/post-pmf split).
- Create `references/sources/INDEX.md` with table header.
- Populate routing frontmatter (`stages`, `topics`, `preconditions`, `postconditions`, `successors`, `predecessors`) on all 16 existing skills. Many of these derive from existing "Recommended Next Skills" sections.
- Commit. System works identically.

### Phase 2: Build the engine
- Author `skills/_meta/knowledge-pipeline/SKILL.md` per spec.
- Smoke-test on one short transcript with 2-3 kernels. Verify dedup, INDEX.md update, conflict surfacing.
- Commit.

### Phase 3: Canonical distillation (one-time backfill)
- Walk the 23 composed references. For each, identify the load-bearing claims (anchored anecdotes, named frameworks, specific rules).
- Extract each as a kernel with `provenance: canonical-distilled`, `distilled: true`, source id from canonical table (`c001` = Mom Test, `c002` = Sean Ellis 40% Rule, `c003` = YC Startup School, etc.).
- Update composed references to `[[wikilink]]` instead of duplicating.
- Estimated 60-100 canonical kernels. Spreads across multiple sessions. One domain per PR (pricing, then discovery, then validation, ...) for tractable review.

### Phase 4: First raw ingest (5 transcripts)
- Run INGEST mode on each transcript. ~25-35 new kernels.
- For each kernel that conflicts with a canonical kernel (e.g., charm-pricing vs price-as-quality-signal-band), the kernel body must resolve the conflict.
- Update affected composed references to cite new kernels alongside canonical ones.
- Ships per-transcript: five small PRs.

### Phase 5: New skills surface from kernels
- Three new skills (flat in `skills/`, frontmatter does the routing): `fundraising-readiness`, `growth-funnel`, `scaling-ops`.
- Four new composed references: `pricing-psychology.md`, `fundraising-fundamentals.md`, `growth-funnel-tactics.md`, `scaling-operations.md`.
- Design content from s002 goes into a new composed reference `visual-design-principles.md` loaded by `artifact-builder`. No standalone design-craft skill.
- Ships per-skill: three small PRs.

## First-batch concrete deliverables

### Kernels (~35 new from raw + 60-100 canonical-distilled)

- **From s001 (pricing psychology):** charm-pricing, price-as-quality-signal-band, pricing-anchoring-tiers, lost-price-points, product-mix-tiers, persona-segmentation-from-customer-data, persona-driven-copy
- **From s002 (web design):** typography-anchor-font, typography-super-families, star-of-the-show, visual-rhyming, depth-and-texture, opacity-hierarchy, iterate-past-first-thought
- **From s003 ($1M blueprint):** billion-dollar-market-test, high-leverage-business-test, what-how-who-sales, lean-learning-loop, customer-advisory-board, black-car-product, pizza-squad-rule, triad-of-influence, document-and-delegate
- **From s004 (fundraising):** explosive-growth-vs-lifestyle, round-mechanics-by-milestone, dilution-math, valuation-as-betting-odds, stuck-between-rounds, runway-to-next-fundable-milestone, convertible-note-basics
- **From s005 (e-commerce funnel):** banger-offers, ad-volume-strategy, white-label-collab-ads, persona-specific-landing-pages, popup-question-first, daily-email-sms-cadence, founder-text-emails, post-purchase-education-not-promotion

### New skill sketches

**`fundraising-readiness`**
```yaml
stages: [pmf-measurement, ready-to-scale, fundraising-prep]
preconditions:
  - pmf-audit has blessed scaling OR strong behavioral demand signal exists
  - pricing committed
postconditions:
  - state.fundraising populated (round target, milestone budget, valuation range, basket)
successors: [artifact-builder, outreach-engine]
predecessors: [pmf-audit, pricing-model]
refuses: fundraising work for lifestyle-basket businesses (cites [[explosive-growth-vs-lifestyle]])
```

**`growth-funnel`**
```yaml
stages: [first-customers, growth, scaling]
preconditions:
  - existing offer with measured traction (or subscription product)
  - pmf-audit not below 25%
postconditions:
  - state.growth-funnel populated (offer, ad strategy, lander variants, email cadence)
successors: [scaling-ops, fundraising-readiness]
predecessors: [pmf-audit, pricing-model, outreach-engine]
refuses: ad-spend authorization without measured winning offer
```

**`scaling-ops`**
```yaml
stages: [growth, scaling]
preconditions:
  - revenue trajectory > $50k MRR OR equivalent behavioral PMF + scale signal
postconditions:
  - state.operations populated (team plan, top-3 playbooks documented, founder leveling plan)
successors: [fundraising-readiness]
predecessors: [pmf-audit]
refuses: premature org build (>8 people before $1M ARR; cites [[pizza-squad-rule]])
```

### Composed reference updates (~9 existing rewrites + 4-5 new)

- New: `pricing-psychology.md`, `fundraising-fundamentals.md`, `growth-funnel-tactics.md`, `scaling-operations.md`, `visual-design-principles.md`.
- Updated to use wikilinks: `pricing-frameworks.md`, `landing-page-patterns.md`, `validation-techniques.md`, `mvp-examples.md`, `scoring-rubrics.md`, `email-templates.md`, `sales-funnel-math.md`, `bias-sentinel.md`, `external-resources.md`.

## What's at risk of going wrong

1. **Frontmatter discipline.** Routing graph stays correct only if every SKILL.md keeps `stages`/`preconditions`/`successors` accurate. Stale graph = bad routing. The AUDIT mode of `knowledge-pipeline` is the safeguard; it must be run regularly.
2. **Composed-vs-kernel boundary drift.** Easy to start re-explaining a kernel inline in a composed reference. The "no inline duplication" rule needs to be enforced in PR review.
3. **Canonical-distilled kernels getting frozen.** If we never re-ingest from raw canonical sources, the `distilled: true` flag becomes vestigial. Acceptable risk — flag stays for traceability even if never used.
4. **Conflict resolution quality.** When `conflicts-with:` is declared but bodies don't resolve well, the system reads as contradictory. AUDIT mode flags these; quality of resolution depends on author care.
5. **State predicates as natural language.** Ambiguous wording → wrong routing. Convention (predicates reference STARTUP-STATE.md section names directly) needs to be enforced; we accept some routing imprecision in exchange for not building a structured state schema.

## Out of scope (explicitly)

- Structured/typed state schema for STARTUP-STATE.md (preconditions stay natural language).
- Auto-generation of composed references from kernels without human review (COMPOSE mode always proposes diffs).
- Cross-source quote verification (we don't store raw canonical sources in repo; can't verify Mom Test quotes against source text).
- Multi-language or non-US-startup adaptations.
- Versioning of kernels (kernels are append-source / supersede-by, not versioned).
- A separate UI or tool for browsing the kernel graph; lives in markdown only.
