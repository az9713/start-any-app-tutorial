# deploy-an-app

Takes a Next.js app that runs on your machine and puts it into production on Vercel, then proves the live site works. [`SKILL.md`](../../skills/skills/deploy-an-app/SKILL.md), thirteen reference files.

## Why it exists as its own skill

The value is not that it types `vercel deploy`. It is that it asks for every human-only credential **once, in one place, before anything starts** — rather than discovering them one failed build at a time.

The skill draws a hard line around itself: **deploying is not building.** It provisions infrastructure, sets configuration, and runs deploys. It does not add features, redesign pages, or improve code it happens to dislike. There is exactly one common case where production requires a code change — a SQLite app moving to Postgres — and that goes on the sheet in Step 3 and is approved before it happens.

## How it works

Twelve steps. The ordering is the design.

| Step | Name | What happens |
| --- | --- | --- |
| 0 | Preflight | Read the machine, the account, the code. Change nothing |
| 1 | Check what's current | One research subagent per detected branch |
| 2 | What only the user knows | The address, the credentials, test mode or real money |
| 3 | Deploy sheet | The plan in plain words. The only decision point |
| 4 | The address | Create the project, read back the real URL, start DNS |
| 5 | Provision and prepare | Database, storage, the SQLite conversion, a local build against production |
| 6 | The rendezvous | One announced pause for browser work |
| 7 | The environment | Every variable written, then webhooks and job keys wired |
| 8 | Deploy | Once |
| 9 | Prove it | Commands against the live URL, whose output is read |
| 10 | Fresh eyes | Critic subagents, read-only, two rounds |
| 11 | Hand off | What exists, what it costs, what a `git push` now does |

### Step 0 — Preflight

Nothing here mutates anything. It establishes: whether the hosting CLI is available and what it can do, whether you are logged in and under which account, what this app is and what its build command really runs, which features it has, every environment variable the code reads, and whether the git tree is clean.

**The branch list comes from the code.** An app has payments because there is a payments client in it, not because you remembered to mention it. This is also what lets the skill work on an app it did not build.

> **Warning:** A dirty tree stops here. A deploy cut from uncommitted work is a build nobody can reproduce, and the two deploy mechanisms — a push and a direct upload — disagree about what it even contains.

Preflight also asks whether there is already a production deployment with users on it. If so the posture changes for the whole run: nothing is created from scratch, and Step 8 stops deploying straight to production. That is established here, not at the moment it matters.

### Step 1 — Check what's current

One research subagent per detected branch, all in a single message. Hosting CLIs, dashboards, and provider APIs move faster than application libraries, and they move in a way that breaks scripts rather than types. A renamed flag is a pipeline that stops halfway through, having already created things.

> **Note:** On what the hosting CLI can do, `--help` outranks everything — this skill, its reference files, the research, and the provider's own documentation. Documentation describes the release the writer had. Ask the binary that is about to run.

### Steps 2 and 3 — one decision point

Step 2 asks three things, one at a time. The address comes first because every callback URL, every webhook target, and the sign-in origin derive from it, and each one that changes later is manual work done twice. Then every provider credential the detected branches need, gathered in **one block** — not one question per failure. Then, if payments exist, test mode or real money.

Step 3 restates it as a deploy sheet: what will be created, what it costs, what is irreversible, and where the pause lands. **One approval, and one announced pause** — Step 3 is the only place you decide anything, and Step 6 is a scheduled hand-off promised in advance.

Real money is the one carve-out. It is the only action that can charge a real card, and live prices cannot be deleted afterwards, so it takes a separate explicit confirmation.

### Step 4 — the address, before anything exists

Create the project, **read back the production URL the platform actually assigned**, attach the custom domain, and produce the DNS records. A name collision makes the host assign something else, and every value derived from a guessed URL — the sign-in origin, the OAuth callbacks, the token audience — is then quietly wrong.

DNS starts early because it is the slowest thing in the pipeline and depends on nothing.

Where a domain is involved, decide the canonical host — bare or `www` — and redirect the other to it. A session cookie set on the wrong one is a sign-in that appears to work and then does not.

### Step 5 — provision, and the best-value check in the skill

