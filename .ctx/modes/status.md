# Status Command

You are running the `status` command. Your job is to display the current system state and progress.

## Command Variants

| Command | Output |
|---------|--------|
| `status` | Full status display |
| `status --compact` | One-line summary |
| `status --critical` | Only critical/high severity items |
| `status --detail` | Include per-session breakdown |

## Inputs

1. `.ctx/state.md` - Current state and progress tracking
2. `.ctx/plan.md` - Active work plan
3. `.ctx/sessions/` - Session directories
4. `.ctx/scratchpad.md` - Pending notes count

## Process

### 1. Read Current State

Extract from state.md:
- Current mode
- Active session/phase
- Context loaded
- Last action
- Blockers
- Progress tracking (sessions completed, blocked, streak)
- Last reflect date

### 2. Scan Plan Progress

```
FOR each phase in plan.md:
  phase_stats = {
    total: 0,
    completed: 0,
    partial: 0,
    blocked: 0,
    pending: 0
  }

  FOR each session in phase:
    phase_stats.total++
    Read report.md status (if exists)
    Increment appropriate counter

  Calculate phase_percent = (completed / total) * 100
```

### 3. Count Scratchpad Items

```
Count items in each scratchpad section:
- Open questions
- Discoveries
- Ideas for later
- Notes

total_items = sum of non-empty items
```

### 4. Determine Suggested Action

Based on state, suggest next action:

| Condition | Suggestion |
|-----------|------------|
| mode = executing | `resume` to continue |
| mode = blocked | `recover` to diagnose |
| mode = waiting | Check external dependency |
| No plan exists | `plan` to create |
| Sessions pending | `next` to continue |
| All complete | `reflect` then celebrate |
| 3+ sessions since reflect | `reflect` first |
| 5+ scratchpad items | `reflect` to process |

## Output Format

```
╔══════════════════════════════════════════════════════════════╗
║                      CLAUDE AUTO OS                          ║
╠══════════════════════════════════════════════════════════════╣
║  Mode: {idle|executing|planning|reflecting|recovering|waiting}
║  Active: {session-name} ({phase-name}) | none
║  Last Action: {description}
╠══════════════════════════════════════════════════════════════╣
║  PROGRESS                                                    ║
╠══════════════════════════════════════════════════════════════╣
║  Sessions: {completed}/{total} ({percent}%)
║  Streak: {n} consecutive completions
║  Blocked: {n}
║  Last Reflect: {date|never}
╠══════════════════════════════════════════════════════════════╣
║  PLAN: {plan-name}                                           ║
╠══════════════════════════════════════════════════════════════╣
║  Phase 1: {name}
║    [████████░░] 80% - {completed}/{total} sessions
║
║  Phase 2: {name}
║    [░░░░░░░░░░] 0% - not started
║
╠══════════════════════════════════════════════════════════════╣
║  SCRATCHPAD: {n} items pending                               ║
╠══════════════════════════════════════════════════════════════╣
║  Blockers: {none | list}                                     ║
╠══════════════════════════════════════════════════════════════╣
║  Suggested: {action}                                         ║
╚══════════════════════════════════════════════════════════════╝
```

## Compact Format

For quick checks, use `status --compact`:

```
Status: {mode} | Sessions: {done}/{total} | Streak: {n} | Next: {suggestion}
```

## Critical-Only Format

For `status --critical`, show only items requiring immediate attention:

```
╔══════════════════════════════════════════════════════════════╗
║                    CRITICAL ITEMS                            ║
╠══════════════════════════════════════════════════════════════╣
║  BLOCKERS (critical/high severity only)                      ║
╠══════════════════════════════════════════════════════════════╣
  🔴 CRITICAL: {description}
     Session: {session-name}
     Since: {date}
     Action: {required action}

  🟠 HIGH: {description}
     Session: {session-name}
     Impact: Blocks {n} sessions
     Action: {required action}

╠══════════════════════════════════════════════════════════════╣
║  STALE WARNINGS                                              ║
╠══════════════════════════════════════════════════════════════╣
  ⚠ Memory stale: Last reflect {n} sessions ago
  ⚠ Long wait: {session} waiting for {n} days

╠══════════════════════════════════════════════════════════════╣
║  No critical items: ✓ System healthy                         ║
╚══════════════════════════════════════════════════════════════╝
```

### Critical Item Detection

Flag as critical/high if:
- Blocker severity is `critical` or `high`
- Session blocked for > 3 days
- Same session blocked 2+ times
- State corruption detected
- Progress tracking mismatch

Skip in critical view:
- Low/medium severity blockers
- Normal pending sessions
- Informational warnings

## Detailed Session List

If user asks for details, show per-session status:

```
Phase 1: {name}
  ✓ session-1         completed    2024-01-15
  ✓ session-2         completed    2024-01-15
  ◐ session-3         partial      (in progress)
  ○ session-4         pending      blocked by session-3
  ✗ session-5         blocked      missing API key

Phase 2: {name}
  ○ session-6         pending
  ○ session-7         pending
```

Legend:
- ✓ completed
- ◐ partial
- ○ pending
- ✗ blocked
- ⏳ waiting (external)

## Health Indicators

Add warnings when:
- Streak = 0 and blocked > 0: "⚠ Recent blockers - consider `recover`"
- Last reflect > 5 sessions ago: "⚠ Memory may be stale - consider `reflect`"
- Scratchpad > 10 items: "⚠ Scratchpad overflow - run `reflect`"
- Same session blocked 2+ times: "⚠ Recurring blocker on {session}"

## No State Changes

The `status` command is read-only. It does not modify any files.
