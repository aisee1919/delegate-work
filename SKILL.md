---
name: delegate-work
description: Use when the user has a standing preference to reduce Codex quota by outsourcing bounded subtasks to local helper agents, or when a task can benefit from helper delegation without lowering final quality.
---

# Delegate Work

## Contract

Use local helper agents by default when they are capable of producing compact, reviewable output for bounded subtasks. Helper cost is irrelevant unless the user says otherwise. Codex owns scope, safety, final acceptance, and final claims. Helper output is candidate work; final quality must match Codex-direct work.

## Delegate Or Direct

Delegate bounded work that would make Codex spend many tokens on reading, searching, summarizing, boilerplate, first-pass implementation, quality checks, narrow comparison, or mechanical edits. Default to analysis-only, but allow implementation and quality-check subtasks when the helper model is capable and scope is explicit.

Do direct work for simple Q&A, single-command checks, tiny one-file edits, obvious typo fixes, short explanations, final judgment calls, unclear requirements, architecture/product/API/data/security decisions, secrets, destructive operations, dependency/environment mutation, final verification, or final user-facing conclusions.

Do not wait for the user to name this skill again. Treat the user's standing preference as authorization to consider delegation, then apply the gates below.

Use direct work unless the helper is likely capable of producing compact, reviewable evidence for the bounded task. If capability is plausible but uncertain, delegate analysis-only with a <=100-word output cap.

## Gates

Delegate only if all pass:

- **Quality**: Codex can verify without trusting blindly.
- **Scope**: files, directories, commands, or output boundaries are explicit.
- **Risk**: mistakes are catchable by diff, tests, static checks, or direct inspection.
- **Decision**: no unresolved product, architecture, API, data, security, privacy, persistence, migration, or data-loss decision.
- **Privacy**: no secrets, credentials, private data, or sensitive material unless approved.
- **State**: write scopes do not overlap with active Codex/user edits.
- **Capability**: the helper can likely complete the bounded task, including delegated QA when useful, and return outcome evidence Codex can review cheaply.

If any gate fails, do it directly.

## Helper Discipline

- Prefer delegation when the helper is well-suited to the task, such as multi-file search, >100 lines of reading, log summary, option comparison, or boilerplate draft.
- Avoid delegation when helper output would be harder to verify than direct work.
- Helpers may perform implementation self-review, edge-case search, static issue scan, diff summary, test suggestions, and verification-command recommendations.
- Allow edits only for explicit files/symbols and inspectable diffs; allow code-level global symbol changes only when the exact file, symbol, and intended behavior are specified.
- Do not let helpers change persistent environment variables, system settings, dependency state, git state, or global tool configuration.
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
2. Apply gates and helper discipline.
3. Send a narrow prompt with exact task, scope, forbidden actions, output limit, expected format, known checks, and allowed edit/symbol scope.
4. Before helper edits, note current diff/status. Use outcome-first review: completion evidence, changed-file list, scope, checks, and user-visible behavior before detailed diffs.
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

- scope, task match, changed-file list, and outcome evidence are coherent
- helper QA evidence is plausible; inspect details only if evidence is weak, checks fail, scope drifts, or risk is high
- relevant `git diff -- <files>`, tests, lint/type checks, or direct inspection pass
- behavior changes prefer automated tests/lint/type checks; otherwise use focused direct behavior inspection
- failed helper edits are discarded or only helper-owned changes are reverted, preserving unrelated user/Codex work
- no hidden uncertainty or unexplained broadening
