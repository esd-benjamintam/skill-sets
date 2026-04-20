---
name: plan-before-build
description: Turn a high-level idea into a concise, actionable implementation plan and a short todo list. Use before implementation starts.
---

## Overview

Convert vague requirements into:
- a step-by-step implementation plan
- a short todo list
- key risks and dependencies

This skill outputs plans only. Do not produce implementation code.

Delegate planning work to a sub-agent when possible. The sub-agent should return only the final planning output so the main agent context stays clean and focused.

## When to trigger

Use this skill when:
- the user asks to break down, plan, scope, or create a todo
- the user asks for milestones or acceptance criteria
- the request is high-level and not ready for coding

Do NOT use this skill when:
- the user explicitly asks for implementation or code
- the task is already well-specified and ready to execute

## Workflow

1. Delegate the planning task to a sub-agent when available
2. Capture the goal, constraints, inputs, and outputs
3. Break the work into 2–8 milestones
4. Add concrete tasks under each milestone
5. For each task, include:
   - inputs
   - outputs
   - acceptance_criteria
   - priority
6. Return only the final planning result to the main agent
7. Include a short todo list plus risks/dependencies
8. If key details are missing, state assumptions instead of guessing

## Output format

# <Project short title>

## Goal
> One-sentence final outcome

## Constraints
- ...

## Inputs
- ...

## Outputs
- ...

## Assumptions
- ...

## Milestones

1. Milestone A — short description
   - Task A.1: short description
     - inputs: ...
     - outputs: ...
     - acceptance_criteria:
       - ...
     - priority: P0

2. Milestone B — short description
   - Task B.1: short description
     - inputs: ...
     - outputs: ...
     - acceptance_criteria:
       - ...
     - priority: P1

## Todo
- [ ] Task A.1 — TBD
- [ ] Task B.1 — TBD

## Risks & Dependencies
- ...
- ...

## Requirements (MUST)

- Responses must be in concise, unambiguous English
- Use 2–8 milestones
- Use no more than 10 tasks per milestone
- Acceptance criteria must be specific and verifiable
- Do not invent missing facts; use assumptions if needed
- Delegate planning work to a sub-agent when possible
- Return only the final planning output to avoid polluting the main agent context

## Do NOT

- Do not produce implementation code
- Do not use vague acceptance criteria
- Do not invent owners or technical decisions

## User Query
