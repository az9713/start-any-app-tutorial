# Leon's Agent Skills

A set of four agent skills that carry a web app from an idea to a live URL: start it, review it, deploy it — plus a brand kit for the logo. They are Markdown instruction files an AI coding agent reads; they contain no application code.

These docs describe the collection in [`../skills/`](../skills/), a clone of [github.com/leonvanzyl/skills](https://github.com/leonvanzyl/skills).

---

## Documentation

| Section | What's inside |
| --- | --- |
| [What this is](overview/what-is-this.md) | The mental model, the three-step workflow, and what a skill actually is |
| [Key concepts](overview/key-concepts.md) | Every term the other docs use — build sheet, lens, gate, ledger, branch |
| [start-an-app](concepts/start-an-app.md) | Interview, research, build sheet, scaffold, gate, critics, hand-off |
| [review-an-app](concepts/review-an-app.md) | Evidence pack, three parallel lenses, reconcile, report |
| [deploy-an-app](concepts/deploy-an-app.md) | Preflight, URL first, provision, rendezvous, one deploy, live gate |
| [create-brand-kit](concepts/create-brand-kit.md) | Render-and-look loop, concept round, critique, the deliverables package |
| [Customize a skill](guides/customizing-references.md) | Swap the auth provider, the host, or any other component |
| [Shared design rules](architecture/shared-design-rules.md) | The eight invariants all four skills share, and why each exists |

**New here?** Read [what this is](overview/what-is-this.md), then the concept page for the skill you are about to run.

---

## Install and run

The upstream [`skills/README.md`](../skills/README.md) is the quickstart. One command installs all four:

```bash
npx skills add leonvanzyl/skills
```

Then invoke one in your agent by name:

```text
Use the start-an-app skill. I want to build...
```

---

## Keeping these docs true

These docs describe `skills/` at commit `b82bf0c`. After a `git pull` in that folder, re-check the pages below against the files they track.

| This doc | Tracks |
| --- | --- |
| [overview/what-is-this.md](overview/what-is-this.md) | `skills/README.md` |
| [overview/key-concepts.md](overview/key-concepts.md) | all four `SKILL.md` files |
| [concepts/start-an-app.md](concepts/start-an-app.md) | `skills/skills/start-an-app/` |
| [concepts/review-an-app.md](concepts/review-an-app.md) | `skills/skills/review-an-app/` |
| [concepts/deploy-an-app.md](concepts/deploy-an-app.md) | `skills/skills/deploy-an-app/` |
| [concepts/create-brand-kit.md](concepts/create-brand-kit.md) | `skills/skills/create-brand-kit/` |
| [guides/customizing-references.md](guides/customizing-references.md) | every `references/` folder |
| [architecture/shared-design-rules.md](architecture/shared-design-rules.md) | the ground-rules block of each `SKILL.md` |

The skills carry their own staleness convention: every reference file starts with a `Last verified:` date. Trust a recent date; treat an old one as an assumption that something has moved.

> **Note:** `skills/` is a clone of someone else's repository. Local edits there conflict on the next pull. [Customize a skill](guides/customizing-references.md) covers how to make changes that survive.
