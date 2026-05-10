---
name: delegate-work
description: Use when the user wants Codex to reduce its own quota usage by outsourcing simple, bounded subtasks to local helper agents without lowering final task quality.
---

# Delegate Work

## Contract

Use local helper agents to save Codex quota. Helper cost is irrelevant unless the user says otherwise. Codex owns scope, safety, review, integration, verification, and final claims. Helper output is candidate work only; final quality must match Codex-direct work.

## Delegate Or Direct

Delegate bounded work that would make Codex spend many tokens on reading, searching, summarizing, boilerplate, first-pass implementation, narrow comparison, or mechanical edits. Default to analysis-only.

Do direct work for simple Q&A, single-command checks, tiny one-file edits, obvious typo fixes, short explanations, final judgment calls, unclear requirements, architecture/product/API/data/security decisions, secrets, destructive operations, dependency/environment mutation, final verification, or final user-facing conclusions.

Use direct work unless delegation plausibly saves at least 1.5x Codex cost. Treat 1.5x as met when Codex would inspect multiple files, read >100 lines, summarize logs, compare options, or draft boilerplate. If plausible but unclear, delegate analysis-only with a <=100-word output cap.

## Gates

Delegate only if all pass:

- **Quality**: Codex can verify without trusting blindly.
- **Scope**: files, directories, commands, or output boundaries are explicit.
- **Risk**: mistakes are catchable by diff, tests, static checks, or direct inspection.
- **Decision**: no unresolved product, architecture, API, data, security, privacy, persistence, migration, or data-loss decision.
- **Privacy**: no secrets, credentials, private data, or sensitive material unless approved.
- **State**: write scopes do not overlap with active Codex/user edits.
- **Quota**: savings plausibly exceed prompt + output + review cost.

If any gate fails, do it directly.

## Quota And Cleanup

- Estimate review cost as prompt + helper output + files/diffs Codex must inspect.
- Ask for paths, line numbers, changed-file lists, and short rationales.
- Default output limit: 200 words; use summaries first and inspect details only when needed.
- Use explicit timeouts; abort stuck helpers and continue directly.
- Close, kill, or release helper agents/processes after completion, failure, or timeout.
- Stop delegating if outputs are vague, verbose, harder to review than direct work, or the same helper/subtask type fails twice.

## Parallel

Use multiple helpers only for independent subtasks with non-overlapping write scopes. For Claude Code, this can mean multiple `claude -p` calls.

Do not parallelize dependent work, shared write scopes, secret/sensitive-data tasks, or work that would force Codex to resolve conflicting product/architecture decisions. Give each helper its own timeout; use partial good results and drop slow/conflicting ones. Codex merges, rejects conflicts, and verifies final integrated work.

## Workflow

1. Split Codex-owned judgment work from helper-owned bounded work.
2. Apply gates and quota rules.
3. Send a narrow prompt with exact task, scope, forbidden actions, output limit, expected format, and known checks. Allow edits only for explicitly named files, small isolated changes, and inspectable diffs.
4. Before helper edits, note current diff/status. Review the smallest sufficient evidence; never skip necessary verification.
5. Accept, discard, or redo helper work. Retry once with a narrower prompt if useful; then continue directly.

When helpers are used, tell the user briefly: `Delegated: <task>; accepted/rejected: <result>.` Do not hide delegation or narrate routine helper mechanics.

For Claude Code invocation issues in Codex desktop, read this skill folder's `references/claude-code.md` before installing or debugging npm.

## Prompt Template

```text
You are a helper agent running under Codex supervision.
Goal: <one bounded subtask>
Scope: allowed <paths>; do not edit outside scope; do not commit, push, install dependencies, run destructive commands, or make architecture/product/security/data-loss decisions.
Output: <=200 words unless allowed; prefer paths, line numbers, changed-file list, short rationale; do not paste large files.
If unsure, stop and state the blocker.
```

## Acceptance

Before using helper work, verify:

- scope, task match, and diff/findings are coherent
- cited findings were spot-checked; edits passed targeted diff review
- relevant `git diff -- <files>`, tests, lint/type checks, or direct inspection pass
- behavior changes prefer automated tests/lint/type checks; otherwise use focused direct behavior inspection
- failed helper edits are discarded or only helper-owned changes are reverted, preserving unrelated user/Codex work
- no hidden uncertainty or unexplained broadening
