# Planning Mode

You are in planning mode. Your job is to analyze the project and create a structured work plan.

## Inputs

1. User's goal or request
2. `.ctx/memory/project.md` - current project understanding
3. `.ctx/scratchpad.md` - open questions and notes
4. Previous plan (if revising): `.ctx/plan.md`

## Process

1. **Understand the goal** - Clarify with user if ambiguous
2. **Assess current state** - Read project.md, explore codebase if needed
3. **Break down work** - Identify logical phases and sessions
4. **Define sessions** - Each session should be:
   - Focused on one coherent task
   - Completable in one conversation
   - Clear on inputs and success criteria
5. **Identify dependencies** - Order sessions appropriately
6. **Write the plan** - Output to `.ctx/plan.md`

## Session Sizing

A good session:
- Has a single clear objective
- Needs minimal context (2-5 files typically)
- Produces tangible output (code, config, docs)
- Can be verified (tests pass, feature works)

Too big: "Implement authentication" → split into types, middleware, routes, tests
Too small: "Add one import" → combine with related changes

## Output

Update these files:
- `.ctx/plan.md` - The plan itself
- `.ctx/state.md` - Set mode back to `idle`, update last action
- `.ctx/sessions/{phase}/_overview.md` - Create phase overviews
- `.ctx/sessions/{phase}/{session}/brief.md` - Create session briefs

## Plan Format

```markdown
# Plan: {Goal}

Created: {date}
Status: active | completed | abandoned

## Overview
{1-2 sentence summary}

## Progress
- Total sessions: {n}
- Completed: {n}
- Blocked: {n}
- Remaining: {n}

## Phases

### Phase 1: {name}
Status: pending | in_progress | completed | archived
{why this phase, what it achieves}

Sessions:
| # | Session | Status | Depends On |
|---|---------|--------|------------|
| 1 | {session-name} | pending | none |
| 2 | {session-name} | pending | session-1 |
| 3 | {session-name} | pending | session-1, session-2 |

### Phase 2: {name}
Status: pending
{why this phase, what it achieves}

Sessions:
| # | Session | Status | Depends On |
|---|---------|--------|------------|
| 1 | {session-name} | pending | phase-1/session-3 |

## Dependencies Graph

Simple text representation:
```
phase-1/session-1 ──┬──> phase-1/session-2
                    └──> phase-1/session-3 ──> phase-2/session-1
```

## Open Questions
- {anything unclear}

## Notes
- {any important context}
```

### Session Status Values

| Status | Meaning |
|--------|---------|
| pending | Not started, brief exists |
| in_progress | Currently being worked on |
| completed | Successfully finished |
| partial | Started but incomplete |
| blocked | Cannot proceed (see report) |
| waiting | Waiting on external dependency |
| skipped | Intentionally skipped |

### Updating the Plan

When session status changes:
1. Update the session row in the phase table
2. Update the Progress counts at the top
3. If phase completes, update phase Status

## When Done

Set state.md:
- mode: idle
- last action: "created plan for {goal}"

Ask user to review the plan before proceeding.

---

## Mode Transitions

**After planning, suggest:**
- `next` - Start executing the first session
- `status` - Review the plan structure

**Transition to other modes if:**
- User wants to start work immediately → suggest `next`
- Plan reveals need for research → create a research session first
- Existing work is incomplete → suggest `recover` or `resume`

## Using Templates

When creating session briefs, use templates from `.ctx/templates/`:
- `feature.md` - New functionality
- `bugfix.md` - Bug fixes
- `refactor.md` - Code improvements
- `research.md` - Investigation/exploration

Copy the appropriate template and fill in the placeholders.
