# review-an-app

Reads a web app that already exists and reports what is actually wrong with it. Read-only by default. [`SKILL.md`](../../skills/skills/review-an-app/SKILL.md), four reference files.

## Why it exists as its own skill

Three other things already review code, and all three are anchored to something other than the app:

| Tool | Anchored to | Blind to |
| --- | --- | --- |
| `/code-review` | The pending diff | Anything already wrong before this branch |
| `deploy-an-app` | Whether a deploy will land | Whether the app is right |
| `start-an-app` critics | A build sheet written the day the app was scaffolded | Six months of change since |

An app six months in has drifted past all three. This skill reviews the thing itself, as it is now.

## The two rules that define it

**The app is its own spec.** There is no build sheet here. The only things to measure against are the app's own claims — what the landing page sells, what the docs instruct, what the privacy policy promises, what the sitemap invites a crawler to open, what a button implies when it renders. A [finding](../overview/key-concepts.md) is a **contradiction** between what the app says and what it does.

**Absence is not a finding.** "No rate limiting", "no tests", "consider adding structured data" — none of these are things the app got wrong. They are things a bigger app would have. The skill states the stakes plainly: the fastest way to make it worthless is to let it list what a mature product does, because that report gets skimmed once and never again.

Two corollaries. **Consequence, not pattern**: "this query isn't scoped by user" is a pattern; "any signed-in account can read every other account's invoices by changing the id in the URL — `src/app/invoices/[id]/page.tsx:14`" is a finding. And **the user's choices are not findings**: a stack you would have picked differently, or a deliberate decision to stay out of search, is not wrong. Where a choice looks like an accident, report the evidence that it was an accident, not the choice.

## How it works

```
Step 1  Orient          read the stack, the branches, the claims
   │
Step 2  Evidence        capture once — routes, probes, served HTML     ← no secrets
   │
Step 3  Lenses          ┌─ security ─┐
        (parallel)      ├─ discover. ─┤  each gets one brief, sees no other output
                        └─ drift ────┘
   │
Step 4  Reconcile       re-check every suspect with a command; dedupe; rank
   │
Step 5  Report          worst-first, plain language, what was NOT checked
   │
Step 6  Fix             only if you ask
```

### Step 1 — Orient

Establish four things from the code, not by asking: **the stack** (framework, router, ORM, auth library), **which branches exist**, **whether the app is meant to be found** (`robots.ts`, `robots.txt`, root layout metadata), and **what the app claims** (landing page, docs, legal pages, pricing).

The skill is written for what `start-an-app` builds. Where it meets a different stack it says so in the report and says which checks that weakened — a review that pretends to full coverage over a stack it does not know is worse than a narrower one that names its edges.

Knowing the branches turns lenses *off* as much as on. A lens that knows the app has no payments will not go looking for webhook verification.

### Step 2 — Evidence, once

Three lenses run at the same time and cannot share a port, a database, or a browser. Whatever is not captured here is something three agents each try to find out for themselves, badly. `references/evidence.md` has the commands: the route inventory, whether the app runs, the probe sweep.

> **Warning:** No secret enters the evidence pack. Variable *names* from `.env`, never values. `git diff` and served HTML get read for leaks, never pasted wholesale. A review that leaks the key it was checking on is the only way this skill can do real damage.

### Step 3 — The three lenses

Dispatched in a single message so they run at once.

| Lens | Brief | Covers |
| --- | --- | --- |
| Security | `references/security.md` | OWASP Top 10, grounded in what this stack actually gets wrong |
| Discoverability | `references/seo.md` | sitemap, `robots.txt`, `llms.txt`, metadata — still true, or not |
| Drift | `references/drift.md` | what the app claims, against what it does |

**Each lens gets its own reference file and nothing else** — not `SKILL.md`, and not another lens's output. A lens that can see another's findings starts agreeing with them, and three agreeing agents read as corroboration when they are only an echo.

Note the deliberate contrast with `start-an-app`. There, critics are kept *away* from the files the app was built from. Here, the reference files are handed *to* the reviewers. The difference is what the file is: build instructions given to a critic turn it back into a checklist-runner; these were written for the reviewer.

Lenses get evidence, not access. A lens that can run things will spend its budget running things.

### Step 4 — Reconcile

The lenses are done and none of them is right yet.

- **Re-check every `suspect` with a command.** This is what separates a review from a guess. If the command disagrees, drop the finding and say in one line that it was checked and cleared.
- **Deduplicate across lenses.** A privacy policy promising deletion that nothing performs is one finding, not two. Keep the version that names the consequence best.
- **Drop anything without a `path:line`, a response, or a quote.** If it has none, it was an opinion wearing a template.
- **Where two lenses disagree, the one holding a `path:line` wins.** Where both do, it is genuine ambiguity and goes in as one finding.
- **Rank by consequence, not by lens.** One ordered list. An unverified payment webhook and a stale `llms.txt` do not belong in the same section just because they came from different agents.

### Step 5 — Report

Worst-first, in plain language, for someone who may not read code. Lead with the count and the shape — "four things are wrong, one seriously" — then each finding: what it is, where, why it matters to a person, and what fixing it involves.

Then the part that makes it honest:

- **What was checked, and how.** Whether the app was served or only read. The two produce very different confidence, and the reader cannot tell them apart from the findings alone.
- **What was not checked, and what it would need.** A browser, a key, a live payment, a production domain.
- **The closing sentence never says the app is secure.** No review establishes that. "No vulnerabilities found" is a sentence this skill must never produce, because it will be quoted back later by someone who has stopped looking.

If a lens found nothing, that is one line, and it is a result rather than a failure.

### Step 6 — Fix, only if asked

The review ends at Step 5. If you then ask for fixes: confirmed `broken` findings first, one at a time, each verified after. **Fixes touch only the files the finding names** — no refactors, no tidying, no "while I'm in here". This is somebody's working app, not a scaffold. A fix needing a schema change goes through `db:generate`, a read of the generated SQL, then `db:migrate` — never `push`, never an edit to a migration already applied. Nothing on the `worth knowing` list gets fixed unless you name it.

## Interaction with other skills

- Runs on any app, including one this collection did not build — it detects branches by reading code.
- Complements `start-an-app` Step 7 rather than repeating it. Those critics measure against the build sheet; this measures against the app.
- Run it before `deploy-an-app` if the app has been alive a while.

## Gotchas

**Read-only means read-only.** The skill may serve the app to probe it and changes nothing else. Fixing is a separate, explicit ask.

**Version numbers are established at review time, never written down.** AI crawler user-agent tokens, advisory databases, and framework file conventions all move.

**A long report deserves a page.** The skill offers to publish it rather than leaving it in terminal scrollback.

## Reference files

| File | Role |
| --- | --- |
| `evidence.md` | The capture commands, and the no-secrets rule |
| `security.md` | The security lens brief — OWASP Top 10 for this stack |
| `seo.md` | The discoverability lens brief |
| `drift.md` | The drift lens brief |

See [shared design rules](../architecture/shared-design-rules.md) for the invariants this skill shares with the other three, and [customizing a skill](../guides/customizing-references.md) to change a brief.
