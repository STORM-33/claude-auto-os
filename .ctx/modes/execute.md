# Execution Mode

You are in execution mode. Your job is to complete the active session.

## Inputs

1. `.ctx/state.md` - Which session is active
2. `.ctx/sessions/{phase}/{session}/brief.md` - What to do
3. `.ctx/memory/project.md` - Project context
4. `.ctx/memory/modules/{x}.md` - Relevant module context (as specified in brief)

## Process

1. **Load context** - Read only what's listed in brief.md
2. **Understand the task** - Re-read brief.md objectives
3. **Do the work** - Write code, make changes, run tests
4. **Verify** - Check against success criteria in brief
5. **Document** - Write the session report

## During Execution

- Stay focused on the session objective
- If you discover something outside scope, note in scratchpad
- If blocked, document in report and ask user
- If scope needs to change, pause and discuss with user

## Output

Create/update these files:
- `.ctx/sessions/{phase}/{session}/report.md` - What was done
- `.ctx/state.md` - Update status
- `.ctx/scratchpad.md` - Any cross-session notes
- `.ctx/memory/modules/{x}.md` - If significant new understanding

## Report Format

```markdown
# Session Report: {session-name}

Completed: {date}
Status: {completed | partial | blocked}

## Objective
{from brief}

## What Was Done
- {concrete changes made}
- {files modified}

## Decisions Made
- {choice}: {reasoning}

## Discovered
- {anything learned that wasn't expected}

## Blockers
{if any, what's preventing completion}

## Next Steps
{if partial, what remains}
```

## When Done

Set state.md:
- mode: idle
- active session: none
- last action: "completed {session-name}" or "blocked on {session-name}"

Update progress tracking:
- Increment `Sessions Completed` (if completed)
- Increment `Sessions Blocked` (if blocked)
- Increment `Current Streak` (if completed) or reset to 0 (if blocked)

---

## Mode Transitions

**After successful completion, suggest based on state:**

| Condition | Suggestion |
|-----------|------------|
| More sessions in phase | `next` - Continue to next session |
| Phase complete, more phases exist | `reflect` then `next` |
| All phases complete | `reflect` to consolidate learning |
| 3+ sessions since last reflect | `reflect` before continuing |

**If blocked:**
- Minor issue you can fix → fix it, complete session
- Need user input → set mode to `waiting`, ask user
- Significant blocker → `recover` mode

**If scope expands:**
- Note new work in scratchpad
- Do NOT expand current session
- Suggest `plan` to revise after completing current work

## Progress Awareness

Check state.md progress tracking:
- If `Sessions Completed` >= 3 since last reflect → suggest reflect
- If `Current Streak` >= 5 → acknowledge momentum
- If `Sessions Blocked` > 0 recently → be extra careful
