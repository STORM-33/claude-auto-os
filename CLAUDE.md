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
| `resume` | Continue active session | execute.md |
| `reflect` | Update memory from recent work | reflect.md |
| `recover` | Diagnose and fix blockers | recover.md |
| `validate` | Check system consistency | validate.md |
| `archive` | Move completed work to history | archive.md |

## File Structure

```
.ctx/
├── state.md              # Current state (read this first)
├── plan.md               # Active work plan
├── scratchpad.md         # Cross-session notes
├── modes/                # Instructions per mode/command
│   ├── plan.md           # Planning mode
│   ├── execute.md        # Execution mode
│   ├── reflect.md        # Reflection mode
│   ├── recover.md        # Error recovery
│   ├── waiting.md        # External dependency wait
│   ├── next.md           # Session selection logic
│   ├── status.md         # Status display format
│   ├── validate.md       # Consistency checks
│   └── archive.md        # History management
├── templates/            # Session brief templates
│   ├── feature.md        # New functionality
│   ├── bugfix.md         # Bug fixes
│   ├── refactor.md       # Code improvements
│   └── research.md       # Investigation
├── sessions/             # Active work (grouped by phase)
│   └── {phase}/
│       ├── _overview.md  # Phase goals
│       └── {session}/
│           ├── brief.md  # Input (what to do)
│           └── report.md # Output (what was done)
├── memory/               # Persistent context
│   ├── project.md        # Layer 0 - always loaded
│   └── modules/          # Layer 1 - load as needed
└── history/              # Archived completed work
    └── {date}_{plan}/
        ├── _final.md     # Plan completion summary
        └── {phase}/
            └── _summary.md
```

## Core Principles

1. **State is truth** - Always read state.md first, always update it after actions
2. **Minimal context** - Load only what's needed for current task
3. **Document decisions** - Session reports capture reasoning, not just outcomes
4. **Update memory** - Reflect mode keeps project.md current
5. **Recover gracefully** - Use recover mode when blocked, never leave undefined state

## Workflow Cycle

```
       ┌─────────┐
       │  plan   │ ← Create work breakdown
       └────┬────┘
            │
            ▼
       ┌─────────┐
  ┌───>│  next   │ ← Select next session
  │    └────┬────┘
  │         │
  │         ▼
  │    ┌─────────┐
  │    │ execute │ ← Do the work
  │    └────┬────┘
  │         │
  │    ┌────┴────┐
  │    │         │
  │    ▼         ▼
  │ success   blocked
  │    │         │
  │    │         ▼
  │    │    ┌─────────┐
  │    │    │ recover │ ← Fix issues
  │    │    └────┬────┘
  │    │         │
  │    ▼         │
  │ ┌─────────┐  │
  │ │ reflect │◄─┘ ← Consolidate learning
  │ └────┬────┘
  │      │
  └──────┘ (more sessions)
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
3. Applies resolution strategy
4. Returns to normal workflow

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
