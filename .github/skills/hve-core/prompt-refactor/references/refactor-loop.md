---
description: "Sandbox contract and subagent dispatch matrix for the prompt-refactor skill"
---

# Prompt Refactor Loop Reference

Use this reference to keep the refactor loop, sandbox contract, subagent dispatch matrix, and cleanup gate available during execution.

## Deterministic sandbox contract

* Use today's date as `{{YYYY-MM-DD}}`.
* Derive `{{topic}}` from the primary target artifact: if the target is a `SKILL.md`, use the parent folder name; otherwise use the artifact's base name with the suffix stripped (`.prompt.md`, `.instructions.md`, `.agent.md`) and convert it to kebab-case.
* If multiple `promptFiles` are supplied, use the lexically first entry as the primary artifact.
* Discover the next run number under `.copilot-tracking/sandbox/{{YYYY-MM-DD}}-{{topic}}-*` and name the sandbox `.copilot-tracking/sandbox/{{YYYY-MM-DD}}-{{topic}}-{{run-number}}`.
* Keep execution logs and scratch inside the sandbox folder; the refactor edits themselves are applied by `Prompt Updater` to the real target artifacts outside the sandbox.

## Subagent dispatch matrix

### Prompt Tester

Inputs:

* target prompt path(s)
* run number
* sandbox path
* purpose/requirements/expectations
* optional prior run paths

Outputs:

* sandbox path
* execution-log path
* status
* literal findings
* questions

### Prompt Evaluator

Inputs:

* target file path(s)
* run number
* sandbox path
* execution-log path
* optional prior evaluation-log paths

Outputs:

* evaluation-log path
* status
* severity-graded checklist
* questions

### Prompt Updater

Inputs:

* requirements/objectives
* tracking path
* optional target files
* optional sandbox path and evaluation-log path
* optional findings to implement

Outputs:

* tracking path
* changed paths
* related paths
* status
* checklist
* questions

## Cleanup gate

* Clean up the sandbox files and folders created for this request before the final response unless the user asked to keep them.
* Do not return the final response until the cleanup pass is complete.
