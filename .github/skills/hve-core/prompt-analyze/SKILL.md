---
name: prompt-analyze
description: 'Execute prompt evaluation for existing prompt artifacts and produce an analysis report without modifying files.'
argument-hint: "[promptFiles=...]"
license: MIT
user-invocable: true
---

# Prompt Analyze Skill

## Goal

Execute only Phase 1 of the prompt-builder workflow for existing prompt artifacts: run the target prompts in a sandbox, evaluate them against the Prompt Quality Criteria, and produce an Analysis Report. This skill is read-only and never modifies the analyzed artifacts or any file outside the sandbox.

## Flow

1. Confirm the target prompt file(s) and derive the sandbox context by using the deterministic contract in [references/analysis-report-template.md](references/analysis-report-template.md). Use the primary target artifact as the source for `{{topic}}`: if the target is a `SKILL.md`, use the parent folder name; otherwise use the artifact's base name with the suffix stripped (`.prompt.md`, `.instructions.md`, `.agent.md`) and convert it to kebab-case. If multiple `promptFiles` are supplied, use the lexically first entry as the primary artifact. Discover the next run number under `.copilot-tracking/sandbox/{{YYYY-MM-DD}}-{{topic}}-*` and name the sandbox `.copilot-tracking/sandbox/{{YYYY-MM-DD}}-{{topic}}-{{run-number}}`.
2. Dispatch `Prompt Tester` to execute the target prompt file(s) literally inside the sandbox and write an execution log. Provide the target prompt path(s), run number, sandbox path, the analysis purpose/requirements/expectations, and any prior run paths. When the only input is `promptFiles`, default the analysis purpose/requirements/expectations to "evaluate the target artifact(s) against the Prompt Quality Criteria."
3. Dispatch `Prompt Evaluator` to review the execution log and the target files against the Prompt Quality Criteria and write an evaluation log. Provide the target file path(s), run number, sandbox path, the execution-log path, and any prior evaluation logs.
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
* Clean up the sandbox files and folders created for this request before the final response unless the user asked to keep them.

## Handoff

If follow-up changes are needed, recommend `/prompt-build` or `/prompt-refactor` briefly, referencing the issues identified in the Analysis Report.

## Final response contract

Present the Analysis Report inline as the final response using the template structure (Purpose and Capabilities, Issues Found, Quality Assessment), then add:
* The quality outcome (a pass, or the severity-graded issues found).
* The recommended next action.

> Brought to you by microsoft/hve-core
