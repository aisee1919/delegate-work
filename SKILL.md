---
name: delegate-work
description: Use when the user has a standing preference to reduce Codex quota by outsourcing bounded subtasks to local helper agents, or when a task can benefit from helper delegation without lowering final quality.
---

# Delegate Work

## Contract

Codex acts as the brain, not the laborer: define the approach, split tasks, assign bounded execution and QA to capable helpers, then review outcomes. Helper cost is irrelevant unless the user says otherwise. Codex owns scope, safety, final acceptance, and final claims. Helper output is candidate work; final quality must match Codex-direct work.

## Manager Mode

For any non-trivial task, Codex should first create a short task list: plan/decision work kept by Codex, execution/QA work assigned to helpers, and acceptance evidence required from each helper. Default posture: Codex plans and verifies; helpers read, search, draft, implement, compare, debug, summarize, and self-review.

Delegate bounded work whenever it should reduce Codex quota versus direct work, especially multi-file reading, repo search, log review, boilerplate, first-pass implementation, test/lint triage, option comparison, edge-case analysis, documentation extraction, and mechanical edits. Default to analysis-only only when helper capability or scope is uncertain.

Do not wait for the user to name this skill again. Treat the user's standing preference as authorization to consider delegation, then apply the gates below.

Codex executes directly only when direct work is cheaper than delegation, when a tool/action is available only to Codex, when the task is too small to recover helper overhead, or when helper delivery fails the gates/acceptance. For unclear requirements, architecture/API/data/security/privacy/persistence/migration decisions, secrets, dependency changes, git state, system settings, environment variables, or global tool configuration, helpers may prepare analysis or candidate actions, but Codex keeps approval, execution control, and final acceptance.

## Gates

Delegate only if all pass:

- **Quality**: Codex can verify without trusting blindly.
- **Scope**: files, directories, commands, or output boundaries are explicit.
- **Risk**: mistakes are catchable by diff, tests, static checks, or direct inspection.
- **Decision**: unclear requirements and product, architecture, API, data, security, privacy, persistence, migration, or data-loss decisions have explicit boundaries, expected options, and Codex-owned final approval.
- **Privacy**: secrets, credentials, private data, or sensitive material are excluded, redacted, or explicitly approved with minimum necessary exposure.
- **State**: write scopes do not overlap with active Codex/user edits.
- **Capability**: the helper can likely complete the bounded task, including delegated QA when useful, and return outcome evidence Codex can review cheaply.
- **Savings**: helper prompt + output + Codex review must cost less Codex quota than direct execution. If uncertain, delegate a smaller probe first instead of doing the full task in Codex.

If any gate fails, do it directly.

## Helper Discipline

- Prefer delegation whenever the helper is well-suited and Codex can review the result cheaply; use helpers as workers for concrete deliverables, not as extra reviewers of Codex's own reasoning.
- Avoid delegation when helper output would be harder to verify than direct work.
- Helpers may perform first-pass implementation, implementation self-review, edge-case search, static issue scan, diff summary, test suggestions, verification-command recommendations, and focused remediation.
- Allow edits only for explicit files/symbols and inspectable diffs; allow code-level global symbol changes only when the exact file, symbol, intended behavior, and verification path are specified.
- Helpers may triage unclear requirements, compare architecture/API/data/security/privacy/persistence/migration options, draft dependency/git/system/env/global-config commands, inspect risk, and propose rollback plans.
- Helpers must not directly expose secrets or perform persistent dependency, git, system, environment, or global-tool mutations unless the current task gives exact scope and Codex can route execution through the normal approval and verification path.
- Ask for paths, line numbers, changed-file lists, and short rationales.
- Default output limit: 200 words; use summaries first and inspect details only when needed.
- Use explicit timeouts; abort stuck helpers and continue directly.
- Close, kill, or release helper agents/processes after completion, failure, or timeout.
- Stop delegating if outputs are vague, verbose, harder to review than direct work, or the same helper/subtask type fails twice.

## Helper Lifecycle

Start helpers only after Codex has a task list, explicit scope, forbidden actions, output cap, timeout, and acceptance evidence. Start multiple helpers early when their tasks are independent. Wake or wait for helpers only when Codex needs their result for the next decision, or when collecting parallel results after useful local work is done.

Kill, close, or release helpers when they finish, fail, exceed timeout, drift scope, produce unusable output, become blocked, or lose relevance because Codex changed the plan. For parallel helpers, stop remaining helpers once enough accepted evidence exists, or when their results would no longer change the decision.

## Parallel

Use multiple helpers only for independent subtasks with non-overlapping write scopes. For Claude Code, this can mean multiple `claude -p` calls.

Do not parallelize dependent work, shared write scopes, the same sensitive-data surface, the same dependency/git/system state, or work that would force Codex to resolve conflicting product/architecture decisions. Give each helper its own timeout; use partial good results and drop slow/conflicting ones. Codex merges, rejects conflicts, and verifies final integrated work.

## Workflow

1. Create a short task list and mark each item Codex-owned or helper-owned; minimize Codex-owned execution.
2. Apply gates and helper discipline; assign capable helpers first, including parallel helpers for independent work.
3. Send narrow prompts with exact task, scope, forbidden actions, output limit, expected format, known checks, and allowed edit/symbol scope.
4. Before helper edits, note current diff/status. Use outcome-first review: completion evidence, changed-file list, scope, checks, and user-visible behavior before detailed diffs.
5. Accept, discard, or redo helper work. Retry once with a narrower prompt if useful; Codex executes only after helper delivery fails, the task is non-delegable, or direct work is clearly cheaper.

When helpers are used, tell the user briefly: `Delegated: <task>; accepted/rejected: <result>.` Do not hide delegation or narrate routine helper mechanics.

For Claude Code invocation issues in Codex desktop, read this skill folder's `references/claude-code.md` before installing or debugging npm.

## Prompt Template

```text
You are a helper agent running under Codex supervision.
Goal: <one bounded subtask>
Scope: allowed <paths/commands/state changes>; do not act outside scope. For commits, pushes, dependency installs, destructive commands, system/env/global config changes, sensitive data, or architecture/product/security/data-loss decisions, stop unless the prompt explicitly grants that exact action, approval basis, rollback plan, and acceptance evidence.
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
