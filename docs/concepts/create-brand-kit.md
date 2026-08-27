# create-brand-kit

Runs a critique-driven brand-identity process from any starting point — a website, a brief, a product list, or just a name — through to a packaged kit. [`SKILL.md`](../../skills/skills/create-brand-kit/SKILL.md), three reference files, five scripts, and an evals folder.

## Why it exists as its own skill

The other three skills build software. This one is the only skill in the collection with executable helpers, because it is the only one whose output is judged by eye rather than by a command exiting zero.

It refuses the narrow version of the ask. Even if you only say "make me a logo", the skill states its position: a logo that ships without its variants, small-size testing, and guidelines is not done.

## The two convictions

**1. Quality comes from rendering and looking, not describing.** Every mark is judged from its rendered pixels, by its own designer first, with a minimum of three render-to-look cycles. Non-negotiable at every depth level. Designers report what they *saw* in the render, not what they intended.

**2. The deliverables package is the product.** The full kit — every variant, the guidelines page, the README — ships every time, even on a "quick" run. What varies with depth is how much exploration happens before production, never what ships.

## Depth levels

| Level | Trigger | Shape |
| --- | --- | --- |
| Quick | "quick", "simple" | No subagents. 2–3 concepts designed in the main loop, self-critiqued against the kill list |
| Default | (none) | One concept round: 3–4 designers plus one critic, a user checkpoint, one verifier. Roughly 300–600k agent tokens |
| Thorough | "thorough", "no budget limit" | Multiple rounds, per-concept critics, a two-judge panel, three verifiers, and law-file accumulation between rounds |

## How it works

Seven phases. Note which are delegated and which are not.

| Phase | Who does it | What happens |
| --- | --- | --- |
| 0 | You (the agent) | Understand the brand, up to four questions, check the name for a gift |
| 1 | You | Pick 3–4 style routes, set the palette and typography, **build the render harness** |
| 2 | Subagents | One designer per route, one critic, a contact sheet, your pick |
| 3 | You | Refine by hand — 3–5 geometry variants per open question |
| 4 | Subagents | Adversarial verification across three lenses |
| 5 | You | Production: every variant, guidelines page, README, packaged and zipped |
| 6 | Optional | Photorealistic merch mockups, only if the Codex CLI is installed |

### Phase 1 — set up the harness first

The last item in Phase 1 is the one that gates everything: copy the skill's `scripts/` into the project's `build/`, adapt the paths, and verify headless Chrome renders an SVG *before* any designer needs it. Nothing else in the process works without the visual loop.

Palette: ink plus paper plus **one** flat accent with a darker twin, WCAG 4.5:1 computed on both grounds. Typography: a display face and a text face, with the font file fetched now so the wordmark can be outlined later.

### Phase 2 — the concept round

Three to four designer agents run in parallel, one style route each, every one reading `BRIEF.md` and `references/design.md`. The construction rules in that file are the heart of the skill: a roomy 512 frame, primitives and strokes rather than freehand paths, a stated proportion system.

Those rules exist because two failure modes dominated earlier runs. Marks came out **blocky** — built on a coarse pixel grid, curve-phobic, favicon constraints applied to the master mark. Or they came out **broken** — hand-authored bézier paths that drew something the designer never intended and never looked at.

One critic audits the whole field against the [kill list](../overview/key-concepts.md). A contact sheet gets rendered and shown to you: which direction do you want refined, and what would you change? **Your pick outranks any score.**

If the whole field is weak — the critic kills everything, or you reject it — the *why* is distilled into `brand/LAW.md` as general kill conditions, and one more round runs with those laws as mandatory reading. Default budget is two rounds.

### Phase 3 — the part that is not delegated

Take the chosen concept and finish it by hand. Build 3–5 geometry variants per open question, render contact sheets, choose with your eyes, measure with `scripts/optics.py` (ink percentage, centroid, bounding box, maximum ink radius).

The skill is explicit that this stays in the main loop: it is twenty minutes of taste that agents consistently fumble. Verify every critique instruction against a render before applying it — reviewers are sometimes wrong in the details.

Then derive the **small-size variant**: the bolder sibling that does favicon duty. Deriving it here, rather than constraining the master mark, is what stops the blocky failure mode.

### Phase 4 — verification

