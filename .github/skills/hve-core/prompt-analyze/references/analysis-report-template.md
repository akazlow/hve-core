---
description: "Analysis report structure and analyze-only contract for the prompt-analyze skill"
---

# Analysis Report Template

Use this structure to synthesize the evaluator findings into a concise report. The shared execution contract is centralized in the `prompt-builder` skill; this reference adds only the analyze-only scope and the report structure.

## Sandbox and dispatch contract

Derive the sandbox folder and dispatch `Prompt Tester` and `Prompt Evaluator` using the `prompt-builder` skill's sandbox contract and subagent dispatch matrix in its orchestration reference. Write the execution log and evaluation log inside that sandbox folder, and present the Analysis Report inline as the final response. The analysis stays read-only with respect to the analyzed artifacts and any file outside the sandbox, and this skill dispatches only `Prompt Tester` and `Prompt Evaluator`, never `Researcher Subagent` or `Prompt Updater`.

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
