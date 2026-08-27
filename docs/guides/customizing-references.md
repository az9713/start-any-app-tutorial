# Customize a skill

Change what a skill builds — a different auth provider, a different host, a different design rule — by editing one reference file.

## When you need this

The `SKILL.md` files hold the procedure and the ground rules. The `references/` files hold every command, package name, and piece of config. That split is deliberate: **the skill file never names a package or a version**, so swapping a component means editing one reference file, not unpicking a procedure.

Swap a provider when you already use a different one, when a provider is unavailable in your country, or when a reference file has gone stale and the skill told you at hand-off which file to fix.

## Prerequisites

- The skills installed, or the repository cloned. `npx skills add leonvanzyl/skills` installs all four.
- A text editor. Every file is Markdown.
- No build step. Skills are read at run time.

## Steps

**1. Find the file that owns the component.**

Each reference file covers exactly one thing, and its `Purpose:` line at the top says which.

| You want to change | Edit |
| --- | --- |
| The auth provider | `start-an-app/references/auth.md` |
| The database or ORM | `start-an-app/references/database.md` |
| The email provider | `start-an-app/references/email.md` |
| The payments provider | `start-an-app/references/payments.md` |
| File storage | `start-an-app/references/storage.md` |
| The AI provider | `start-an-app/references/ai.md` |
| The background-job runner | `start-an-app/references/jobs.md` |
| Design tokens and `DESIGN.md` | `start-an-app/references/design.md` |
| The gate commands | `start-an-app/references/verify.md` |
| The hosting platform | `deploy-an-app/references/deploy.md`, plus `project-and-url.md` and `env.md` |
| Production database provisioning | `deploy-an-app/references/provision-database.md` |
| What a security review looks for | `review-an-app/references/security.md` |
| What counts as drift | `review-an-app/references/drift.md` |
| Logo construction rules | `create-brand-kit/references/design.md` |
| What ships in the kit | `create-brand-kit/references/deliverables.md` |

Full lists live on each concept page: [start-an-app](../concepts/start-an-app.md), [review-an-app](../concepts/review-an-app.md), [deploy-an-app](../concepts/deploy-an-app.md), [create-brand-kit](../concepts/create-brand-kit.md).

**2. Check what else references it.**

Search the whole skill folder before editing. A provider named in one reference file is usually named in two or three more.

```bash
grep -rn "Resend" skills/skills/
```

Sign-in is the widest blast radius. Anything that changes `src/lib/auth.ts` is also touched by payments, agent access, and settings — because **Better Auth owns anything that belongs to a user**, and integrations go through its plugins rather than beside them.

**3. Edit the file.**

Keep the file's existing shape. Every reference file has the same three parts, and the skill depends on all of them:

| Part | Why it matters |
| --- | --- |
| `Last verified:` date | Step 2 of `start-an-app` uses it to size the research effort |
| `**Purpose:**` line | How the agent decides whether to load the file |
| `Verify` section | The agent completes it before moving to the next scaffold step |

Update the date when you edit. Rewrite the `Verify` section to match what you changed — a verify block checking the old provider is worse than none.

**4. Write no version numbers.**

This is the one rule that must survive your edit. No file in the collection pins a version — not in an install command, not in a `package.json` snippet, not in prose, not a Docker image tag. Every install takes the current stable release, and the research step is what establishes what that is.

```bash
# Correct
npm install better-auth

# Wrong — a lie with a timestamp on it
npm install better-auth@1.2.3
```

**5. Tell the agent which file you changed.**

For a one-off run, you can skip the edit entirely and say it in the prompt:

```text
Use the start-an-app skill, but use Clerk instead of Better Auth — swap it out in references/auth.md.
```

## Verify it worked

```bash
grep -rn "Resend" skills/skills/start-an-app/
```

No hits for the old provider means the swap is complete. Then check the file still has its three structural parts:

```bash
head -5 skills/skills/start-an-app/references/email.md
```

Expected: an H1, a `Last verified:` line, and a `**Purpose:**` line.

Run the skill on a throwaway folder before running it on something you care about.

## Troubleshooting

**The agent used the old provider anyway.** It read a different file. Run the `grep` from step 2 across the whole `skills/skills/` tree — the provider name almost certainly survives in a second reference file, or in the `description` front matter of `SKILL.md`, which is what the agent reads to decide the skill applies.

**Your edits vanished after an update.** `npx skills add` overwrites. So does `git pull` in a clone. See the next section.

**The swapped provider has no CLI.** `deploy-an-app` drives provider CLIs. A host without one cannot be automated, and the skill's `--help`-outranks-documentation rule has nothing to ask. Pick a host with a CLI, or expect to do that part by hand.

## Keeping your changes

`skills/` in this project is a clone of someone else's repository. Local edits conflict on the next pull, and a reinstall overwrites them. Three options, cheapest first:

| Option | Survives an update | Effort |
| --- | --- | --- |
| Say the change in the prompt, per run | Yes — nothing is edited | None |
| Copy the skill folder to your own skills directory under a new name | Yes | One copy |
| Fork the repository and install from your fork | Yes, and you can merge upstream | A fork |

For a fork, install from it the same way:

```bash
npx skills add <your-user>/skills
```

Browse before installing, or install one skill at a time:

```bash
npx skills add leonvanzyl/skills --list
npx skills add leonvanzyl/skills --skill start-an-app
```

## Related

- [Shared design rules](../architecture/shared-design-rules.md) — the invariants your edit must not break
- [Key concepts](../overview/key-concepts.md) — what a reference file, a brief, and a branch are
