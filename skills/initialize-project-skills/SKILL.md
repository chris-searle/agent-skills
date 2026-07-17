---
name: initialize-project-skills
description: Configure this repo for the engineering skills — set up its issue tracker and domain doc layout. Run once before first use of the other engineering skills.
disable-model-invocation: true
---

# Initialize Project Skills

Scaffold the per-repo configuration that the engineering skills assume:

- **Issue tracker** — where issues live (Local markdown by default)
- **Domain docs** — where `CONTEXT.md` and ADRs live, and the consumer rules for reading them

This is a prompt-driven skill, not a deterministic script. Explore, present what you found, confirm with the user, then write.

## Process

### 1. Explore

Look at the current repo to understand its starting state. Read whatever exists; don't assume:

- `.scratch/` — sign that a local-markdown issue tracker convention is already in use
- `git remote -v` and `.git/config` — is this a GitHub repo? Which one?
- `AGENTS.md` and `CLAUDE.md` at the repo root — does either exist? Is there already an `## Agent skills` section in either?
- `CONTEXT.md` and `CONTEXT-MAP.md` at the repo root
- `docs/adr/` and any `src/*/docs/adr/` directories
- `docs/agents/` - does this skill's prior output already exist?
- Monorepo signals - a `pnpm-workspace.yaml`, a `workspaces` field in `package.json`, a populated `packages/*` with its own `src/`, or a maven multimodule POM. Present only in a genuinely large multi-package repo; their absence means single-context, which is almost every repo.

### 2. Present findings and ask

Summarise what's present and what's missing. Then take the sections in order — one section, one answer, then the next.

Lead each section with the recommended answer so the user can accept it in a word. Give a one-line explainer only when the choice genuinely branches; skip the section entirely when exploration already settled it.

**Section A — Issue tracker.**

> Explainer: The "issue tracker" is where issues live for this repo. Skills like `to-tickets`, `to-spec`, and `qa` read from and write to it — they need to know whether to call `gh issue create`, write a markdown file under `.scratch/`, or follow some other workflow you describe. Pick the place you actually track work for this repo.

- **Local markdown** — feature specs and tickets live under `.scratch/features/`, while bug reports live under `.scratch/bugs/` in this repo (good for solo projects or repos without a remote)
- **GitHub** — issues live in the repo's GitHub Issues (uses the `gh` CLI)
- **Other** (Jira, Linear, etc.) — ask the user to describe the workflow in one paragraph; the skill will record it as freeform prose

**Section B — Domain docs.** Default to **single-context** — one `CONTEXT.md` + `docs/adr/` at the repo root. This fits almost every repo; write it without asking.

Offer **multi-context** — a root `CONTEXT-MAP.md` pointing to per-context `CONTEXT.md` files — only when exploration found monorepo signals. Then confirm which layout they want.

**Section C - Verification commands**. 

> Explainer: The engineering skills need to know how to verify the codebase. This section asks you to provide the commands to build, lint, and run tests in this repo. The skill will record them in a new skill file `.agents/verification-commands/SKILL.md` for future use.

You need to know how to verify the codebase. You need to identify the commands to perform the tasks below. Talk the user through each task and ask them to provide the command(s) for each (also suggest a recommended command if you think that you already know what the command would be based on your initial findings of the codebase - typescript projects would likely use scripts referenced in the package.json, Java Maven projects will have a pom.xml that might have provided details about the commands and test phases within surefire or failsafe etc). The tasks are:

* how to build the project
* how to lint the project (if applicable)
* how to run a single unit / component test
* how to run the full unit / component test suite
* how to run a single integration / e2e test (if applicable) - These tests may also require a specific environment to be set up, so you should ask the user to provide any necessary setup steps for running integration / e2e tests.
* how to run the full integration / e2e test suite (if applicable)
* are there any other commands that are necessary to verify the codebase that are not covered by the above tasks? Are there any other tests that are run in a different way than the above tasks? If so, please provide the commands for those tests as well.
* what is the sequence in which these verification commands should be executed to verify the codebase. For example, you may want to run linting before running tests, or run unit tests before running integration tests.

### 3. Confirm and edit

Show the user a draft of:

- The `## Agent skills` block to add to whichever of `CLAUDE.md` / `AGENTS.md` is being edited (see step 4 for selection rules)
- The contents of `docs/agents/issue-tracker.md`, and `docs/agents/domain.md`

Let them edit before writing.

### 4. Write

**Pick the file to edit:**

- If `CLAUDE.md` exists, edit it.
- If `AGENTS.md` exists, edit it.
- If both are missing, ask the user which one to create — don't pick for them.

Never create `AGENTS.md` when `CLAUDE.md` already exists (or vice versa) — always edit the one that's already there.

If an `## Agent skills` block already exists in the chosen file, update its contents in-place rather than appending a duplicate. Don't overwrite user edits to the surrounding sections.

The block:

```markdown
## Agent skills

### Issue tracker

[one-line summary of where issues are tracked]. See `docs/agents/issue-tracker.md`.

### Domain docs

[one-line summary of layout — "single-context" or "multi-context"]. See `docs/agents/domain.md`.
```

Then write the docs files using the seed templates in this skill folder as a starting point:

- [issue-tracker-local.md](./issue-tracker-local.md) — local-markdown issue tracker
- [issue-tracker-github.md](./issue-tracker-github.md) — GitHub issue tracker
- [domain.md](./domain.md) — domain doc consumer rules + layout

For "other" issue trackers, write `docs/agents/issue-tracker.md` from scratch using the user's description.

### 5. Create the verification-commands skill

Using the answers you gathered in Section C, create a new skill file `.agents/verification-commands/SKILL.md` that documents how to verify the codebase. There is an example you can reference for this purpose [verification-commands.md](./verification-commands.md). You must complete it based on the answers you received in Section C.

### 6. Done

Tell the user the setup is complete and which engineering skills will now read from these files. Mention they can edit `docs/agents/*.md` directly later — re-running this skill is only necessary if they want to switch issue trackers or restart from scratch.
