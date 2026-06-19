---
description: "Phase loop, sandbox contract, and subagent dispatch matrix for the prompt-build skill"
---

# Prompt Build Loop Reference

Use this reference to keep the full build loop, sandbox contract, subagent dispatch matrix, artifact paths, and cleanup gate available during execution.

## Phase loop and the return-to-Phase-1 behavior

1. Execution and evaluation: dispatch `Prompt Tester`, then `Prompt Evaluator` in a sandbox folder, and inspect the evaluation log.
2. Research: create or update the primary research artifact and dispatch `Researcher Subagent` when the research topics are independent. Consolidate findings into the primary research document and finalize it before the modification phase.
3. Modifications: dispatch `Prompt Updater` to create or modify the prompt files and related instruction files when the prompt files are independent, review the updater tracking file, and return to the execution and evaluation phase.

Repeat the loop until the evaluation log shows no remaining issues. If the target prompt files do not yet exist, move to Phase 2 first for research and Phase 3 for creation before returning to Phase 1. Only dispatch `Prompt Tester` once the target files exist. If the evaluation log still contains findings, continue from the earliest affected phase instead of finalizing early.

## Sandbox contract and cross-run continuity

* Sandbox root: `.copilot-tracking/sandbox/`.
* Folder name pattern: `{{YYYY-MM-DD}}-{{topic}}-{{run-number}}`.
* Use today's date as `{{YYYY-MM-DD}}`.
* When multiple target prompt files are supplied, use the lexically first entry as the primary artifact.
* Derive `{{topic}}` from the primary target artifact: if the target is a `SKILL.md`, use the parent folder name; otherwise use the artifact's base name with the suffix stripped (`.prompt.md`, `.instructions.md`, `.agent.md`) and convert it to kebab-case.
* Discover the next run number by inspecting existing `.copilot-tracking/sandbox/{{YYYY-MM-DD}}-{{topic}}-*` folders and choosing the next available `-001`, `-002`, and so on.
* Keep all sandbox edits inside the assigned sandbox folder and reuse prior runs for continuity across iterations.
* Mirror the relevant target folder structure under the sandbox when a phase is sandbox-constrained so execution logs, research notes, and prompt updates can be compared across runs.
* Keep real source edits outside the sandbox only when the modification phase intentionally changes the target artifacts.

## Subagent dispatch matrix

Provide each subagent the inputs below and record the outputs in the relevant artifact so later iterations can continue from the last confirmed state.

### Prompt Tester

Inputs:

* Target prompt file paths.
* Run number for the current iteration.
* Purpose, requirements, expectations, and user-provided details relevant to the target prompt files.
* Sandbox folder path under `.copilot-tracking/sandbox/` using the `{{YYYY-MM-DD}}-{{topic}}-{{run-number}}` pattern.
* Optional test scenarios or prior sandbox run paths when iterating.

Outputs:

* Sandbox folder path.
* Execution log path.
* Execution status.
* Literal execution findings.
* Clarifying questions.

### Prompt Evaluator

Inputs:

* Target prompt file paths.
* Run number for the current iteration.
* Sandbox folder path from the tester run.
* Execution log path from the prior test run.
* Optional prior evaluation log paths when iterating.

Outputs:

* Evaluation log path.
* Evaluation status.
* Severity-graded checklist.
* Clarifying questions.

### Researcher Subagent

Inputs:

* Research topic or question.
* Research artifact path to create or update.
* Optional compact task brief or delegation context.

Outputs:

* Research artifact path.
* Research status.
* Key findings.
* Suggested next research.
* Clarifying questions.

### Prompt Updater

Inputs:

* Detailed purpose, requirements, expectations, and user-provided details for prompt files.
* Prompt updater tracking path under `.copilot-tracking/prompts/{{YYYY-MM-DD}}/{{prompt-filename}}-updates.md`.
* Optional target prompt files to create or modify.
* Optional current sandbox folder path containing the evaluation log.
* Optional evaluation log path.
* Optional specific findings or modifications from the evaluation log to implement.

Outputs:

* Updater tracking path.
* Changed prompt file paths.
* Related file paths.
* Modification status.
* Outstanding checklist.
* Clarifying questions.

## Artifact paths

* Primary research artifact: `.copilot-tracking/research/{{YYYY-MM-DD}}/{{topic}}-research.md`.
* Subagent research artifact: `.copilot-tracking/research/subagents/{{YYYY-MM-DD}}/{{topic}}-research.md`.
* Prompt updater tracking artifact: `.copilot-tracking/prompts/{{YYYY-MM-DD}}/{{prompt-filename}}-updates.md`.
* Sandbox root: `.copilot-tracking/sandbox/`.

## Cleanup gate

* Clean up all sandbox folders and files created for this request before the final response, unless the user explicitly asked to keep the sandbox artifacts.
* Do not return the final response until the cleanup pass is complete.
* If the request is too vague to act on safely, stop and ask for clarification rather than creating low-confidence artifacts.

## User conversation expectations

* Announce the current phase before starting work.
* Summarize outcomes when a phase completes and explain how the next phase will proceed.
* Surface important findings and clarifying questions as work unfolds instead of operating silently.
* Keep the summary focused on the key outcomes and the next step.
