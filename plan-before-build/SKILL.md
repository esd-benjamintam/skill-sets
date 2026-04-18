---
name: plan-before-build
description: |
  When a user needs to turn an idea or draft plan into a concrete implementation plan, TODO list, and evaluation cases, trigger this skill. Use it before implementation starts to generate step-by-step tasks, acceptance criteria, and evals/evals.json templates.
compatibility:
  - required-tools: ["python", "git"]
  - optional: ["docker", "bash"]
---

## Overview

plan-before-build converts vague or half-baked requirements into:
- an executable step-by-step plan (each step includes goal, inputs, outputs, acceptance criteria, priority)
- a concise todo list that can be pasted into a task board
- a basic evals/evals.json skeleton for automated or manual evaluation

It is intended for product/PMs preparing an implementation plan, engineers drafting an implementation, or anyone who needs to structure brainstorming into actionable work.

## Triggering heuristics

- Trigger when the user asks to "break down", "plan", "make a todo", "acceptance criteria", or similar phrases about implementing a feature.
- Trigger when the user provides a high-level goal without concrete implementation details (e.g., "I want a CSV export feature, how to break it down?").

If either condition holds, the skill should be invoked to produce a structured plan.

## How the skill works

Workflow the model should follow:

1. Capture intent
   - Confirm the Goal: one-sentence summary of the final outcome
   - Note Constraints: platform, language, compatibility, timelines, dependencies
   - Identify input/output examples if provided

2. Decompose into milestones
   - Break the goal into 2-8 milestones; each milestone into concrete tasks

3. Task schema (each task should include):
   - id: unique identifier
   - title: short task title
   - description: implementation notes or references
   - inputs: required inputs (files, APIs, libraries)
   - outputs: deliverables
   - acceptance_criteria: verifiable checks
   - estimate: rough effort (hours/days)
   - priority: high/medium/low
   - owner: suggested role (optional)

4. Produce a todo list (short bullet list, ready to paste to task board)

5. Generate evals/evals.json skeleton
   - Include 2-4 realistic scenario prompts, expected outputs, and input file lists

6. Return deliverables
   - Markdown plan, todo list, evals/evals.json draft, and short risks & dependencies section

## Template (example output format)

The model should follow this structure to ensure downstream automation:

### Plan (Markdown)

# <Project short title>

## Goal
> A one-sentence description of the final outcome

## Constraints
- constraint A
- constraint B

## Milestones

1. Milestone 1 — description
   - Task 1.1: ...
     - inputs: ...
     - outputs: ...
     - acceptance_criteria:
       - ...
     - estimate: 1d
     - priority: high

### Todo list (short)

- [ ] Task 1.1 — owner — estimate
- [ ] Task 1.2 — owner — estimate

### evals/evals.json (skeleton)

{
  "skill_name": "plan-before-build",
  "evals": [
    {
      "id": 0,
      "prompt": "A realistic user scenario prompt",
      "expected_output": "Description of expected result",
      "files": []
    }
  ]
}

## Output requirements (MUST)

- Prefer concise, unambiguous English responses (the skill should be English-only).
- Keep milestones between 2 and 8; no more than 10 tasks per milestone.
- Every acceptance_criteria must be verifiable (automated script or manual check).

## Do NOT

- List fuzzy, untestable acceptance criteria (e.g., "user satisfied" as the sole criterion).
- Produce implementation code — this skill outputs plans and evaluation cases only.

## Example prompts (suggested)

1. "Add a CSV export endpoint to an existing Express + TypeScript service. Backend: PostgreSQL, Frontend: React. Provide an implementation plan and evaluation cases."
2. "Create a small tool to convert internal XLSX reports to standardized CSV. Column names vary. Provide a detailed plan and 3 test cases."
3. "Implement image upload with thumbnail generation. Provide milestones, task list, acceptance criteria, and evaluation cases."

## Risks & dependencies (examples)

- When relying on third-party APIs, note rate limits and fallback options.
- For data migrations, include backup and rollback steps.

## Next steps (when plan generated)

1. Confirm priorities and owners with stakeholders
2. Import the todo list into a task manager (Jira/Trello/Asana)
3. Run a small-scale evaluation (manual or automated) and collect feedback for iteration

---

<!-- End of SKILL.md -->
