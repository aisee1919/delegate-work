---
name: delegate-work
description: Use when the user wants Codex to reduce its own quota usage by outsourcing simple, bounded subtasks to local helper agents without lowering final task quality.
---

# Delegate Work

## Contract

Use local helper agents to save Codex quota. Helper-agent token/API cost is irrelevant unless the user says otherwise.

Codex still owns scope, safety, review, integration, verification, and final claims. Helper output is candidate work only. Final quality must match the standard expected if Codex had done the work directly.

## Delegate When

Delegate aggressively when a bounded subtask would make Codex spend many tokens on reading, searching, summarizing, boilerplate, first-pass implementation, narrow comparison, or mechanical edits.

Do not delegate unclear requirements, architecture/product/API/data/security decisions, secrets, destructive operations, dependency/environment mutation, final verification, or final user-facing conclusions.

Default to analysis-only delegation. Allow edits only for explicitly named files, small isolated changes, and inspectable diffs.

## Hard Gates

Delegate only if every gate passes:

- **Quality**: Codex can verify without trusting the helper blindly.
- **Scope**: files, directories, commands, or output boundaries are explicit.
- **Risk**: mistakes are catchable by diff, tests, static checks, or direct inspection.
- **Decision**: no unresolved product, architecture, API, data, security, privacy, persistence, migration, or data-loss decision.
- **Privacy**: no secrets, credentials, private data, or sensitive material unless the user approved exposure.
- **State**: write scopes are explicit and do not overlap with active Codex/user edits.
- **Quota**: expected Codex savings exceed prompt + output + review cost.

If any gate fails, Codex does that part directly.

## Quota Rules

- Prefer delegation when helper work would cost at least 3x the Codex cost of prompting and reviewing it.
- Ask for concise findings, paths, line numbers, changed-file lists, and short rationales.
- Default output limit: 200 words unless the task needs more.
- Use explicit command timeouts; abort stuck helpers and continue directly.
- Ask for summaries first; inspect details only when needed.
- Stop delegating if outputs become vague, verbose, or harder to review than direct work.

## Parallel Delegation

Use multiple helpers in parallel only for independent subtasks with non-overlapping write scopes. For Claude Code, this can mean multiple independent `claude -p` calls.

Do not parallelize dependent work, shared write scopes, secret/sensitive-data tasks, or work that would force Codex to resolve conflicting product/architecture decisions. Codex must merge results, reject conflicts, and verify the final integrated work.

## Workflow

1. Split the task into Codex-owned judgment work and helper-owned bounded work.
2. Apply the hard gates and quota rules.
3. Send a narrow prompt with exact task, scope, forbidden actions, output limit, expected format, edit permission, and known checks.
4. Before helper edits, note the current diff/status. Review the smallest sufficient evidence; never skip necessary verification.
5. Accept, discard, or redo helper work. Retry once with a narrower prompt if useful; then continue directly.

For Claude Code invocation issues in Codex desktop, read `references/claude-code.md` before installing or debugging npm.

## Prompt Template

```text
You are a helper agent running under Codex supervision.

Goal:
<one bounded subtask>

Scope:
- Allowed files/directories: <paths>
- Do not edit outside this scope.
- Do not commit, push, install dependencies, or run destructive commands.
- Do not make architecture, product, security, or data-loss decisions.

Output limit:
- <=200 words unless explicitly allowed.
- Prefer file paths, line numbers, changed-file list, and short rationale.
- Do not paste large files.

Expected output:
- Analysis: concise findings with file references.
- Edits: minimal change plus changed-file list.
- Uncertainty: stop and state the blocker.
```

## Acceptance

Before using helper work, verify:

- scope was respected
- result matches the delegated task
- diff/findings are coherent
- cited findings were spot-checked; edits passed targeted diff review
- relevant tests, checks, or direct inspection pass
- failed helper edits are discarded or reverted without touching unrelated user changes
- no hidden uncertainty or unexplained broadening
