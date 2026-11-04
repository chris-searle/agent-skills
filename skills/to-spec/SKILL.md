---
name: to-spec
description: Turn the current conversation into a spec and publish it to the project issue tracker — no interview, just synthesis of what you've already discussed.
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec (you may know this document as a PRD). Do NOT interview the user — just synthesize what you already know.

The issue tracker should have been provided to you — run `/initialize-project-skills` if not.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

2. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

Check with the user that these seams match their expectations.

3. Write the spec using the template below, then publish it to the project issue tracker.

4. Create a spec branch for the work following the template `spec/<issue_id>/<short_slug>`

5. Commit all updated files that may exist in the docs directory (typically ADRs and notes). Commit all changes from the the CONTEXT.md files and CONTEXT-MAP.md files to this spec branch.

6. If there are any other files that have been edited but not yet committed verify with the user what should happen with the files and act accordingly.

7. Push the spec branch to the remote repository.

<spec-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Give every story a stable identifier (`US-01`, `US-02`, ...). Each user story should be in the format of:

1. **US-01.** As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. **US-01.** As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

Immediately before the list, include a `### Fulfilment tracking` table with `Status`, `User stories`, and `Evidence` columns. New stories default to **Open** unless the repository already contains verified evidence of their complete user-visible outcome. Do not mark a story fulfilled merely because preparatory configuration, types, or extension points exist. Explain this completion rule in one concise sentence above the table.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this spec.

## Further Notes

Any further notes about the feature.

</spec-template>

4. At this point you should ask the user to review the spec and confirm that it is complete and accurate. If they have any feedback, make the necessary changes and republish the spec.

5. Once the spec is approved, ask the user if they want to break it down into tickets. If they do, tell them to invoke the `/to-tickets` skill to break the spec down into tickets and publish them to the issue tracker.

Preserve story identifiers and the fulfilment table in every later spec revision. Status changes require acceptance evidence, not editorial judgement alone.
