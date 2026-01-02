# Waiting Mode

You are in waiting mode. The system is paused waiting for an external dependency or user input.

## When to Enter Waiting Mode

- Waiting for user decision on a design question
- Waiting for external API key, credentials, or access
- Waiting for external service to be available
- Waiting for code review or approval
- Waiting for data or assets from external source
- Waiting for another team member's work

## State When Waiting

state.md should show:
```
## Mode
waiting

## Active Session
{session that triggered wait}

## Waiting For
{description of what we're waiting for}

## Waiting Since
{date}

## Blockers
external: {description}
```

## Inputs

1. `.ctx/state.md` - Current wait status
2. `.ctx/sessions/{phase}/{session}/report.md` - Partial report with wait reason
3. User input indicating the wait is resolved

## On Conversation Start

When entering a conversation in waiting mode:

```
1. Read state.md to understand what we're waiting for
2. Display wait status to user:

   "System is waiting for: {waiting_for}
    Since: {waiting_since}
    Session: {active_session}

    Options:
    - If resolved, say 'resolved' to continue
    - If still waiting, say 'still waiting' for suggestions
    - If need to work around it, say 'workaround'
    - If need to abandon, say 'abandon'"
```

## Resolution Paths

### Path 1: Resolved
User confirms the dependency is now available.

```
1. Update state.md:
   - mode: executing
   - blockers: none
   - last action: "resumed after wait"

2. Continue with the session from where it paused
3. Complete execution as normal
```

### Path 2: Still Waiting
Dependency is not yet available.

```
1. Check if other sessions can proceed:
   - Are there sessions without this dependency?
   - Can work be reordered?

2. If yes:
   - Suggest which sessions can proceed
   - Offer to update plan to work around

3. If no:
   - Document wait status
   - Suggest checking back later
   - Offer to set a reminder note in scratchpad
```

### Path 3: Workaround
User wants to proceed without the dependency.

```
1. Discuss workaround options:
   - Mock the dependency temporarily?
   - Change approach to avoid dependency?
   - Split session to do what's possible now?

2. Update plan and session briefs accordingly

3. Set mode back to idle or executing

4. Note in scratchpad: "Worked around {dependency} by {approach}"
```

### Path 4: Abandon
User decides to abandon this path entirely.

```
1. Mark session as blocked (permanent) in report
2. Update plan.md:
   - Remove or skip dependent sessions
   - Note the abandonment reason

3. Set state.md:
   - mode: idle
   - active session: none
   - last action: "abandoned {session} due to {reason}"

4. Suggest `next` for remaining work or `plan` to revise
```

## Wait Tracking

For long waits, track in scratchpad:

```markdown
## Waiting On
- [ ] {dependency} for {session} (since {date})
  - Impact: blocks {n} sessions
  - Last checked: {date}
  - Notes: {any updates}
```

## Timeout Guidance

Suggest action based on wait duration:

| Duration | Suggestion |
|----------|------------|
| < 1 day | Normal, check again soon |
| 1-3 days | Consider workaround or parallel work |
| 3-7 days | Escalate or revise plan |
| > 7 days | Likely should abandon or major replanning |

## Output Format

When checking in on a wait:

```
## Waiting Status

Waiting for: {description}
Since: {date} ({n} days)
Session: {name}
Impact: Blocks {n} dependent sessions

Options:
1. resolved   - Dependency is now available
2. waiting    - Still waiting, show alternatives
3. workaround - Proceed without it
4. abandon    - Give up on this path
```

## Exiting Waiting Mode

Always exit to a defined mode:
- `executing` - If resuming the paused session
- `idle` - If session was completed, abandoned, or skipped
- `planning` - If major replanning is needed

Never leave the system in an undefined state.
