---
description: "Analysis report structure and analyze-only contract for the prompt-analyze skill"
---

# Analysis Report Template

Use this structure to synthesize the evaluator findings into a concise report and to keep the shared analyze-only contract deterministic.

## Deterministic sandbox contract

* Use today's date as `{{YYYY-MM-DD}}`.
* If multiple `promptFiles` are supplied, use the lexically first entry as the primary artifact.
* Derive `{{topic}}` from the primary target artifact as follows: if the target is `SKILL.md`, use the parent folder name; otherwise use the artifact's base name with the suffix stripped (`.prompt.md`, `.instructions.md`, `.agent.md`) and convert it to kebab-case.
* Discover the next run number under `.copilot-tracking/sandbox/{{YYYY-MM-DD}}-{{topic}}-*` and name the sandbox `.copilot-tracking/sandbox/{{YYYY-MM-DD}}-{{topic}}-{{run-number}}`.
* Write the execution log and evaluation log inside that sandbox folder, and present the Analysis Report inline as the final response. The analysis must remain read-only with respect to the analyzed artifacts and any file outside the sandbox.

## Prompt Tester and Prompt Evaluator dispatch contract

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

This skill does not dispatch `Researcher Subagent` or `Prompt Updater`.

## Analysis Report Template

## Purpose and Capabilities

* State the prompt's purpose in one sentence.
* List the workflow type and key capabilities.
* Describe the protocol structure if present.

## Issues Found

Group issues by severity, starting with Critical, then High, then Medium, then Low.

* Severity: Critical
  * Category:
  * Description:
  * Suggested fix:
* Severity: High
  * Category:
  * Description:
  * Suggested fix:
* Severity: Medium
  * Category:
  * Description:
  * Suggested fix:
* Severity: Low
  * Category:
  * Description:
  * Suggested fix:

When issues are found, highlight the most impactful items first and include a count by severity.

## Quality Assessment

Summarize which Prompt Quality Criteria passed and which failed, and note any patterns of concern across multiple criteria.

If no issues are found, include this exact line:

✅ Quality Assessment Passed - This prompt meets all Prompt Quality Criteria.
