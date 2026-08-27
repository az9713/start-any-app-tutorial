# start-an-app

Turns an idea into a running full-stack web app in the folder you are standing in. It is the largest skill in the collection — [`SKILL.md`](../../skills/skills/start-an-app/SKILL.md) is roughly 47 KB, with seventeen reference files behind it.

## Why it exists as its own skill

An agent handed "build me a hiking journal" will produce a generic CRUD shell and call it done. This skill puts an interview in front of the build and a gate behind it, so the result is an app you recognise and a claim you can check.

The skill states its own priority plainly: **understanding comes before scaffolding.** Ten minutes of good questions produces an app the user recognises; skipping them produces a shell they have to rewrite. Nothing touches the app before the build sheet is agreed, except the six skill installs in Step 2a.

## How it works

Eleven steps, in order.

| Step | Name | What happens |
| --- | --- | --- |
| 1a | Understand the idea | Open questions until the nouns and verbs are concrete; the data model said back in plain words |
| 1b | Find the gaps | Nine silent checks; two or three raised as suggestions |
| 1c | Technical choices | Twelve branch questions, one at a time, each with a recommendation |
| 2a | Install the official skills | Six `npx skills add ... -g` commands |
| 2b | Check what's left | One research subagent per chosen branch, dispatched in one message |
| 3 | Build sheet | The plan in plain words; you approve it |
| 4 | Scaffold | Sixteen reference files, in a fixed order |
| 5 | Make it theirs | Real names, real copy, real pages, the verbs wired up |
| 6 | Prove it | Commands that pass or fail (`references/verify.md`) |
| 7 | Fresh eyes | Critic subagents against the build sheet, two rounds |
| 8 | Hand off | Every unrun check named, with what it would need |

### Step 1 — the interview

**1a** asks the highest-value question in the skill: the agent says the data model back to you in plain words and lets you correct it. People who cannot design a schema can absolutely tell you what is wrong with one.

**1b** runs nine checks silently and raises only the ones that apply: whose data is it, can things be changed, is anyone special, what does day one look like, anything time-based, does anyone need telling, does anything take a while, phone or desktop, and what is deliberately *not* in version one. Raising all nine is an interrogation.

**1c** walks the twelve branch questions. Each carries a recommendation so you can answer "whatever you recommend":

| # | Question | Default |
| --- | --- | --- |
| 1 | Who will use it? | SQLite for one person, Postgres-in-Docker for more |
| 2 | Do people sign in? | Email + password; Google optional |
| 3 | Any email? | Resend if yes; skipped entirely if no |
| 4 | Any uploads? | Local folder now, cloud storage on deploy — same code |
| 5 | Any payments? | Polar recommended, Stripe if you already use it. Requires sign-in |
| 6 | Any AI? | Only if yes; AI SDK over OpenRouter |
| 7 | Background work? | **No.** Inngest only when work must survive a restart, retry, or run on a schedule |
| 8 | Agent access over MCP? | A genuine either/or, asked without leaning. Requires sign-in |
| 9 | What do signed-out visitors see? | Decides landing page vs straight into the app |
| 10 | Help pages? | **No**, and only asked if 9 was a real landing page |
| 11 | Should it be found? | Only asked where the app is public. Otherwise, deliberately kept out of search |
| 12 | How should it look? | Asked of every app, asked last |

Question 12 accepts three kinds of answer: a description ("calm and minimal"), a pasted design system, or "you pick". A pasted system **wins outright** and is used as given, not as inspiration. A description is played back as decisions — a named neutral base, one accent, a radius, a font — so you correct something concrete instead of agreeing with a mood.

### Step 2 — current facts, in two halves

**2a installs six official skills globally**, before any research:

```bash
npx skills add https://github.com/shadcn-ui/ui --skill shadcn -g --agent claude-code -y
npx skills add https://github.com/vercel-labs/agent-skills --skill vercel-react-best-practices -g --agent claude-code -y
npx skills add https://github.com/vercel-labs/agent-skills --skill deploy-to-vercel -g --agent claude-code -y
npx skills add https://github.com/better-auth/skills --skill better-auth-best-practices -g --agent claude-code -y
npx skills add https://github.com/vercel/ai --skill ai-sdk -g --agent claude-code -y
npx skills add https://github.com/anthropics/skills --skill mcp-builder -g --agent claude-code -y
```

`-g` matters twice. The folder you are standing in is about to become the app, and `create-next-app .` refuses to run in a non-empty directory — a project-level install would put `.claude/` there and break every build. And these are documentation for a fixed stack, so installing once serves every future app. `--agent claude-code -y` is what makes each command non-interactive; without both flags it stops on a prompt with nobody to answer it.

All six install even where a branch was not chosen. They are inert until read.

**2b dispatches one research subagent per chosen branch, all in a single message.** No file in this skill names a version, deliberately — this half is where versions come from. Two branches get extra instructions: the agent-access branch must establish the current Model Context Protocol revision, because a protocol can deprecate something without any package changing its name; the discoverability branch researches conventions rather than packages, including the current AI-crawler user-agent tokens, because a wrong crawler name in `robots.ts` is not an error, it is a rule that silently matches nothing.

**Precedence when sources disagree:**