Three lenses: legal and prior-art, production, and unintended readings. Default is one adversarial verifier covering all three, plus your own pass over the production gates in `references/deliverables.md`.

Findings split two ways: *must-fix before delivery* (fix them) and *document as limitation* (put them in the README honestly). A kit that hides its known weaknesses fails its owner later.

### Phase 5 — what ships

| Deliverable | Detail |
| --- | --- |
| Logomark | dark, light, mono-positive, mono-knockout, all-accent; tight artboard |
| Wordmark | true font outlines, never live text; accent letters as their own path group |
| Lockups | horizontal and stacked; the mark's foot on the wordmark's baseline; size from a stated geometric relationship, not eyeballed |
| Icons | square, app (plated), round-safe avatar, adaptive foreground scaled by maximum ink radius |
| Favicon | one file with an embedded `prefers-color-scheme` style block |
| `-themed` variants | `currentColor` plus `var(--accent)` for inlining |
| PNG rasters | favicons 16/32/48, apple-touch 180, adaptive 432, avatars, marks, lockups |
| `guidelines.html` | generated **from** the shipped SVGs, so it cannot drift from the assets |
| `README.md` | a pick-the-right-file table, the circular-mask warning, and the honest known-open-items list |

Everything packages into `<company>-brand-kit/` and gets zipped.

The SVG technical contract in `references/deliverables.md` binds every file: pure vector, no filters, no blurs, no `<image>`, no live `<text>`, no external fonts, and explicit `width` and `height` matching the viewBox. Without width and height an `<img>` falls back to 300×150 and letterboxes, and Office and Slides mis-size a viewBox-only file.

### Phase 6 — merch mockups

Offered only if `codex --version` succeeds, and only run if you say yes — it takes time and uses your Codex account. Produces the logo screen-printed on shirts, embroidered on caps, and printed on business cards, with checks that catch mangled wordmarks. Surviving images land in `<kit>/mockups/`, `guidelines.html` is regenerated so its gallery shows them, and the kit is re-zipped. If Codex is not installed, the skill does not offer and does not mention it.

## Working style

- Show real renders at every phase gate — contact sheets and in-situ mockups, not descriptions. Send files as they are produced.
- Report failures honestly, including your own. "All four concepts have problems, here they are" builds more trust than a highlight reel, and you need the failure reasons to steer.
- Between agent runs, keep building the parts that do not depend on the outcome.
- **Your taste outranks the process.** Love a mark the critic scored 38? It gets refined, with the critic's concerns noted in the README. Hate the judges' winner? It gets killed.

## Interaction with other skills

Best run right after `start-an-app`, before you show the app to anyone. It is independent of the other three — it needs no app, and produces no code.

It is the only skill here that calls the Workflow tool, and its own instructions are the authorization to do so. Where Workflow is unavailable, it falls back to parallel agent calls.

## Gotchas

**The harness comes before the designers.** A designer agent with no working render loop produces descriptions, which is the failure this skill is built to prevent.

**Never live text in a wordmark.** Outline it from the TTF with `scripts/outline.py`, which preserves real kerning.

**Circular masks need the ink-radius rule.** An avatar clipped to a circle crops a mark that was not measured for it. `references/deliverables.md` has the maths, and the README ships the warning.

## Reference files and scripts

| File | Role |
| --- | --- |
| `references/design.md` | The creative playbook: style routes, construction rules, typography, palette, the designer prompt. Loaded into every designer |
| `references/review.md` | Kill list, cliché list, scoring, critic/judge/verifier prompts, round-escalation rules. Loaded into every reviewer |
| `references/deliverables.md` | Asset manifest, SVG technical contract, construction maths, packaging layout, merch templates |
| `scripts/preview.mjs` | The render-and-look harness: colour-true and mono rows, raster ladder, silhouette, circular crop, ink-gain |
| `scripts/sheet.mjs` | Multi-mark contact sheets |
| `scripts/context.mjs` | In-situ mockups: browser tab, app grid, avatar, cards, size ladders |
| `scripts/optics.py` | Measures a rendered mark: ink %, centroid, bbox, max ink radius |
| `scripts/outline.py` | Outlines TTF text into SVG paths with real kerning, via fontTools |

See [customizing a skill](../guides/customizing-references.md) to change any of them.
