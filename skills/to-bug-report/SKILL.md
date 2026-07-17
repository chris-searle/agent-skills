---
name: to-bug-report
description: "Create and publish a bug report from the current chat context"
disable-model-invocation: true
---

# To Bug Report

Turn the current conversation and any relevant codebase evidence into a concise,
actionable bug report, then publish it to the configured project issue tracker.
Do not interview the user: synthesize what is already known. The issue tracker
should have been provided to you; run `/initialize-project-skills` if it has not.

## Process

1. Work from the current conversation. If it identifies relevant code, tests,
	logs, or a reproducible workflow, inspect only the evidence needed to make
	the report accurate. Use the project's domain glossary vocabulary and respect
	relevant ADRs.

2. Separate confirmed observations from hypotheses. Do not invent reproduction
	steps, environments, impact, or a root cause. State an unknown explicitly;
	omit sections that have no useful information.

3. Write the report with the template below, then publish it to the issue
	tracker. For the local Markdown tracker, use the next available filename at
	`.scratch/bugs/<NN>-<bug-slug>.md`, numbered from `01`.

<bug-report-template>

# <Bug title>

## Summary

A concise statement of the unintended behaviour and the affected actor or workflow.

## Observed Behaviour

What actually happens. Include exact errors or visible results when known.

## Expected Behaviour

What should happen instead.

## Reproduction

1. A deterministic step, when known.
2. The next step.
3. The observed result.

## Impact

Who or what is affected, the severity or frequency when known, and any workaround.

## Evidence

Relevant logs, test results, screenshots, links, or source observations.

## Suspected Cause

An explicitly labelled hypothesis, only when one is supported by the available evidence.

## Acceptance Criteria

- [ ] A regression test demonstrates the reported behaviour where practical.
- [ ] The documented reproduction results in the expected behaviour.
- [ ] No regression is introduced to the affected workflow.

</bug-report-template>

4. Tell the user where the report was published and ask them to review it for
accuracy. Apply any requested corrections to the published report.

