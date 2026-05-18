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
