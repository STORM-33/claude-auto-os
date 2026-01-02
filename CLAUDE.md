# Claude Auto OS

Self-orchestrating AI workflow system built on Claude Code.

## On Conversation Start

1. Read `.ctx/state.md` to understand current state
2. Determine mode: `idle`, `planning`, `executing`, `reflecting`, `recovering`, `waiting`
3. Load appropriate instructions from `.ctx/modes/{mode}.md`
4. Check transition hints in state.md for suggested actions
5. Follow those instructions

## Quick Commands

| Command | Action | Mode File |
|---------|--------|-----------|
| `plan` | Create or revise work plan | plan.md |
| `next` | Start next pending session | next.md |
| `status` | Show current state and progress | status.md |
| `status --compact` | One-line status summary | status.md |
| `status --critical` | Show only critical/high items | status.md |
| `status --detail` | Include per-session breakdown | status.md |
| `resume` | Continue active session | execute.md |
| `reflect` | Update memory from recent work | reflect.md |
| `recover` | Diagnose and fix blockers | recover.md |
| `validate` | Check system consistency | validate.md |
| `archive` | Move completed work to history | archive.md |

## File Structure

```
.ctx/
  state.md          # Current state (read first)
  plan.md           # Active work plan
  scratchpad.md     # Cross-session notes
  modes/            # Mode instructions (plan, execute, reflect, recover, etc.)
  templates/        # Session brief templates (feature, bugfix, refactor, research)
  sessions/{phase}/{session}/  # Active work: brief.md + report.md
  memory/           # project.md + modules/
  history/          # Archived completed work
```

## Core Principles

1. **State is truth** - Always read state.md first, always update it after actions
2. **Minimal context** - Load only what's needed for current task
3. **Document decisions** - Session reports capture reasoning, not just outcomes
4. **Update memory** - Reflect mode keeps project.md current
5. **Recover gracefully** - Use recover mode when blocked, never leave undefined state

## Workflow Cycle

```
plan -> next -> execute -> success -> reflect -> next (loop)
                       \-> blocked -> recover -> reflect
```

## Automatic Transitions

The system tracks progress and suggests mode transitions:

| Condition | Suggestion |
|-----------|------------|
| No active plan | `plan` |
| Plan exists, mode=idle | `next` |
| 3+ sessions since reflect | `reflect` |
| 5+ scratchpad items | `reflect` |
| Session blocked | `recover` |
| Phase complete | `reflect` → `archive` → `next` |
| All complete | `archive` → celebrate |
| External wait | `waiting` mode |

## Session Types

Use templates from `.ctx/templates/` for consistent briefs:

| Type | Use For | Key Sections |
|------|---------|--------------|
| feature | New functionality | Requirements, success criteria |
| bugfix | Bug fixes | Reproduction steps, root cause |
| refactor | Code improvements | Before/after state, rollback |
| research | Investigation | Questions to answer, time box |

## Session Complexity

Each session is assigned a complexity level that affects documentation requirements:

| Complexity | Documentation | Verification | Reflect Trigger |
|------------|---------------|--------------|-----------------|
| `low` | Fast Track (minimal report) | Basic check | Every 5 sessions |
| `medium` | Standard report | Full criteria check | Every 3 sessions |
| `high` | Detailed report + decisions | Full + edge cases | After each session |

### Complexity Guidelines

- **Low**: Single file, obvious change, no decisions (typos, config updates)
- **Medium**: 2-5 files, some decisions, clear path (add endpoint, write tests)
- **High**: Many files, architectural decisions, unknowns (new feature, complex debug)

## Progress Tracking

state.md tracks:
- **Sessions Completed** - Total finished sessions
- **Sessions Blocked** - Sessions that hit issues
- **Current Streak** - Consecutive completions (resets on block)
- **Last Reflect** - When memory was last updated

## Session Status Values

| Status | Meaning |
|--------|---------|
| pending | Not started |
| in_progress | Currently active |
| completed | Successfully finished |
| partial | Started but incomplete |
| blocked | Cannot proceed |
| waiting | External dependency |
| skipped | Intentionally bypassed |

## Recovery

When things go wrong:
1. Run `recover` to enter recovery mode
2. System diagnoses: `code_error`, `missing_context`, `unclear_requirements`, `external_dependency`, `scope_creep`, `state_corruption`
3. Assesses severity level
4. Applies resolution strategy based on severity
5. Returns to normal workflow

### Blocker Severity Levels

| Severity | Response | Example |
|----------|----------|---------|
| `critical` | Stop all work, notify user immediately | State corruption, data loss risk |
| `high` | Attempt one fix, escalate if fails | Blocks phase, no workaround |
| `medium` | Auto-resolve if possible, log | Blocks session, workaround exists |
| `low` | Fix silently, note in report | Minor inconvenience |

## Validation

Run `validate` periodically to check:
- State file integrity
- Active session consistency
- Plan structure validity
- Dependency correctness
- Report completeness
- No orphaned files

## Archiving

After completing phases/plans:
1. Run `archive` (or happens during reflect)
2. Completed work moves to `.ctx/history/`
3. Summaries generated for future reference
4. Active workspace stays clean

## History & Pattern Reuse

The system learns from past work via `.ctx/history/index.md`:

### During Planning
- Check "Patterns & Solutions" for reusable approaches
- Check "Decisions Log" for consistent architectural choices
- Check "Lessons Learned" to avoid past mistakes

### During Archiving
- Decisions, discoveries, and patterns are extracted from reports
- Index is updated automatically for future reference

## Auto-Report Generation

Reports are auto-generated from execution trace to reduce overhead:

1. **During execution**: System tracks files modified, tests run, decisions made
2. **After completion**: Skeleton report is generated automatically
3. **Review step**: Human reviews and adds context (minimal for low complexity)

This reduces "management tax" while maintaining documentation quality.