Create the database and the file store and connect them. Two details decide whether it works: the name the provider injects may not be the name the code reads, and migrations want the direct connection string while the app wants the pooled one.

Any code change happens here — the SQLite conversion, and reconciling a variable name — and gets committed.

**Then build locally against the production database, before deploying anything.** It runs the same migrate-then-build the platform will run, catches the conversion and every module-scope key, and costs no deployments.

### Step 7 — the environment, before the build

Every variable the code reads is written to production before any build runs. Provider clients get constructed when a module is first imported, so a missing key is a failed build, not a runtime warning.

> **Warning:** `.env` is not a deployment artefact. Some values in it are actively dangerous in production — a local database address, a sandbox payments flag, a switch that turns off signature checking. Each is copied, transformed, regenerated, or refused deliberately. `references/env.md` has the table; there is no copy loop.

Three things make this more than a copy: values that must be transformed or refused; secrets corrupted by an invisible trailing newline that cannot be read back to diagnose; and the fact that a written secret can be confirmed to *exist* but never to be *correct*.

### Steps 8 and 9 — one deploy, then proof

Everything the build needs is already in place, so the first deploy is the first one that can succeed. Where you have a repository, connect it and let a push produce the deploy — the mechanism you will use forever is the one that just got proven.

Then confirm the deployment that went live is the commit you think it is. One command, and it catches the two most common false victories: reading the previous build's result while the new one is still running, and deploying something that was never committed.

Step 9 runs commands against the live URL. More is provable from outside without a browser than it looks: the deployed commit, that migrations ran, that the database answers, every route, the certificate and canonical host, cookie flags, that a webhook endpoint verifies signatures rather than accepting anything, and that discovery documents name the real domain.

> **Note:** Distinguish failed from blocked from not attempted. If DNS has not landed, every check behind the domain is red for one reason. Reporting fifteen failures for one cause teaches the user to ignore the gate.

## Interaction with other skills

- Expects what `start-an-app` builds, but detects branches from code, so it works on other apps.
- Run `review-an-app` first if the app has been alive a while.
- `deploy-to-vercel`, one of the six official skills `start-an-app` Step 2a installs, is the one that was never used during the build — this is its moment.

## Gotchas

**Check before creating, and write down what was created.** Every provider will happily create a second one of anything. Two webhooks on one URL means every event processed twice, with half the signature checks failing. The [ledger](../overview/key-concepts.md) is written as each resource is created, never reconstructed afterwards.

**When something fails, stop and report — never tear down.** A database a later step could not reach still holds the schema that worked. `references/recovery.md` says what can be undone and what cannot.

**Never delete or overwrite something this run did not create.** A name collision stops and asks. The exception is a value this run wrote itself and is correcting.

**The gate is passed by fixing the deploy, never by widening the gate.** No build-error suppression, no skipping validation, and above all never removing the migration step from the build script to make a red build go green.

**Never `drizzle-kit push`.** `start-an-app` bans it while building. Here the database has real data in it.

**No subfolder.** Deploys from the current working directory.

## Reference files

| File | Covers |
| --- | --- |
| `preflight.md` | The three inventories: CLI, app, environment variables |
| `project-and-url.md` | Creating the project, reading back the real URL, DNS records |
| `provision-database.md` | Hosted Postgres, and the SQLite conversion |
| `provision-storage.md` | Uploads in production — a setting, not a code change |
| `env.md` | The variable table: copy, transform, regenerate, or refuse |
| `wire-auth.md` | Sign-in on the real domain. Never weaken auth to make something work |
| `wire-email.md` | Domain verification. Start first, check last — it is the only step measured in hours |
| `wire-payments.md` | Webhooks, test mode by default |
| `wire-jobs.md` | Background jobs, and keeping the development flag out of production |
| `wire-mcp.md` | Agent access, where one wrong character fails with no useful error |
| `deploy.md` | The deploy itself, and the already-live-with-users posture |
| `gate.md` | The live checks, and the critic briefs |
| `recovery.md` | The ledger format, and what happens when a step fails halfway |

See [shared design rules](../architecture/shared-design-rules.md) for the invariants this skill shares with the other three.
