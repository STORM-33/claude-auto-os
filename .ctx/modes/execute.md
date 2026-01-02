# Execution Mode

You are in execution mode. Your job is to complete the active session.

## Inputs

1. `.ctx/state.md` - Which session is active
2. `.ctx/sessions/{phase}/{session}/brief.md` - What to do
3. `.ctx/memory/project.md` - Project context
4. `.ctx/memory/modules/{x}.md` - Relevant module context (as specified in brief)

## Process

1. **Load context** - Read only what's listed in brief.md
2. **Check complexity** - Read `Complexity:` field from brief.md
3. **Understand the task** - Re-read brief.md objectives
4. **Do the work** - Write code, make changes, run tests
5. **Verify** - Check against success criteria in brief
6. **Document** - Auto-generate report, then fill gaps

## Complexity-Based Execution

| Complexity | Documentation | Verification | Reflect Trigger |
|------------|---------------|--------------|-----------------|
| `low` | Fast Track (minimal) | Basic check | Every 5 sessions |
| `medium` | Standard report | Full criteria | Every 3 sessions |
| `high` | Detailed report + decisions | Full + edge cases | After each session |

### Fast Track (Low Complexity)

For `Complexity: low` sessions, use streamlined process:

```
1. Do the work
2. Verify it works
3. Generate minimal report (auto-filled)
4. Skip detailed "Decisions Made" unless noteworthy
5. Move directly to next session
```

Fast Track report format:
```markdown
# Session Report: {session-name}

Completed: {date}
Status: completed
Complexity: low (fast track)

## Summary
{One sentence: what was done}

## Changes
- {file}: {change}

## Verified
- [x] Works as expected
```

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

## Auto-Report Generation

Instead of writing reports from scratch, auto-generate from execution trace:

### Step 1: Collect During Execution

As you work, track:
- Files modified (from tool calls)
- Tests run and results
- Errors encountered and resolved
- Key decisions (when choosing between options)

### Step 2: Generate Skeleton

After completing work, auto-populate:

```markdown
# Session Report: {session-name}

Completed: {date}
Status: {completed|partial|blocked}

## Objective
{Copy from brief.md}

## What Was Done
{Auto-fill from files modified during session}
- Modified `{file1}`: {detected change type}
- Modified `{file2}`: {detected change type}
- Created `{file3}`

## Tests
{Auto-fill from test runs}
- {test command}: {pass/fail}

## Decisions Made
{Only if non-trivial choices were made - leave empty for low complexity}

## Discovered
{Auto-fill from scratchpad additions during session}
```

### Step 3: Human Review

For medium/high complexity:
- Review auto-generated content
- Add reasoning to "Decisions Made"
- Expand "Discovered" with insights
- Add "Blockers" or "Next Steps" if needed

For low complexity:
- Quick scan for accuracy
- Accept or make minimal edits

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
