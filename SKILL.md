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

Use these default thresholds:

- Direct: likely <=2 tool calls, <=500 words of raw output, or <=10 lines changed.
- Probe: likely >5 tool calls, >2,000 words of raw output, >3 files, or unclear search space.
- Batch: 3+ same-type microtasks with the same read/write permission and risk profile.

## High-Savings Mode

Target about 70% Codex quota savings only when the task is mostly bounded reading, search, drafting, implementation, or QA. Use this mode by default for medium/large tasks:

- Keep Codex out of raw context first. Have helpers read files/logs/docs and return compact findings before Codex opens details.
- Use mapper helpers for independent areas, then a reducer helper to merge findings into one short decision packet.
- Ask helpers for exact paths, line numbers, commands run, pass/fail status, changed files, and confidence; avoid prose that Codex must re-interpret.
- Codex samples raw evidence only where risk is high, evidence is weak, checks fail, or acceptance depends on subtle behavior.
- Prefer helper-written first drafts and patches over Codex writing from scratch; Codex edits only the rejected or risky parts.
- Cap normal helper outputs at 100 words; allow 200 words only for complex diffs, architecture options, or failure analysis.
- Create one shared task brief for multi-helper work: goal, scope, forbidden actions, current status, expected evidence, and output format in <=200 words. Reuse it instead of repeating context.

## Gates

Delegate only if all pass:

- **Quality**: Codex can verify without trusting blindly.
- **Scope**: files, directories, commands, or output boundaries are explicit.
- **Risk**: mistakes are catchable by diff, tests, static checks, or direct inspection.
- **Decision**: unclear requirements and product, architecture, API, data, security, privacy, persistence, migration, or data-loss decisions have explicit boundaries, expected options, and Codex-owned final approval.
- **Privacy**: secrets, credentials, private data, or sensitive material are excluded, redacted, or explicitly approved with minimum necessary exposure.
- **State**: write scopes do not overlap with active Codex/user edits.
- **Capability**: the helper can likely complete the bounded task, including delegated QA when useful, and return outcome evidence Codex can review cheaply.
- **Savings**: helper prompt + output + Codex review must cost less Codex quota than direct execution. For medium/large tasks, aim for helpers to do at least 70% of reading, drafting, implementation, and QA labor. If uncertain, delegate a smaller probe first instead of doing the full task in Codex.

If any gate fails, do it directly.

## Helper Discipline

- Use helper tiers:
  - Tier 1: search, read, log summary, path/line extraction, simple comparison.
  - Tier 2: single-area implementation, tests, lint/type triage, focused remediation.
  - Tier 3: multi-file debug, cross-cutting edits, architecture/API/security option analysis.
- Prefer delegation whenever the helper is well-suited and Codex can review the result cheaply; use helpers as workers for concrete deliverables, not as extra reviewers of Codex's own reasoning.
- Avoid delegation when helper output would be harder to verify than direct work.
- Helpers may perform first-pass implementation, implementation self-review, edge-case search, static issue scan, diff summary, test suggestions, verification-command recommendations, and focused remediation.
- Allow edits only for explicit files/symbols and inspectable diffs; allow code-level global symbol changes only when the exact file, symbol, intended behavior, and verification path are specified.
- Helpers may triage unclear requirements, compare architecture/API/data/security/privacy/persistence/migration options, draft dependency/git/system/env/global-config commands, inspect risk, and propose rollback plans.
- Helpers must not directly expose secrets or perform persistent dependency, git, system, environment, or global-tool mutations unless the current task gives exact scope and Codex can route execution through the normal approval and verification path.
- Ask for paths, line numbers, changed-file lists, commands run, pass/fail status, and short rationales.
- Default output limit: 100 words; use summaries first and inspect details only when needed.
- Use explicit timeouts; abort stuck helpers and continue directly.
- Close, kill, or release helper agents/processes after completion, failure, or timeout.
- Stop delegating if outputs are vague, verbose, harder to review than direct work, or the same helper/subtask type fails twice.

