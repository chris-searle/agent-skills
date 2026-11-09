---
name: code-review
description: Review changes since a fixed point across Standards, Spec, and ADR compliance. Runs separate reviews in parallel sub-agents and reports each axis separately. Use when the user wants to review a branch, a PR, work-in-progress changes, or asks to "review since X".
---

Three-axis review of the diff between `HEAD` and a fixed point the user supplies:

- **Standards** — does the code conform to this repo's documented coding standards?
- **Spec** — does the code faithfully implement the originating issue / PRD / spec / bug report? This includes **story traceability**: whether the ticket's claimed `Fulfils`/`Contributes to` relationships match the observable implementation and whether the spec's fulfilment table is accurate.
- **ADRs** — does the implementation comply with the project's applicable architectural decision records?

Each axis runs as a separate **parallel sub-agent**, then this skill aggregates their findings.

The issue tracker should have been provided to you — run `/initialize-project-skills` if `docs/agents/issue-tracker.md` is missing.

## Process

### 1. Pin the fixed point

Whatever the user said is the fixed point — a commit SHA, branch name, tag, `main`, `HEAD~5`, etc. If they didn't specify one, ask for it.

Capture the diff command once: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base). Also note the list of commits via `git log <fixed-point>..HEAD --oneline`.

Before going further, confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. Stop here for a bad ref or empty diff.

### 2. Identify the spec source

Look for the originating spec, in this order:

1. Issue references in the commit messages (`#123`, `Closes #45`, GitLab `!67`, etc.) — fetch via the workflow in `docs/agents/issue-tracker.md`.
2. A path the user passed as an argument.
3. A PRD/spec file under `docs/`, `specs/`, or `.scratch/` matching the branch name or feature.
4. If nothing is found, ask the user where the spec is. If they say there isn't one, the **Spec** sub-agent will skip and report "no spec available".

### 3. Identify the standards sources

Anything in the repo that documents how code should be written, such as `CODING_STANDARDS.md` or `CONTRIBUTING.md`.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** below — a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation — and, like any standard here, skip anything tooling already enforces.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name** — a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code** — the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy** — a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps** — the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession** — a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches** — the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery** — one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change** — one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Speculative Generality** — abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Message Chains** — long `a.b().c().d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man** — a class or function that mostly just delegates onward. → cut it, call the real target direct.
- **Refused Bequest** — a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

### 4. Identify the ADR sources

Read `docs/agents/domain.md` and the domain-doc guidance in `AGENTS.md` or `CLAUDE.md`, when present, for project-specific locations. These take precedence over the defaults below.

The `/initialize-project-skills` layout and `/domain-modeling`, reached through `/new-feature`, use:

- `docs/adr/` at the repo root for project-wide decisions.
- `src/<context>/docs/adr/` for context-specific decisions in multi-context repos.
- Root `CONTEXT-MAP.md` to locate contexts. Follow its paths to the relevant `CONTEXT.md` files and adjacent `docs/adr/` directories, including contexts outside `src/`.

Check both project-wide and affected-context decisions. Follow any ADR indexes and links in these docs, and search repository paths for ADRs if the project uses another layout. `/to-spec` also expects existing ADRs to be respected but defines no additional location.

Pass the discovered locations and relevant records to the ADR sub-agent. Missing domain docs or ADR directories are allowed. Do not create them or flag their absence as a defect.

### 5. Spawn the sub-agents in parallel

**Standards sub-agent prompt** — include:

- The full diff command and commit list.
- The list of standards-source files you found in step 3, **plus the smell baseline from step 3** pasted in full — the sub-agent has no other access to it.
- The brief: "Report — per file/hunk where relevant — (a) every place the diff violates a documented standard: cite the standard (file + the rule); and (b) any baseline smell you spot: name it and quote the hunk. Distinguish hard violations from judgement calls — documented-standard breaches can be hard, but baseline smells are always judgement calls, and a documented repo standard overrides the baseline. Skip anything tooling enforces. Under 400 words."

**Spec sub-agent prompt** — include:

- The diff command and commit list.
- The path or fetched contents of the spec.
- The brief: "Report: (a) requirements the spec asked for that are missing or partial; (b) behaviour in the diff that wasn't asked for (scope creep); (c) requirements that look implemented but where the implementation looks wrong; and (d) for every user story named by the ticket, whether the diff proves it Fulfilled, only Contributes, or does not support the claim. Check the spec's fulfilment table for stale or premature status. A story is Fulfilled only when its complete user-visible outcome is implemented and verified; preparatory configuration or types are not enough. Quote the spec or ticket line for each finding. Under 500 words."

If the spec is missing, skip the Spec sub-agent and note this in the final report.

**ADR sub-agent prompt** — include:

- The diff command and commit list.
- The ADR discovery guidance from step 4, the locations searched, and paths or contents of discovered ADRs and indexes.
- The brief: "Verify the implementation against applicable project-wide and context-specific ADRs. Read each record's scope, status, decision, and any superseding records before treating it as binding. Do not enforce proposed, rejected, or superseded decisions as current requirements. For each conflict introduced by the diff, cite the ADR path and decision text, the changed code location, and why the implementation conflicts. Inspect surrounding code and tests where needed to verify behaviour. If the diff changes an ADR, explain the changed decision and whether the implementation follows it; surface any unresolved conflict with an existing decision. Distinguish confirmed conflicts from ambiguities that need clarification. List the applicable ADRs checked and any limits to verification. If no ADRs exist or none apply, report that explicitly without treating it as a defect or claiming compliance. Review only; do not edit code or ADRs. Under 500 words."

### 6. Aggregate

Present the reports under `## Standards`, `## Spec`, and `## ADRs` headings, verbatim or lightly cleaned. Keep findings separate by axis without merging or reranking them. Include skipped or not-applicable outcomes and verification limits.

Within `## Spec`, include a compact `Story traceability` subsection listing each ticket-linked `US-nn` as **Fulfilled**, **Contributes**, or **Unsupported**, with the acceptance evidence. Report the exact fulfilment-table update warranted by the review. Code review is read-only: do not edit the spec or tracker unless the user separately requested that mutation.

End with a one-line summary of total findings per axis and the worst issue within each axis, if any. Distinguish a skipped or not-applicable review from a review that found no conflicts.

## Why separate axes

A change can pass one axis and fail the other:

- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the issue asked but breaks the project's conventions → **Spec pass, Standards fail.**
- Code that meets the spec and standards but contradicts an applicable architectural decision → **Standards and Spec pass, ADRs fail.**

Reporting them separately stops one axis from masking the other.
