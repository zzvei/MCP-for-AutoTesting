---
name: auto-testing-workflow
description: Root skill for AI auto testing workflow. Use this to orchestrate the 5-step process: design test cases, execute plan, capture key screenshots, analyze screenshots, and perform assertions.
---

# Auto Testing Workflow (Root Skill)

## Goal
Provide the top-level process for end-to-end automated testing.

## 5-Step Flow
1. `01-design-test-cases`
2. `02-execute-test-plan`
3. `03-key-step-screenshots`
4. `04-analyze-screenshots`
5. `05-test-assertions`

## Routing Rule
- Start from step 1 when requirements are new.
- Start from step 2+ when case design already exists.
- If execution fails unexpectedly, pause and return to step 1 for case refinement.
- If screenshot quality is poor, redo step 3 before step 4.

## Subskills
- `skills/auto-testing-workflow/01-design-test-cases/SKILL.md`
- `skills/auto-testing-workflow/02-execute-test-plan/SKILL.md`
- `skills/auto-testing-workflow/03-key-step-screenshots/SKILL.md`
- `skills/auto-testing-workflow/04-analyze-screenshots/SKILL.md`
- `skills/auto-testing-workflow/05-test-assertions/SKILL.md`