## Helper Lifecycle

Start helpers only after Codex has a task list, explicit scope, forbidden actions, output cap, timeout, and acceptance evidence. Start multiple helpers early when their tasks are independent. Wake or wait for helpers only when Codex needs their result for the next decision, or when collecting parallel results after useful local work is done.

Kill, close, or release helpers when they finish, fail, exceed timeout, drift scope, produce unusable output, become blocked, or lose relevance because Codex changed the plan. For parallel helpers, stop remaining helpers once enough accepted evidence exists, or when their results would no longer change the decision.

## Parallel

Use multiple helpers only for independent subtasks with non-overlapping write scopes. For Claude Code, this can mean multiple `claude -p` calls. For large tasks, use mapper helpers first and, if their outputs are numerous, a reducer helper to compress them before Codex reviews.

Do not parallelize dependent work, shared write scopes, the same sensitive-data surface, the same dependency/git/system state, or work that would force Codex to resolve conflicting product/architecture decisions. Give each helper its own timeout; use partial good results and drop slow/conflicting ones. Codex merges, rejects conflicts, and verifies final integrated work.

## Batching

Batch same-type microtasks when they share scope, permissions, and risk. Prefer one helper with a numbered task list over many helpers for small reads, searches, summaries, or mechanical edits. Keep each batch reviewable: <=10 files or <=20 findings unless a reducer helper will compress it. Do not batch mixed-risk tasks, overlapping writes, secrets, or decisions that need separate acceptance.

## Workflow

1. Create a short task list and mark each item Codex-owned or helper-owned; minimize Codex-owned execution and raw-file reading.
2. Apply gates and helper discipline; assign capable helpers first, including parallel mapper helpers and a reducer helper when useful.
3. For multi-helper work, write one <=200-word shared task brief, then send narrow prompts with only task-specific scope, output limit, known checks, and allowed edit/symbol scope.
4. Before helper edits, note current diff/status. Use outcome-first review: completion evidence, changed-file list, scope, checks, and user-visible behavior before detailed diffs.
5. Accept, discard, or redo helper work. Retry once with a narrower prompt if useful; Codex executes only after helper delivery fails, the task is non-delegable, or direct work is clearly cheaper.

When helpers are used, tell the user briefly: `Delegated: <task>; accepted/rejected: <result>.` Do not hide delegation or narrate routine helper mechanics.

For Claude Code invocation issues in Codex desktop, read this skill folder's `references/claude-code.md` before installing or debugging npm.

## Prompt Template

```text
You are a helper agent running under Codex supervision.
Goal: <one bounded subtask>
Scope: allowed <paths/commands/state changes>; do not act outside scope. For commits, pushes, dependency installs, destructive commands, system/env/global config changes, sensitive data, or architecture/product/security/data-loss decisions, stop unless the prompt explicitly grants that exact action, approval basis, rollback plan, and acceptance evidence.
Output: <=100 words unless allowed; prefer paths, line numbers, changed-file list, commands run, pass/fail status, confidence, and short rationale; do not paste large files.
If unsure, stop and state the blocker.
```

## Acceptance

Before using helper work, verify:

- scope, task match, changed-file list, and outcome evidence are coherent
- helper QA evidence is plausible; inspect details only if evidence is weak, checks fail, scope drifts, or risk is high
- sample raw evidence first when helper confidence is high and tests/checks pass: inspect 1-2 highest-risk files/findings, not every detail
- use full `git diff -- <files>`, tests, lint/type checks, or direct inspection when confidence is low, checks fail, risk is high, or sampled evidence disagrees
- behavior changes prefer automated tests/lint/type checks; otherwise use focused direct behavior inspection
- failed helper edits are discarded or only helper-owned changes are reverted, preserving unrelated user/Codex work
- no hidden uncertainty or unexplained broadening
