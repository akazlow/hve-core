---
name: prompt-builder
description: 'Orchestrate prompt engineering research, validation, and updates through the prompt-builder phase loop.'
argument-hint: "[promptFiles=...] [files=...] [requirements=...]"
license: MIT
user-invocable: true
---

# Prompt Builder Skill

Umbrella entry point for prompt-engineering work. Route each request to the matching specialized skill, which owns its execution. [references/orchestration.md](references/orchestration.md) is the canonical reference for the phase loop, sandbox contract, subagent dispatch matrix, artifact paths, and cleanup contract; each specialized skill mirrors it in its own self-contained references.

Follow the shared conventions in `copilot-tracking.instructions.md`.

## Goal

Understand the request, route it to the matching specialized skill, and maintain the canonical orchestration reference in [references/orchestration.md](references/orchestration.md). The routed skill runs the phase loop and owns its sandbox lifecycle, cleanup, and final response, following its own self-contained references.

## Flow

1. Identify the target prompt artifacts and the kind of work requested.
2. Route to the matching specialized skill using the delegation crosswalk.
3. Hand off to that skill, which executes the phase loop in a sandbox per the shared contract and returns its own final response.

## Delegation crosswalk

| Request                                                                   | Routed skill       |
|---------------------------------------------------------------------------|--------------------|
| Create or update a prompt artifact                                        | `/prompt-build`    |
| Apply fixes from a prior analysis                                         | `/prompt-build`    |
| Refactor, simplify, or clean up an existing artifact against requirements | `/prompt-refactor` |
| Read-only analysis or quality report with no changes                      | `/prompt-analyze`  |

When a "clean up" request is ambiguous, route substantial create-or-change work to `/prompt-build` and scoped simplification of an existing artifact to `/prompt-refactor`. The read-only `/prompt-analyze` route runs the execution and evaluation phase only.

## Inputs

* `promptFiles=...`: the prompt, instruction, agent, or skill artifacts to analyze, create, update, or refactor; infer from the current open or attached files when not provided.
* `files=...`: reference artifacts the target prompt should be able to produce, used by create or update work.
* `requirements=...`: explicit objectives or constraints, used mainly by refactor work.
* When no explicit input is given, infer the intent and targets from the conversation, attached files, or the current file.

## Success criteria

* The request is routed to the matching specialized skill: `/prompt-analyze`, `/prompt-build`, or `/prompt-refactor`.
* The routed skill completes its phase loop with the evaluation log showing no unresolved issues, or with remaining issues documented explicitly.
* The canonical reference in [references/orchestration.md](references/orchestration.md), mirrored by each routed skill's own references, defines the phase loop, sandbox naming, dispatch matrix, artifact paths, and cleanup.

## Constraints

* Keep the umbrella as the routing and shared-contract layer; do not run the phase loop or duplicate a granular skill's execution detail.
* Route each request to one specialized skill and let that skill own its sandbox lifecycle, cleanup, and final response.
* Keep [references/orchestration.md](references/orchestration.md) as the canonical reference that each routed skill mirrors in its own self-contained references.

## Stop rules

* Stop and ask when the request is too vague to route.
* Stop when the routed skill reports a blocking issue that needs user input.

## Handoff

Hand off to the routed skill, which returns the final response per its own contract: status and iteration count, the key artifacts touched, the evaluation outcome, decisions or questions surfaced, and the next recommended step. For a read-only review recommend `/prompt-analyze`; for create or update work recommend `/prompt-build`; for cleanup against requirements recommend `/prompt-refactor`.

> Brought to you by microsoft/hve-core
