# Shared design rules

Eight invariants that all four skills share. They are the architecture of this collection: the skills have no shared code, so what makes them a family is that they repeat the same rules in the same words.

Each rule below names where it appears and what failure it prevents. If you [customize a skill](../guides/customizing-references.md), these are the parts not to break.

---

## 1. A check that wasn't run is named, never claimed

**Appears in:** all four `SKILL.md` ground rules.

Saying the app does something because you wrote the code that should make it do it is recall, not verification. Where a check could not be run — no browser, no key, no domain yet — the skill names it and says what it would need.

The sentence that carries the whole rule: **the user reads silence as success.**

Each skill names its own specific temptations. `deploy-an-app` lists three: saying the environment is verified when only names were read, that the database is connected when only a page returned 200, and that webhooks work when only an endpoint was registered. `review-an-app` adds the hardest one — **never conclude that the app is secure**, because "no vulnerabilities found" will be quoted back later by someone who has stopped looking.

**Prevents:** a hand-off that reads as a pass when half of it was never tested.

---

## 2. Never write or accept a version number

**Appears in:** `start-an-app`, `deploy-an-app`, `review-an-app`.

Not in an install command, not in a `package.json` snippet, not in prose, not a Docker image tag. No file in the collection pins one, and none should ever gain one.

The reasoning is stated once and holds everywhere: **a version written into a skill file is a lie with a timestamp on it.** It goes stale in silence and builds the app against last year's API.

This is why every skill has a research step (`start-an-app` Step 2b, `deploy-an-app` Step 1) that dispatches one subagent per branch to establish what is current *today*. The versions come from the registry at run time, never from the file.

`review-an-app` extends it past packages: AI crawler user-agent tokens, advisory databases, and framework file conventions all move, and all get established at review time.

**Prevents:** an app built confidently against an API that moved.

---

## 3. Nothing deprecated, ever

**Appears in:** `start-an-app`, `deploy-an-app`.

If the current release deprecates, renames, or supersedes something a reference file uses, the replacement gets used — not the old path that "still works".

**Still working is what deprecated means.** It is a removal notice with a delay on it, and shipping onto one hands the user a rewrite they did not ask for.

**Prevents:** a build that works today and needs rewriting in six months.

---

## 4. The gate is passed by fixing the code, never by widening the gate

**Appears in:** `start-an-app` Step 6, `deploy-an-app` Step 9, and both `verify.md` and `gate.md`.

No build-error suppression, no skipping validation, and — named explicitly in both skills because it is the specific thing agents do — **never removing the migration step from the build script to make a red build go green.**

A gate that can be widened is not a gate. This is the rule that makes the other seven checkable.

**Prevents:** a green build that proves nothing.

---

## 5. Fresh eyes read evidence, not the running app

**Appears in:** `start-an-app` Step 7, `deploy-an-app` Step 10, `review-an-app` Step 3.

Every skill ends with subagents the builder cannot influence. Three constraints are shared:

- **Evidence, not access.** Several agents cannot share a port, a database, or a browser. Capture once, hand it over. A lens that can run things will spend its budget running things.
- **No agent sees another's output.** In `review-an-app` the reason is stated plainly: a lens that can see another's findings starts agreeing with them, and three agreeing agents read as corroboration when they are only an echo.
- **Two rounds, then stop and report what is left.** A third round is where an agent starts editing code it does not understand to make a report go away.

One deliberate asymmetry sits inside this rule. In `review-an-app`, each lens is *handed* its reference file as its brief. In `start-an-app`, critics are kept *away* from the files the app was built from. The difference is what the file is: build instructions given to a critic turn it back into a checklist-runner; a reviewer's brief was written for the reviewer.

**Prevents:** an agent grading its own homework.

---

## 6. Absence is not a finding; the sheet is the bar

**Appears in:** `review-an-app` ground rules, `start-an-app` Step 7.

Both skills refuse to report what a bigger, more mature app would have. `review-an-app` states the stakes: the fastest way to make the skill worthless is to let it list what a mature product does, because that report gets skimmed once and never again.

Each anchors to something concrete instead:

| Skill | The bar | Why that one |
| --- | --- | --- |
| `start-an-app` | The Step 3 build sheet | It is the one thing the user actually approved, and they approved a description of code they could not read |
| `deploy-an-app` | The Step 3 deploy sheet | Same, on the infrastructure side |
| `review-an-app` | The app's own claims | There is no sheet; the landing page, docs, and privacy policy are the spec |

Critics report divergence from the bar. They never propose a different bar.

**Prevents:** a report of generic best practices that nobody reads twice.

---

## 7. Explain it like you would to a smart friend who doesn't code

**Appears in:** `start-an-app`, `deploy-an-app` ground rules.

Say "a place to store your data" before saying "database". Say "the address people will type" before saying "domain". Introduce each technical term once, briefly, then use it normally.

This shapes the deliverables, not just the chat. The build sheet, the deploy sheet, and the review report are all written for someone who may not read code — which is what makes an approval meaningful.

**Prevents:** an approval given to jargon the user did not understand.

---

## 8. The current directory is the project root

**Appears in:** `start-an-app`, `deploy-an-app` ground rules.

Never create a subfolder for the app and never `cd` into one. The user already chose where the app goes by being there.

This is why `start-an-app` installs the six official skills globally with `-g`: `create-next-app .` refuses to run in a non-empty directory, and a project-level install would put `.claude/` and `skills-lock.json` there and force a workaround on every build.

**Prevents:** an app one folder deeper than the user expects, and a scaffold command that cannot run.

---

## Two more, in the stack rather than the process

These are narrower — they govern what `start-an-app` builds rather than how any skill behaves — but both skills repeat them, and both are worth knowing before you edit anything.

**Never `drizzle-kit push`.** Schema changes go through `db:generate`, a read of the generated SQL, then `db:migrate`. `start-an-app` bans it from the first table; `deploy-an-app` bans it harder because by then the database holds real data; `review-an-app` bans it in its fix step for the same reason.

**A tool is another caller, never a second way in.** Where the agent-access branch is built, every MCP tool goes through the same functions, the same ownership checks, and the same log as the buttons do, and takes the user from the token rather than from anything the model passed. The app gains a second front door, not a second set of rules.

---

## Related

- [Key concepts](../overview/key-concepts.md) — the vocabulary these rules use
- [Customize a skill](../guides/customizing-references.md) — how to change a skill without breaking these
- [What this is](../overview/what-is-this.md) — the mental model the rules serve
