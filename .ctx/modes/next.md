# Next Command

You are running the `next` command. Your job is to identify and start the next pending session.

## Inputs

1. `.ctx/state.md` - Current state
2. `.ctx/plan.md` - Active work plan with phases and sessions
3. `.ctx/sessions/` - Session directories with briefs and reports

## Process

### 1. Check Prerequisites

```
IF mode != idle THEN
  Error: "Cannot start next session while in {mode} mode"
  Suggest: Complete current mode first
  EXIT

IF no plan.md exists OR plan.md says "No active plan" THEN
  Error: "No active plan"
  Suggest: Run `plan` first
  EXIT
```

### 2. Build Session Status Map

Scan all sessions and determine status:

```
FOR each phase in plan.md (in order):
  FOR each session in phase:
    path = .ctx/sessions/{phase}/{session}/

    IF report.md exists AND contains "Status: completed" THEN
      status = completed
    ELSE IF report.md exists AND contains "Status: blocked" THEN
      status = blocked
    ELSE IF report.md exists AND contains "Status: partial" THEN
      status = partial
    ELSE IF brief.md exists THEN
      status = pending
    ELSE
      status = not_created
```

### 3. Check Dependencies

For each pending/partial session, verify dependencies are met:

```
FOR each session with status pending OR partial:
  Read brief.md
  Find "Requires:" line

  IF requires != "none" THEN
    FOR each required_session in requires:
      IF required_session.status != completed THEN
        session.blocked_by = required_session
        CONTINUE to next session
```

### 4. Select Next Session

Priority order:
1. **Partial sessions** - Resume incomplete work first
2. **Pending sessions with met dependencies** - In phase order, then session order
3. **First session of next phase** - If current phase complete

```
selected = NULL

# First: check for partial sessions
FOR each session WHERE status = partial:
  IF no unmet dependencies:
    selected = session
    BREAK

# Second: check for pending sessions
IF selected = NULL:
  FOR each phase in order:
    FOR each session in phase in order:
      IF status = pending AND no unmet dependencies:
        selected = session
        BREAK

# Third: check if all done
IF selected = NULL:
  IF all sessions completed:
    Message: "All sessions complete! Run `reflect` to consolidate."
    EXIT
  ELSE:
    Message: "No sessions available. Blocked sessions exist."
    List blocked sessions and what they're waiting for
    EXIT
```

### 5. Start Selected Session

```
1. Read .ctx/sessions/{phase}/{session}/brief.md
2. Update state.md:
   - mode: executing
   - active session: {session}
   - active phase: {phase}
   - context loaded: (list from brief.md)
   - last action: "started {session}"

3. Load mode instructions from .ctx/modes/execute.md
4. Begin execution
```

## Output Format

When starting a session:
```
Starting: {session-name}
Phase: {phase-name}
Type: {feature|bugfix|refactor|research}

Objective: {from brief}

Context loading:
- {file1}
- {file2}
...

Beginning execution...
```

When blocked:
```
Cannot proceed. Blocked sessions:

- {session1}: waiting on {dependency}
- {session2}: waiting on {dependency}

Suggestions:
- Complete {dependency} first
- Run `recover` if {dependency} is stuck
- Run `plan` to revise dependencies
```

When complete:
```
All sessions complete!

Summary:
- Phases: {n} completed
- Sessions: {n} completed, {n} blocked

Suggested: Run `reflect` to consolidate learning.
```

## Edge Cases

### No brief.md exists
Session was planned but brief not created. Error and suggest `plan` to create briefs.

### Circular dependency
Sessions depend on each other. Error and suggest `plan` to fix.

### Phase has no sessions
Skip empty phases, continue to next.

### Multiple partial sessions
Pick the one from the earliest phase.
