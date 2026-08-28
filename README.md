# Deep-dive documentation for Leon van Zyl's agent skills

> **This repository is documentation, not code.**
> It is a **deep dive into a clone of another repository** — [**leonvanzyl/skills**](https://github.com/leonvanzyl/skills), by Leon van Zyl.
> Every skill described here is his work. Nothing in this repository replaces it, forks it, or ships it.
> To get the skills themselves, go to the original: **https://github.com/leonvanzyl/skills**

> **The video that started this:** [**The 3 Skills I Use to Build Any Web App**](https://www.youtube.com/watch?v=tqi4cTZZWzQ&t=5s)

---

## What this is

Leon van Zyl publishes four agent skills that take a web app from an idea to a live URL: **start it, review it, deploy it**, plus a **brand kit** for the logo. They are Markdown instruction files that an AI coding agent reads. They contain no application code.

The skills are large. `start-an-app/SKILL.md` alone is about 47 KB, with 17 reference files behind it. Reading all of that to decide whether a skill fits your project is a lot of work.

This repository is that reading, done once. It explains what each skill does, in what order, and why each rule exists.

## Start here

**Read it as a website: <https://az9713.github.io/start-any-app-tutorial/>** — every page below, rendered.

**[`docs/index.md`](docs/index.md)** — the navigation hub.

| Read this | Web | If you want |
| --- | --- | --- |
| [What this is](docs/overview/what-is-this.md) | [html](https://az9713.github.io/start-any-app-tutorial/docs/overview/what-is-this.html) | The mental model, and what a skill actually is |
| [Key concepts](docs/overview/key-concepts.md) | [html](https://az9713.github.io/start-any-app-tutorial/docs/overview/key-concepts.html) | The vocabulary — build sheet, lens, gate, ledger, branch, drift |
| [start-an-app](docs/concepts/start-an-app.md) | [html](https://az9713.github.io/start-any-app-tutorial/docs/concepts/start-an-app.html) | The 11 steps, the 12 interview questions, the 16-step scaffold order |
| [review-an-app](docs/concepts/review-an-app.md) | [html](https://az9713.github.io/start-any-app-tutorial/docs/concepts/review-an-app.html) | The evidence pack, the three parallel lenses, the report |
| [deploy-an-app](docs/concepts/deploy-an-app.md) | [html](https://az9713.github.io/start-any-app-tutorial/docs/concepts/deploy-an-app.html) | The 12 steps, from preflight to hand-off |
| [create-brand-kit](docs/concepts/create-brand-kit.md) | [html](https://az9713.github.io/start-any-app-tutorial/docs/concepts/create-brand-kit.html) | The 7 phases, the depth levels, what ships in the kit |
| [Customize a skill](docs/guides/customizing-references.md) | [html](https://az9713.github.io/start-any-app-tutorial/docs/guides/customizing-references.html) | To swap the auth provider, the host, or any component |
| [Shared design rules](docs/architecture/shared-design-rules.md) | [html](https://az9713.github.io/start-any-app-tutorial/docs/architecture/shared-design-rules.html) | The 8 invariants all four skills repeat, and why |

## Get the actual skills

From the original repository, not from this one:

```bash
npx skills add leonvanzyl/skills
```

Browse first, or install one at a time:

```bash
npx skills add leonvanzyl/skills --list
npx skills add leonvanzyl/skills --skill start-an-app
```

They work with Claude Code, Cursor, Codex, OpenCode, and any other agent the [`skills` CLI](https://github.com/vercel-labs/skills) supports.

## The four skills, in one line each

| Skill | What it does |
| --- | --- |
| `start-an-app` | Interviews you until the idea is concrete, then builds a Next.js app in your current folder and proves it works |
| `review-an-app` | Reads an app that already exists and reports contradictions between what it claims and what it does. Read-only |
| `deploy-an-app` | Provisions the infrastructure, gathers every credential once, deploys once, then checks the live site |
| `create-brand-kit` | Runs a critique-driven identity process from any starting point and packages the full kit |

## What is not in this repository

The skills. The `skills/` folder on the machine these docs were written from is a git clone of the original, and it is deliberately **not tracked here** — cloning someone else's repository into your own is how you end up shipping a stale copy of their work.

## Accuracy

These docs describe `leonvanzyl/skills` at commit **`b82bf0c`**.

The skills carry their own staleness convention: every reference file starts with a `Last verified:` date. Trust a recent date. Treat an old one as an assumption that something has moved. After you pull the original repository, re-check the pages against the files they track — [`docs/index.md`](docs/index.md) has that mapping in a table.

## Credit

- **The skills:** [Leon van Zyl](https://github.com/leonvanzyl) — [leonvanzyl/skills](https://github.com/leonvanzyl/skills)
- **The video:** [The 3 Skills I Use to Build Any Web App](https://www.youtube.com/watch?v=tqi4cTZZWzQ&t=5s)
- **This documentation:** written from the source files, in this repository only.
