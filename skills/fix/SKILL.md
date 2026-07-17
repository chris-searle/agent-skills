---
name: fix
description: "Fix a bug from the issue tracker."
disable-model-invocation: true
---

You should be given a bug report from the issue tracker to work on. If you have not been given a bug report, then prompt the user to do one of the 2 following steps

* If the chat context DOES NOT contain enough details to be able to confidently declare the undesired behaviour and the expected behaviour, then prompt the user to invoke the '/triage-bug' skill to get to a shared understanding of the bug first.

* If the chat context DOES contain enough details to be able to confidently declare the undesired behaviour and the expected behaviour, then prompt the user to invoke the '/to-bug-report' skill to create a bug report from the current chat context.

## Workflow

Before making any changes, create a bug branch from the main branch. Name the bug branch after the spec or ticket slug you've been asked to work on, e.g. `bug/<slug>`.

Use /tdd at all times. 
You must write regression tests asserting the expected behaviour prior to fixing.
You must prove that these tests fail before fixing the bug, and that they pass after fixing the bug.

Once done, use /code-review to review the work.

Commit your work to the bug branch.
