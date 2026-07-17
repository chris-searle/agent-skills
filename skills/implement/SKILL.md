---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

If you have been asked to work on a bug report then stop and instead prompt the user to invoke the '/fix' skill.

Before making any changes, create a feature branch from the main branch. Name the feature branch after the spec or ticket slug you've been asked to work on, e.g. `feature/<slug>`.

Use /tdd at all times, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, use /code-review to review the work.

Commit your work to the feature branch.
