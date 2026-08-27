# Key concepts

Every term the other docs use. Grouped by where you first meet it.

## The skills themselves

**Skill** — A folder containing a `SKILL.md` procedure and, usually, a `references/` subfolder. An agent reads it and follows it. See [what this is](what-is-this.md).

**`SKILL.md`** — The procedure file. YAML front matter (`name`, `description`) followed by ground rules and numbered steps. The agent always reads the whole file.

**Reference file** — A Markdown file under a skill's `references/` folder covering one component: `auth.md`, `database.md`, `payments.md`. Loaded only when that component is in play, which keeps the agent's context small. Each opens with a `Last verified:` date.

**Brief** — A reference file written to be handed to a subagent as its entire instruction set. `review-an-app`'s `security.md`, `seo.md`, and `drift.md` are briefs. `start-an-app`'s reference files are deliberately *not* briefs; its critics are kept away from the files the app was built from, so they do not turn into checklist-runners.

**Official skill** — An agent skill published by the maintainers of a library, for their own library. `start-an-app` Step 2a installs six of them globally: `shadcn`, `vercel-react-best-practices`, `deploy-to-vercel`, `better-auth-best-practices`, `ai-sdk`, `mcp-builder`.

## Running a skill

**Branch** — An optional component of the app: sign-in, email, uploads, payments, AI, background jobs, agent access, documentation, legal pages, discoverability. `start-an-app` chooses branches by interview. `deploy-an-app` and `review-an-app` detect them by reading the code, which is what lets them work on an app they did not build. A branch that is off produces no code, no dependency, and no settings tab.

**Build sheet** — `start-an-app` Step 3. The plan restated in plain words before anything is built: the data model, what you can do, sign-in, look, legal, discoverability, and an explicit *not in version one* list. You approve it. Later, the critics measure the finished app against this sheet and nothing else, because it is the one thing you actually agreed to.

**Deploy sheet** — `deploy-an-app` Step 3. The same idea on the infrastructure side: what gets created, what it costs, what is irreversible, and where the one pause lands.

**Rendezvous** — `deploy-an-app` Step 6. One announced pause where you do the browser work no API can do — DNS records, OAuth callback URLs, provider sign-ups — handed over in a single block with exact values. Promised on the deploy sheet in advance, so it is a meeting rather than an interruption.

**Gate** — The step that proves the work with commands whose output is read, rather than with a list the agent agrees with. `start-an-app` Step 6 (`references/verify.md`) and `deploy-an-app` Step 9 (`references/gate.md`). The rule attached to both: *the gate is passed by fixing the code, never by widening the gate.*

**Critic / fresh eyes** — Subagents dispatched after the gate to read evidence the builder captured and report where the result diverges from the sheet. `start-an-app` Step 7, `deploy-an-app` Step 10. They get evidence, not access. Two rounds, then stop and report what is left — a third round is where an agent starts editing code it does not understand to make a report go away.

**Ledger** — `deploy-an-app`'s written record of every resource it creates, written as each one is created rather than reconstructed afterwards. It is what makes an interrupted deploy reportable instead of mysterious. Defined in `references/recovery.md`.

## Reviewing an app

**Lens** — One independent reviewer in `review-an-app` Step 3, dispatched in parallel with the others. Three exist: **security** (OWASP Top 10), **discoverability** (sitemap, `robots.txt`, `llms.txt`, metadata), and **drift**. Each gets its own reference file as its brief and nothing else — a lens that can see another's findings starts agreeing with them, and three agreeing agents read as corroboration when they are only an echo.

**Evidence pack** — Everything the three lenses need, captured once in `review-an-app` Step 2 before any of them start. They run at the same time and cannot share a port, a database, or a browser. Hard rule: no secret enters the pack — variable *names* from `.env`, never values.

**Drift** — What the app claims, against what it does. A landing page written at launch, a docs page for a flow that has since changed, a privacy policy from before the AI feature was added. Drift is the reason `review-an-app` exists: a code review reads the pending diff and is blind to anything already wrong before this branch.

**Finding** — A contradiction between what the app says and what it does, named with its consequence and a `path:line`, a response, or a quote. Anything else is a suggestion, and suggestions are not findings. **Absence is not a finding**: "no rate limiting" is something a bigger app would have, not something this one got wrong.

**Finding status** — `broken`, `missing`, or `worth knowing`. Only the first two get fixed. In `review-an-app`, a `suspect` finding is re-checked with a command before it reaches the report; if the command disagrees, the finding is dropped.

## Building a brand

**Render-and-look** — `create-brand-kit`'s central conviction: every mark is judged from its rendered pixels, by its own designer first, minimum three render-to-look cycles. Non-negotiable at every depth level. The harness is `scripts/preview.mjs`.

**Style route** — One of six ways to build a mark: wordmark-led, monogram, geometric abstract, pictorial, name-device, emblem. A concept round assigns one route per designer agent.

**Contact sheet** — A multi-mark render (`scripts/sheet.mjs`) showing every concept across dark, light, knockout, and ink columns. Shown to you at each phase gate instead of a description.

**Kill list** — The failure conditions in `references/review.md` that a critic audits every concept against. A round where the critic kills everything distils *why* into `brand/LAW.md`, which becomes mandatory reading for the next round.

**Depth level** — How much exploration happens before production. Default: one concept round, a checkpoint, one verifier. "Thorough": multiple rounds, per-concept critics, a two-judge panel, three verifiers. "Quick": no subagents at all. What varies is the exploration, never what ships — the full kit ships every time.

**Small-size variant** — The bolder sibling of the master mark that does favicon duty. Derived by hand in Phase 3, because favicon constraints applied to the master mark are what make a logo come out blocky.

## The stack `start-an-app` builds

**Next.js App Router** — The web framework. Pages live under `src/app/`.

**Drizzle** — The ORM. Schema changes always go through `db:generate` then `db:migrate`. **Never `drizzle-kit push`** — both skills ban it, and `deploy-an-app` bans it harder because the database holds real data by then.

**Better Auth** — Sign-in. It owns anything belonging to a user. Where it has a plugin for an integration — payments above all — the plugin is used, never the provider's standalone SDK wired in beside it. Its generated tables stay exactly as generated, which means any column pointing at a user stays `text`, not `uuid`.

**shadcn/ui** — The component library. Screens are composed from its components rather than hand-written markup that approximates them.

**`DESIGN.md`** — The app's look written down, at the project root, created in scaffold step 2 before any screen exists. From then on it is binding: a colour, a font size, or a radius hardcoded into a component is a bug, not a shortcut. `AGENTS.md` and `CLAUDE.md` both point at it, so the next agent to open the project is held to it too.

**MCP (Model Context Protocol)** — The optional agent-access branch. The app gains a second front door: the same actions, the same ownership rules, the same log, reached by an AI agent over an authenticated protocol. Hard rule: **a tool is another caller, never a second way in** — every tool takes the user from the token, never from anything the model passed.

**System page** — The always-on view inside the app of what is configured, what happened, and what is running. Built by `references/ops.md`. It is the answer to "why didn't that email arrive?" and "is that still running?".
