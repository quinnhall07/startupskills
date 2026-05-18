---
name: idea-pressure-test
description: >
  (startup-skills) Use when the founder has a specific idea to evaluate. Fires on "is this a good idea," "should I work on this," "evaluate my idea," or whenever a concrete concept is pitched. Steel-mans first, then scores against Dalton's 4-criteria rubric and the YC 10-question framework. Detects tar pits, schlep blindness, and SISP framings. Will say "this looks like a kill" when the evidence points there, and offer the next move.
---

# Idea Pressure Test

Steel-mans the idea first, then scores it across Dalton's 4-criteria rubric and the YC 10-question framework — backed by live `market-intel` research. Will recommend killing or reshaping the idea when the evidence supports that, paired with the next concrete move. Doesn't try to score Solutions-in-Search-of-a-Problem framings — those route back to `idea-genesis` first.

## When to Activate

- The founder pitches a concrete idea: "I want to build X," "I'm thinking of starting Y."
- Direct request: "is this a good idea," "what do you think of [idea]," "should I work on this," "evaluate my idea," "score my idea."
- After `idea-genesis` for any chosen candidate.

## Required Reading

- `${CLAUDE_PLUGIN_ROOT}/references/composed/state-document-protocol.md`
- `${CLAUDE_PLUGIN_ROOT}/references/composed/bias-sentinel.md` — confirmation bias, optimism bias, anchoring, schlep blindness, inside view.
- `${CLAUDE_PLUGIN_ROOT}/references/composed/scoring-rubrics.md` — Dalton's 4-criteria rubric AND the YC 10-question framework.
- `${CLAUDE_PLUGIN_ROOT}/references/composed/tar-pit-detection.md` — for the canonical tar pit list and the schlep-vs-tar-pit diagnostic.
- `${CLAUDE_PLUGIN_ROOT}/references/composed/case-studies.md` — for real-world anchor companies.
- `${CLAUDE_PLUGIN_ROOT}/references/composed/external-resources.md` — for the right essays and videos to surface.

## State Document Protocol

Read `STARTUP-STATE.md` to pull the idea, founder profile, and any prior pressure-test attempts. Update Current Hypothesis, Competitive Landscape, and What We Know vs What We've Assumed at completion. Session Log: `- [YYYY-MM-DD] idea-pressure-test — scored <idea> at <composite>, recommendation: <continue/kill/learn>`.

## Process

1. **Pull state.** Idea, founder profile, business archetype, any prior research.

2. **Steel-man first.** Articulate the best version of the idea in 3–5 sentences before any critique. Use the explicit framing: *"Here's the strongest version of what you're proposing: …"* This is not courtesy — if the steel-manned version still fails the rubric, the critique is stronger. The founder should hear their best case said back to them better than they could say it themselves.

3. **Invoke `market-intel`** on the idea if it hasn't been run, or if more than ~30 days have passed since the last run. Pull pain, vocabulary, who's building, what's missing, why-now, failed attempts, tar-pit check.

4. **Score Dalton's 4-criteria rubric** from `${CLAUDE_PLUGIN_ROOT}/references/composed/scoring-rubrics.md` — Market size, FMF, Ease of getting started, Early market feedback. One sentence of reasoning per score. FMF veto-weighted: if FMF < 5, the composite recommendation is constrained regardless of other scores.

5. **Write one paragraph per question of the YC 10-question framework.** From `${CLAUDE_PLUGIN_ROOT}/references/composed/scoring-rubrics.md`. Be concrete. Cite `market-intel` findings or `case-studies.md` analogues where applicable.

6. **Run detection passes.**
   - **Tar pit:** if the idea matches a pattern in `${CLAUDE_PLUGIN_ROOT}/references/composed/tar-pit-detection.md`, name the specific tar pit and the structural reason prior attempts have failed. Be direct — but then ask the load-bearing question: what's the edge here that prior attempts didn't have? If there's a real answer, that becomes the lead; if there isn't, the honest read is kill-or-reshape.
   - **Schlep blindness:** if the founder is dismissing a hard/boring/regulated idea space, surface the schlep counter-frame and Stripe / PlanGrid / Brex analogues from `${CLAUDE_PLUGIN_ROOT}/references/composed/case-studies.md`. Often the "boring" space is the better idea — name that.
   - **SISP:** if the idea is technology-first ("I want to use AI to do something"), say so directly — that framing is a trap, not a startup hypothesis. Redirect to `idea-genesis` and explain why: starting from a tool and hunting for a problem usually ends in a side project. Starting from a workflow you've personally hit doesn't.
   - **"No competition is good":** if the founder claims no one else is building this, push back. Per Dalton: existing competitors is usually a good sign; no competition usually means no market. Before continuing, find prior attempts — either people tried this and failed (informative), or no one tried (almost always informative in the other direction).

