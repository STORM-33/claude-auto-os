# Archive Command

You are running the `archive` command. Your job is to move completed work to history and clean up.

## Purpose

- Archive completed phases and plans
- Preserve work history for reference
- Keep active workspace clean
- Maintain long-term project memory

## When to Archive

- After completing all sessions in a phase
- After completing an entire plan
- When starting a major new direction
- During reflect mode (as part of cleanup)

## Inputs

1. `.ctx/state.md` - Current state
2. `.ctx/plan.md` - Active plan
3. `.ctx/sessions/` - Session directories
4. `.ctx/history/` - Archive destination

## Process

### 1. Identify Completed Work

```
FOR each phase in .ctx/sessions/:
  phase_complete = TRUE

  FOR each session in phase:
    IF report.md does not exist OR status != completed:
      phase_complete = FALSE
      BREAK

  IF phase_complete:
    Mark phase for archiving
```

### 2. Create Archive Entry

For each completed phase:

```
archive_name = {date}_{plan-name}_{phase-name}
archive_path = .ctx/history/{archive_name}/

Create directory structure:
{archive_path}/
├── _summary.md      # Generated summary
├── _overview.md     # Copied from phase
└── {session}/
    ├── brief.md     # Copied
    └── report.md    # Copied
```

### 3. Generate Summary

Create `_summary.md` for the archived phase:

```markdown
# Archive: {phase-name}

Archived: {date}
Plan: {plan-name}
Duration: {first-session-date} to {last-session-date}

## Sessions

| Session | Status | Key Outcome |
|---------|--------|-------------|
| {name} | completed | {one-line from report} |
| {name} | completed | {one-line from report} |

## Key Decisions
{Aggregated from session reports}

## Discoveries
{Aggregated from session reports}

## Artifacts Produced
{List of files created/modified}

## Lessons Learned
{Synthesized from reports and scratchpad}
```

### 4. Archive the Plan (if complete)

When all phases are complete:

```
1. Copy plan.md to .ctx/history/{date}_{plan-name}/plan.md

2. Create .ctx/history/{date}_{plan-name}/_final.md:

   # Completed Plan: {name}

   Completed: {date}
   Duration: {start} to {end}

   ## Summary
   {overview from plan}

   ## Phases Completed
   - Phase 1: {name} - {summary}
   - Phase 2: {name} - {summary}

   ## Total Sessions: {n}

   ## Final State
   {What was achieved}

3. Reset plan.md to "No active plan"
```

### 5. Clean Up Sessions Directory

After archiving:

```
1. Remove archived phase directories from .ctx/sessions/
2. Keep any incomplete phases in place
3. Update state.md:
   - sessions completed: reset to 0 (for new plan)
   - last action: "archived {phase/plan}"
```

## Output Format

```
╔══════════════════════════════════════════════════════════════╗
║                     ARCHIVE COMPLETE                         ║
╠══════════════════════════════════════════════════════════════╣
║  Archived:                                                   ║
║    • Phase: {name} → history/{archive_name}/                 ║
║    • {n} sessions preserved                                  ║
║                                                              ║
║  Generated:                                                  ║
║    • _summary.md with key outcomes                           ║
║                                                              ║
║  Cleaned:                                                    ║
║    • Removed {phase}/ from sessions/                         ║
╠══════════════════════════════════════════════════════════════╣
║  Suggested: `next` to continue or `plan` for new work        ║
╚══════════════════════════════════════════════════════════════╝
```

## Archive Structure

Over time, history/ builds up:

```
.ctx/history/
├── 2024-01-15_auth-system/
│   ├── plan.md
│   ├── _final.md
│   ├── phase-1-setup/
│   │   ├── _summary.md
│   │   ├── _overview.md
│   │   └── sessions...
│   └── phase-2-implementation/
│       └── ...
├── 2024-02-01_api-refactor/
│   └── ...
└── 2024-03-10_testing/
    └── ...
```

## Partial Archive

If only some phases are complete:

```
1. Archive only complete phases
2. Keep plan.md active
3. Keep incomplete phases in sessions/
4. Update plan.md to mark archived phases:

   ### Phase 1: {name} ✓ ARCHIVED
   Archived to: history/{path}

   ### Phase 2: {name}
   {still active}
```

## Reference Archived Work

To reference past work:

```
1. Browse .ctx/history/ for past plans
2. Read _summary.md for quick overview
3. Read individual reports for details
4. Use insights to inform current work
```

## No Auto-Delete

The archive command copies, then deletes from active workspace.
Original files are never deleted without being archived first.
If archiving fails, active files remain untouched.
