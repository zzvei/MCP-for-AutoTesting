# OpenClaw Execution Guards

If the execution agent is **openclaw**, read this folder first before running the test plan.

## Mandatory Read Order
1. `playwright-usage-limit.md`
2. `playwright-token-guard.md`
3. `no-progress-stop.md`
4. `self-iteration-limit.md`
5. `task-budget-limit.md`
6. `diff-budget-enforcer.md`

## Purpose
- Prevent infinite loops during autonomous runs
- Enforce call frequency and token budget
- Stop no-progress iterations
- Control patch size and task execution budget

## Rule
When conflict happens between convenience and guard rules, **follow guard rules first**.
