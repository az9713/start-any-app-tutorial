# What this is

Four sets of written instructions that tell an AI coding agent how to build, check, ship, and brand a web application.

## The problem it solves

An agent asked to "build me an app" produces a generic scaffold, then claims it works. Nothing checked whether it compiles, whether one user can read another's data, or whether the landing page still describes the product. These skills replace improvisation with a fixed procedure that ends in commands that pass or fail.

## What a skill is

A skill is a folder. Inside it:

| Item | Role |
| --- | --- |
| `SKILL.md` | The procedure. Steps, ground rules, and pointers to the reference files. Always read. |
| `references/*.md` | One file per component — auth, database, payments, storage. Read only when that component is in play. |
| `scripts/` | Executable helpers. Only `create-brand-kit` has these. |
| `evals/` | Test cases for the skill itself. Only `create-brand-kit` has these. |

`SKILL.md` opens with YAML front matter holding a `name` and a `description`. The agent reads the description to decide whether the skill applies to what you just asked for. Everything below the front matter is the procedure.

Nothing here executes on its own. A skill is text that an agent obeys.

## The three-step workflow

```
     idea
      │
      ▼
┌──────────────┐   working Next.js app, in your current folder
│ start-an-app │──────────────────────────────────────────────┐
└──────────────┘                                              │
      │ optional, right after                                 │
      ▼                                                       │
┌──────────────────┐  logo, wordmark, icons, guidelines       │
│ create-brand-kit │                                          │
└──────────────────┘                                          │
                                                              ▼
                                                    ┌────────────────┐
                                                    │ review-an-app  │  read-only report
                                                    └────────────────┘
                                                              │
                                                              ▼
                                                    ┌────────────────┐
                                                    │ deploy-an-app  │  live URL
                                                    └────────────────┘
```

**1. Start.** You describe the app in plain language. The agent interviews you until the nouns (what the app remembers) and the verbs (what people do) are concrete, proposes a written plan, gets your go-ahead, then builds. See [start-an-app](../concepts/start-an-app.md).

**2. Review.** The agent reads the app as it stands today and reports contradictions — security holes, discoverability files that no longer match, claims the app no longer honours. Read-only. See [review-an-app](../concepts/review-an-app.md).

**3. Deploy.** The agent provisions the database and storage, gathers every credential in one sitting, sets every variable, deploys once, then checks the live site with real commands. See [deploy-an-app](../concepts/deploy-an-app.md).

**Bonus: brand.** A full identity process from any starting point, even just a name. See [create-brand-kit](../concepts/create-brand-kit.md).

## The stack is fixed

`start-an-app` does not choose a framework. It builds Next.js, TypeScript, Tailwind, shadcn/ui, Drizzle, and Better Auth, every time. The interview chooses *within* that stack: which database, what kind of sign-in, whether there is email, uploads, payments, AI, background jobs, agent access, documentation, and how it looks.

Everything optional is a [branch](key-concepts.md). A branch that was not chosen produces no code, no dependency, and no settings tab.

## How the pieces fit together

A run of `start-an-app` goes like this. You say you want a hiking journal. The agent asks what someone does on the first visit and what brings them back, then says the data model back to you in words: *hikes — date, trail, distance, how it felt, photos; all yours, nobody else sees them.* You correct it. It asks eleven technical questions with a recommendation on each, ending with how the app should look.

It then installs the agent skills that shadcn, Vercel, Better Auth, the AI SDK, and Anthropic publish for their own libraries, and dispatches one research agent per chosen branch to find what those libraries look like *today* — because no file in this collection contains a version number.

It writes a [build sheet](key-concepts.md): the plan in plain words, including the data model, the legal call, the discoverability call, and an explicit *not in version one* list. You approve it. Only then does anything get built.

The scaffold runs in a fixed order of sixteen steps, `DESIGN.md` written second so every later screen is held to it. Then the app is made recognisably yours — real table names, real page copy, no lorem ipsum. Then the [gate](key-concepts.md): types, schema drift, build, lint, the app served in production mode, every route answering, two accounts tested against each other. Then critic agents that never saw the code read the evidence and check it against the build sheet. Then a hand-off naming every check that could not be run.

`deploy-an-app` repeats that shape on the infrastructure side. `review-an-app` repeats it on an app that already exists.

## What this is not

- **Not a code generator you can script.** Every skill has approval gates and an interview. They are written for a conversation.
- **Not framework-agnostic.** `start-an-app` builds one stack. `review-an-app` and `deploy-an-app` work best on that stack and say plainly where they meet something else.
- **Not a code review.** `review-an-app` reads the whole app as it stands, not a pending diff.
- **Not a hosting product.** `deploy-an-app` drives provider CLIs you own, under your accounts.
- **Not a test suite.** The gates run type checks, builds, and probes. They do not write unit tests.