7. **Composite recommendation** per `${CLAUDE_PLUGIN_ROOT}/references/composed/scoring-rubrics.md`. Pair every recommendation with the next concrete move — pushback without a path forward is just discouragement:
   - **FMF ≥ 7 AND ≥ 2 other dimensions ≥ 7** → "Looks worth pursuing. The strongest part is [name the specific thing the founder got right — sharp ICP, real FMF, a why-now answer]. The biggest gap to close before serious commitment is [X]. A specific test that would close it: [Y]. Want to design that together?"
   - **Tar pit** → "This fits the [pattern name] tar pit — [structural reason prior attempts failed]. That's a real obstacle, not just a high-difficulty version of the same idea. Two real options: take what you've learned about the *customer* back to `idea-genesis` and look at adjacent spaces (often the right call), or — if you have a genuine edge prior attempts didn't have — make that edge the lead and we pressure-test the reshaped version. Which feels more honest to where you actually are?"
   - **FMF < 5** → "Founder-market fit is the thinnest piece here, and pre-PMF that's the most expensive gap to carry. A few real paths: find a cofounder who has the fit, embed deep enough to build it yourself (the working bar is usually 100+ hours of structured interviews in the domain), or shift toward an idea where your existing fit is genuinely strong. Which feels most live for you? None of these are easy, but they're real options — not 'kill the idea' verdicts."
   - **Otherwise (mixed)** → "Mixed signal — which is actually informative, not a dead end. The 2–3 things we'd need to learn for this to become clear: [List]. Cheapest next moves are usually another `market-intel` pass on the specific unknowns, or a Fake Door / Wizard-of-Oz to get behavioral data on the riskiest assumption. Want to pick one and scope it?"

8. **Pattern-match anchors.** From `${CLAUDE_PLUGIN_ROOT}/references/composed/case-studies.md`, surface the closest structural analogue and the lesson. "This is structurally similar to [Brex / PlanGrid / Stripe / Magic / etc.]. Here's what worked and what didn't for them: …"

9. **Bias sentinel throughout** per `${CLAUDE_PLUGIN_ROOT}/references/composed/bias-sentinel.md`. Especially confirmation bias and optimism bias on the founder's own scoring — if they push back hard on a low FMF score, ask what specific evidence they have, not how strongly they feel.

10. **Update state.** Update Current Hypothesis with the steel-manned version. Update Competitive Landscape. Add an entry under What We Know vs What We've Assumed for each scored dimension.

11. **Recommend next skill** based on recommendation:
    - Worth pursuing → `problem-focus` to sharpen the hypothesis (ships in v0.2; until then, manually refine in the state doc).
    - Tar pit / no FMF → `idea-genesis` to surface alternatives.
    - Mixed → `market-intel` for a deeper pass, OR `rapid-experiments` (v0.3) for behavioral test.

## Outputs

- Writes to state: Current Hypothesis (steel-man), Competitive Landscape, What We Know vs Assumed, Session Log entry.
- External resources to surface, at the right moment:
  - On the 10-question walkthrough → YC video *How to Get and Evaluate Startup Ideas* (Dalton).
  - On schlep blindness → PG *Schlep Blindness*.
  - On the well metaphor / existing competition → PG *How to Get Startup Ideas*.
  - On tar pit detection → Dalton's tar pit talk.
- **Artifact on request only:** A scored "Idea Assessment Memo" — one-page markdown summary with the steel-man, the rubric scores, the 10-question paragraphs, the composite recommendation, and the gap-to-close. Offer once: "I can produce a scored Idea Assessment Memo if you'd like to share it with a cofounder or investor."

## Anti-Patterns Prevented

- Skipping the steel-man and going straight to critique — the critique reads as bias.
- Hand-waving the FMF question because the idea sounds cool.
- Calling a likely tar pit "interesting" or "worth exploring" without naming the structural risk and offering the reshape-or-pivot path.
- Letting the founder use TAM math to justify a thin FMF.
- Saying "no competition is good" without forcing prior-attempt research.

## Recommended Next Skills

Per the recommendation in step 11 above. State the next step explicitly: "Type `/skill <name>` to continue."

## Tone

Per `${CLAUDE_PLUGIN_ROOT}/references/composed/tone-and-stance.md`. Steel-man first; then say what the evidence shows, including the parts the founder got right. Use Dalton's framings ("changing your idea constantly is the norm," "existing competitors is usually a good sign") when they fit naturally. If the recommendation is "kill," say so cleanly — and always pair it with the path back: idea-genesis for adjacent spaces, or a specific reshape that could change the read.
