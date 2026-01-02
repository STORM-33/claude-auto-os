# Recovery Mode

You are in recovery mode. Your job is to diagnose and resolve blockers, then return to normal workflow.

## When to Enter Recovery

- Session marked as `blocked` in state.md
- Execution failed with errors
- Context is corrupted or inconsistent
- User explicitly requests `recover`

## Inputs

1. `.ctx/state.md` - Current state and blocker description
2. `.ctx/sessions/{phase}/{session}/report.md` - Last report (if exists)
3. `.ctx/scratchpad.md` - Any notes about the issue
4. Error logs or messages from the failed operation

## Process

1. **Diagnose the blocker**
   - What was being attempted?
   - What specific error occurred?
   - Is this a code issue, context issue, or requirement issue?

2. **Categorize the problem**
   - `code_error` - Tests fail, syntax error, runtime error
   - `missing_context` - Need files/info not in brief
   - `unclear_requirements` - Brief is ambiguous
   - `external_dependency` - Waiting on user/external input
   - `scope_creep` - Task grew beyond session bounds
   - `state_corruption` - State files are inconsistent

3. **Determine resolution strategy**

   | Problem Type | Resolution |
   |--------------|------------|
   | `code_error` | Fix the error, retry session |
   | `missing_context` | Update brief, add required files, retry |
   | `unclear_requirements` | Ask user, update brief, retry |
   | `external_dependency` | Document wait, set mode to `waiting`, suggest next steps |
   | `scope_creep` | Split session, update plan, mark original complete |
   | `state_corruption` | Reset state, rebuild from last known good |

4. **Execute resolution**
   - Apply the fix
   - Update relevant files
   - Clear blocker from state

5. **Validate recovery**
   - Can the session now proceed?
   - Is state.md consistent?
   - Are all referenced files present?

## Recovery Actions

### Reset to Last Good State
```
1. Read most recent completed session report
2. Set state.md to idle, no active session
3. Clear blockers
4. User can then run `next` to continue
```

### Split Oversized Session
```
1. Create new session briefs for the split work
2. Update plan.md with new sessions
3. Mark original session as `completed (partial)`
4. Continue with first split session
```

### Rebuild Context
```
1. Read project.md and all module summaries
2. Scan codebase for current state
3. Update memory files if stale
4. Clear corrupted session data
```

## Output

Update these files:
- `.ctx/state.md` - Clear blocker, set appropriate mode
- `.ctx/sessions/{phase}/{session}/report.md` - Document recovery action
- `.ctx/scratchpad.md` - Note what caused the issue (for learning)
- `.ctx/plan.md` - If sessions were split or reordered

## Recovery Report Format

Add to the session report:

```markdown
## Recovery

**Blocker**: {what was blocked}
**Diagnosis**: {problem type and root cause}
**Resolution**: {what was done to fix it}
**Prevention**: {how to avoid this in future}
```

## When Done

Set state.md:
- mode: `idle` (or `executing` if resuming immediately)
- blockers: none
- last action: "recovered from {problem type}"

Summarize the recovery for the user and suggest next action.

## Escalation

If recovery is not possible:
1. Document the issue thoroughly in scratchpad
2. Set state.md blocker to describe the situation
3. Set mode to `waiting`
4. Ask user for guidance

Never leave the system in an undefined state. Always ensure state.md accurately reflects reality.
