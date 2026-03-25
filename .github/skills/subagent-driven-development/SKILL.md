---
name: subagent-driven-development
description: Use when executing implementation plans with independent tasks in the current session
---

# Subagent-Driven Development

Execute plan by dispatching fresh subagent per task, with two-stage review after each: spec compliance review first, then code quality review.

**Why subagents:** You delegate tasks to specialized agents with isolated context. By precisely crafting their instructions and context, you ensure they stay focused and succeed at their task. They should never inherit your session's context or history — you construct exactly what they need.

**Core principle:** Fresh subagent per task + two-stage review (spec then quality) = high quality, fast iteration

## When to Use

- Have an implementation plan with mostly independent tasks
- Want to stay in the current session
- Tasks can be executed sequentially without tight coupling

**vs. Executing Plans:** Same session, fresh context per task, two-stage review, faster iteration.

## The Process

For each task in the plan:

### 1. Dispatch Implementer Subagent
- Provide full task text and necessary context
- Include relevant file paths, code snippets, and constraints
- Never make subagent read the plan file — provide full text instead

### 2. Handle Implementer Status
- **DONE:** Proceed to spec compliance review
- **DONE_WITH_CONCERNS:** Read concerns, address if about correctness/scope, then proceed to review
- **NEEDS_CONTEXT:** Provide missing context and re-dispatch
- **BLOCKED:** Assess blocker — provide more context, use more capable model, break task smaller, or escalate to human

### 3. Dispatch Spec Reviewer Subagent
- Provide: what was implemented, what the spec requires, git SHAs
- Reviewer confirms code matches spec — nothing missing, nothing extra
- If issues found: implementer fixes, re-review until approved

### 4. Dispatch Code Quality Reviewer Subagent
- **Only after spec compliance is approved**
- Provide: git SHAs, description of changes
- Reviewer checks code quality, patterns, test coverage
- If issues found: implementer fixes, re-review until approved

### 5. Mark Task Complete

Repeat for all tasks. After all tasks complete:
- Dispatch final code reviewer for entire implementation
- Use the finishing-a-development-branch skill

## Model Selection

Use the least powerful model that can handle each role to conserve cost and increase speed.

- **Mechanical tasks** (isolated functions, clear specs, 1-2 files): fast, cheap model
- **Integration tasks** (multi-file coordination, debugging): standard model
- **Architecture, design, review**: most capable available model

## Red Flags

**Never:**
- Start implementation on main/master branch without explicit user consent
- Skip reviews (spec compliance OR code quality)
- Proceed with unfixed issues
- Dispatch multiple implementation subagents in parallel (conflicts)
- Make subagent read plan file (provide full text instead)
- Skip scene-setting context
- Accept "close enough" on spec compliance
- **Start code quality review before spec compliance is approved** (wrong order)
- Move to next task while either review has open issues

**If subagent asks questions:**
- Answer clearly and completely
- Provide additional context if needed
- Don't rush them into implementation

**If reviewer finds issues:**
- Implementer fixes them
- Reviewer reviews again
- Repeat until approved

## Related Skills

- **using-git-worktrees** - Set up isolated workspace before starting
- **writing-plans** - Creates the plan this skill executes
- **requesting-code-review** - Code review template for reviewer subagents
- **finishing-a-development-branch** - Complete development after all tasks
- **test-driven-development** - Subagents follow TDD for each task
