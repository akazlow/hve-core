---
name: prompt-build
description: "Create or update prompt artifacts through the full prompt-builder execution, evaluation, research, and modification loop."
argument-hint: "[files=...] [promptFiles=...]"
license: MIT
user-invocable: true
---

# Prompt Build Skill

Use [references/build-loop.md](references/build-loop.md) for the full phase loop, sandbox continuity contract, subagent dispatch matrix, and cleanup gate.

## Goal

Create or update prompt-engineering artifacts through the full prompt-builder execution, evaluation, research, and modification loop until the evaluation log shows no remaining issues. Use this skill for new prompt artifacts, improvements, cleanup, and related instruction updates.

## Flow

1. Confirm the target prompt artifacts and any reference files. Infer the primary artifact and derive the sandbox topic as kebab-case from it using the deterministic contract in [references/build-loop.md](references/build-loop.md), then choose the next run number under `.copilot-tracking/sandbox/{{YYYY-MM-DD}}-{{topic}}-*`.
2. When the target prompt files already exist, run the execution and evaluation phase (dispatch `Prompt Tester`, then `Prompt Evaluator`) to establish their current state and inspect the evaluation log; when that baseline shows no unresolved issues, skip to the final response. When the target files do not exist yet, skip to step 3.
3. Research: create or update the primary research artifact at `.copilot-tracking/research/{{YYYY-MM-DD}}/{{topic}}-research.md`, and delegate research to `Researcher Subagent` when the topics are independent.
4. Modify: dispatch `Prompt Updater` to create or update the prompt files and related instruction files from the evaluation findings and research, then review the updater tracking.
5. Run the execution and evaluation phase, then repeat steps 3-5 until the evaluation log shows no unresolved issues or until the remaining issues are documented explicitly.

## Inputs

* `files`: Optional reference files that help define the target prompt artifacts.
* `promptFiles`: Optional prompt or instruction artifacts to create or modify. When these inputs are incomplete, infer them from the current open or attached files and the conversation context.
* When `files` or `promptFiles` are supplied without explicit requirements, identify the related instruction file(s), create or update the instruction and prompt artifacts so they can produce the target files, and improve and clean up the prompt files.

## Success criteria

* The requested prompt artifacts or related instruction files exist or were updated.
* The artifacts meet the stated requirements and prompt-builder quality criteria.
* The evaluation loop completed with no unresolved issues, or any remaining issues are documented explicitly.

## Constraints

* Keep sandbox edits inside the assigned sandbox folder and reuse prior runs for continuity.
* Do not skip the evaluator step or finalize early.
* Maintain the repository's prompt-builder quality standard.
* Clean up the sandbox files and folders created for this request before the final response unless the user asked to keep them.
* When the request is too vague to act on safely, pause and ask for clarification before proceeding.

## Stop rules

* Stop after the loop completes when the targets meet the requirements and evaluation is complete.
* Re-enter the loop when the evaluator identifies outstanding issues.
* Hard stop and ask for clarification when the target artifacts or intent are too ambiguous to create or update safely.

## Handoff

After the build loop completes, hand off to `/prompt-analyze` for a deeper read-only review when more evaluation coverage is useful, or to `/prompt-refactor` when the work is primarily cleanup-focused.

## Final response contract

Return a concise summary that includes the artifacts changed, the evaluation status and iteration count, the key decisions or issues surfaced, and the next recommended step.

> Brought to you by microsoft/hve-core
