---
name: prompt-analyze
description: 'Execute prompt evaluation for existing prompt artifacts and produce an analysis report without modifying files.'
argument-hint: "[promptFiles=...]"
license: MIT
user-invocable: true
---

# Prompt Analyze Skill

This skill runs only the execution-and-evaluation phase (Phase 1) of the `prompt-builder` skill. Use the `prompt-builder` skill's orchestration reference for the sandbox contract, the `Prompt Tester` and `Prompt Evaluator` dispatch matrix, and the cleanup contract. This skill adds only the analyze-only scope and the Analysis Report structure in [references/analysis-report-template.md](references/analysis-report-template.md).

## Goal

Execute only Phase 1 of the `prompt-builder` skill for existing prompt artifacts: run the target prompts in a sandbox, evaluate them against the Prompt Quality Criteria, and produce an Analysis Report. This skill is read-only and never modifies the analyzed artifacts or any file outside the sandbox.

## Flow

1. Confirm the target prompt file(s) and derive the sandbox folder from the `prompt-builder` skill's sandbox contract in its orchestration reference.
2. Dispatch `Prompt Tester` to execute the target prompt file(s) literally inside the sandbox and write an execution log, following the dispatch matrix in the `prompt-builder` skill's orchestration reference. When the only input is `promptFiles`, default the analysis purpose/requirements/expectations to "evaluate the target artifact(s) against the Prompt Quality Criteria."
3. Dispatch `Prompt Evaluator` to review the execution log and the target files against the Prompt Quality Criteria and write an evaluation log, following the same dispatch matrix.
4. Read the evaluation log and synthesize the Analysis Report from the evaluator findings using [references/analysis-report-template.md](references/analysis-report-template.md). Present the Analysis Report inline as the final response. Stop after this phase and do not continue into research, build, or modification behavior.

## Inputs

* `promptFiles` (optional): Existing prompt, instruction, agent, or skill artifact(s) to analyze. If omitted, use the current open or attached file(s).

## Success criteria

* An Analysis Report is produced inline in the final response using the template structure.
* The report faithfully reflects the evaluator findings.
* The run halts after Phase 1 with no modifications to the analyzed artifacts or any file outside the sandbox.

## Constraints

* Remain read-only: never edit the target artifacts or any file outside the sandbox.
* Writing the execution log and evaluation log inside the sandbox is allowed and expected.
* Do not enter research or modification phases.
* Follow the subagent dispatch contract exactly and keep the response concise and evidence-first.

## Stop rules

* Hard stop if the target files or sandbox context cannot be determined.
* Stop if the Analysis Report cannot be produced.
* Stop after the evaluation phase completes; do not continue to later prompt-builder phases.
* Apply the `prompt-builder` skill's cleanup contract from its orchestration reference before the final response.

## Handoff

If follow-up changes are needed, recommend `/prompt-builder` or `/prompt-refactor` briefly, referencing the issues identified in the Analysis Report.

## Final response contract

Present the Analysis Report inline as the final response using the template structure (Purpose and Capabilities, Issues Found, Quality Assessment), then add:
* The quality outcome (a pass, or the severity-graded issues found).
* The recommended next action.

> Brought to you by microsoft/hve-core
