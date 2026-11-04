---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

Before implementation, read the ticket's user-story coverage and the source spec's fulfilment table. Treat `Fulfils` as an acceptance claim to prove end to end; treat `Contributes to` as traceability only, not permission to mark the story complete.
Before implementation, read the tickets parent ticket to understand the broader context and dependencies.

If your ticket has a parent ticket then ensure there is a spec branch named `spec/<parent_ticket_issue_id>/<short_slug>`.
All subtickets should be implemented on feature branches named after the parent ticket's slug that target the parent ticket's spec branch.

If you have been asked to work on a bug report then stop and instead prompt the user to invoke the '/fix' skill.

Before making any changes, create a feature branch from the parent ticket's spec branch if there is a parent ticket, otherwise from the main branch. Name the feature branch after the spec or ticket slug you've been asked to work on, e.g. `feature/<issue_id>/<short_slug>`.

Use /tdd at all times, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, use /code-review to review the work.

After tests and code review pass, update user-story progress in the source spec:

- Mark a ticket-linked story **Fulfilled** only when this implementation proves its complete user-visible outcome.
- Leave partial stories **Open** and record the completed ticket as contribution evidence.
- Include ticket/issue and test or acceptance evidence; never infer completion from preparatory configuration alone.
- If the spec cannot be edited, report the exact status/evidence update the user should make.

Commit your work to the feature branch.