| Disagreement | Winner | Why |
| --- | --- | --- |
| Official skill vs reference file, on API detail | Official skill | It is the maintainer speaking about their own library |
| Official skill vs reference file, on wiring into this app | Reference file | It knows which file owns what, and how it meets the schema, session, and log |
| Research vs official skill, on what is published now | Research | The registry is the fact; a skill is a file, and files go stale |
| Research vs official skill, on which pattern to use | Official skill | Several patterns work; the author knows which one they mean |

### Step 4 — the scaffold order

Sixteen steps, and the order carries most of the reasoning:

1. Base project — `references/stack.md`
2. **Design system — `references/design.md`** (always)
3. Database — `references/database.md`
4. Sign-in, if chosen — `references/auth.md`
5. Email, if chosen — `references/email.md`
6. File uploads, if chosen — `references/storage.md`
7. Payments, if chosen — `references/payments.md`
8. AI features, if chosen — `references/ai.md`
9. Background jobs, if chosen — `references/jobs.md`
10. Landing page and dashboard — `references/pages.md`
11. Agent access, if chosen — `references/mcp.md`
12. Public documentation, if chosen — `references/docs.md`
13. Legal pages and consent — `references/legal.md`
14. Account settings — `references/settings.md`
15. System visibility — `references/ops.md` (always)
16. Discoverability — `references/seo.md` (always)

The design system is **second** because it is the only step every later one reads from, and retrofitting a theme onto pages already written means editing all of them. It is also where `AGENTS.md` and `CLAUDE.md` are written, so every later step and every future agent is held to the same document.

Payments, uploads, and agent access all extend what sign-in built. Documentation can only describe branches that exist, and runs before legal so that legal's pass over the footer sees the docs link. Legal runs after every feature branch, because the privacy page has to describe all of them, and before settings, which grows a cookie-preferences section only if a banner was built. **Discoverability is last** because it writes the sitemap from a list of every public page, and legal and documentation both add pages to it.

The last three are not a polish pass. Two turn a scaffold into something you can operate, and the third decides whether anyone finds it.

### Steps 6 and 7 — the two checks

Step 6 runs commands and reads their output: types, schema drift, the build, lint, the app served in production mode, every route answering, two accounts tested against each other, and the app with its keys taken away. Two orderings matter:

> **Warning:** Generate the schema before the build. `build` runs `db:migrate` first, so reaching it with an ungenerated schema edit outstanding applies SQL nobody read.

> **Warning:** The user signs up before any probe account exists. The first account created becomes the admin — a fixture that takes that place, then gets deleted, locks the user out of their own system page.

Step 7 dispatches critics because the gate cannot tell whether the app *does* anything. An empty project passes every command, because nothing leaks when there is nothing to leak. The critics are promise-keeping and looks-like-theirs always, ownership wherever there is sign-in, and operability scaled to the branches that ran. They measure against the build sheet and nothing else.

## Interaction with other skills

- `create-brand-kit` is best run right after this, before you show the app to anyone.
- `review-an-app` covers what this skill's critics cannot: the app six months later, drifted past the sheet it was built to.
- `deploy-an-app` takes the result live. `deploy-to-vercel`, installed in Step 2a, is unused during the build and waits for that moment.

## Gotchas

**Never `drizzle-kit push`.** Schema changes go through `db:generate` then `db:migrate`, every time, from the first table.

**Ids are UUIDs — except in Better Auth's tables.** Those stay exactly as its CLI generates them, which means any column pointing at a user stays `text`, not `uuid`.

**No subfolder.** The app is scaffolded in the current working directory. You already chose where it goes by being there.

**No version numbers, anywhere.** Not in an install command, not in a `package.json` snippet, not a Docker image tag. A version written into a skill file is a lie with a timestamp on it.

**Nothing deprecated.** Still working is what deprecated means — it is a removal notice with a delay on it.

## Reference files

| File | Covers |
| --- | --- |
| `stack.md` | The Next.js project everything else builds on |
| `design.md` | `DESIGN.md`, theme tokens, `AGENTS.md`, `CLAUDE.md` |
| `database.md` | Drizzle, with a SQLite branch and a Postgres-in-Docker branch |
| `auth.md` | Better Auth, email + password, optional Google |
| `email.md` | Resend. Nothing sends until it is logged |
| `storage.md` | One storage module, two backends chosen at runtime |
| `payments.md` | Polar or Stripe, always through the Better Auth plugin |
| `ai.md` | AI SDK with OpenRouter |
| `jobs.md` | Inngest, with the app keeping its own record of every job |
| `pages.md` | Landing page and dashboard — the step that decides if it looks like your app |
| `mcp.md` | Agent access. The largest reference file at ~38 KB |
| `docs.md` | Public help pages, only where somebody would read them |
| `legal.md` | Privacy, terms, cookie consent — decided, never asked |
| `settings.md` | Profile, verification, password, devices, leaving |
| `ops.md` | The system page. Always runs |
| `seo.md` | Sitemap, `robots.txt`, `llms.txt`, or a deliberate keep-me-out |
| `verify.md` | The gate commands and the critic briefs. ~32 KB |

See [customizing a skill](../guides/customizing-references.md) to change any of them, and [shared design rules](../architecture/shared-design-rules.md) for the invariants this skill shares with the other three.
