# Validate Command

You are running the `validate` command. Your job is to check system consistency and report any issues.

## Purpose

Detect and report:
- State inconsistencies
- Missing files referenced in state
- Orphaned sessions
- Invalid references
- Structural issues

## Validation Checks

### 1. State File Integrity

```
CHECK: state.md exists and is readable
CHECK: state.md has required sections:
  - Mode (valid value: idle|executing|planning|reflecting|recovering|waiting)
  - Active Session
  - Active Phase
  - Context Loaded
  - Last Action
  - Blockers
  - Progress Tracking
  - Recovery Info

ERRORS:
  - missing_state: "state.md not found or unreadable"
  - invalid_mode: "Mode '{value}' is not valid"
  - missing_section: "Required section '{name}' missing from state.md"
```

### 2. Active Session Consistency

```
IF state.mode = executing THEN
  CHECK: Active Session != none
  CHECK: Active Phase != none
  CHECK: .ctx/sessions/{phase}/{session}/brief.md exists

ERRORS:
  - orphan_execution: "Mode is executing but no active session set"
  - missing_brief: "Active session '{session}' has no brief.md"
  - phase_mismatch: "Active session not found in active phase"
```

### 3. Plan Consistency

```
IF plan.md exists AND plan.md != "No active plan" THEN
  CHECK: plan.md has valid structure (Overview, Phases, Dependencies)

  FOR each phase mentioned in plan:
    CHECK: .ctx/sessions/{phase}/ directory exists
    CHECK: .ctx/sessions/{phase}/_overview.md exists

  FOR each session mentioned in plan:
    CHECK: .ctx/sessions/{phase}/{session}/ directory exists
    CHECK: .ctx/sessions/{phase}/{session}/brief.md exists

ERRORS:
  - invalid_plan: "plan.md structure is invalid"
  - missing_phase_dir: "Phase '{phase}' directory not found"
  - missing_overview: "Phase '{phase}' missing _overview.md"
  - missing_session_dir: "Session '{session}' directory not found"
  - missing_session_brief: "Session '{session}' missing brief.md"
```

### 4. Dependency Validity

```
FOR each session brief:
  Read "Requires:" line
  IF requires != none THEN
    FOR each dependency:
      CHECK: dependency session exists
      CHECK: no circular dependencies

ERRORS:
  - invalid_dependency: "Session '{a}' depends on non-existent '{b}'"
  - circular_dependency: "Circular dependency: {a} → {b} → ... → {a}"
```

### 5. Report Consistency

```
FOR each session with report.md:
  CHECK: report.md has valid status (completed|partial|blocked)
  CHECK: completed sessions have "What Was Done" section
  CHECK: blocked sessions have "Blockers" section

WARNINGS:
  - incomplete_report: "Report for '{session}' missing required sections"
  - status_mismatch: "Report status doesn't match session state"
```

### 6. Memory Consistency

```
CHECK: .ctx/memory/project.md exists
CHECK: project.md is not empty

FOR each module in .ctx/memory/modules/:
  CHECK: module.md has required sections (Purpose, Key Files)

WARNINGS:
  - empty_project: "project.md is empty"
  - incomplete_module: "Module '{name}' missing required sections"
```

### 7. Orphan Detection

```
FOR each directory in .ctx/sessions/:
  CHECK: phase is mentioned in plan.md

FOR each session directory:
  CHECK: session is mentioned in plan.md OR has report.md with completed status

WARNINGS:
  - orphan_phase: "Phase '{phase}' not in plan.md"
  - orphan_session: "Session '{session}' not in plan.md"
```

### 8. Progress Tracking Accuracy

```
Count actual completed sessions from reports
Compare to state.md "Sessions Completed" value

WARNINGS:
  - count_mismatch: "State shows {n} completed but found {m}"
```

## Output Format

```
╔══════════════════════════════════════════════════════════════╗
║                    VALIDATION REPORT                         ║
╠══════════════════════════════════════════════════════════════╣
║  Status: {VALID | WARNINGS | ERRORS}                         ║
╠══════════════════════════════════════════════════════════════╣

ERRORS (must fix):
  ✗ {error_code}: {description}
  ✗ {error_code}: {description}

WARNINGS (should fix):
  ⚠ {warning_code}: {description}
  ⚠ {warning_code}: {description}

PASSED:
  ✓ State file integrity
  ✓ Active session consistency
  ✓ Plan structure
  ✓ Dependencies valid
  ✓ Reports complete
  ✓ Memory files present
  ✓ No orphans
  ✓ Progress tracking accurate

╠══════════════════════════════════════════════════════════════╣
║  {n} errors, {m} warnings                                    ║
╚══════════════════════════════════════════════════════════════╝
```

## Auto-Fix Option

For certain issues, offer to fix automatically:

| Issue | Auto-Fix |
|-------|----------|
| count_mismatch | Recalculate from reports |
| missing_phase_dir | Create directory |
| missing_overview | Create template _overview.md |
| orphan_session (completed) | Move to history |

```
Auto-fixable issues found. Run `validate --fix` to apply:
  - Recalculate session count
  - Create missing directories
```

## No State Changes (unless --fix)

By default, validate is read-only. Only with explicit --fix flag will it modify files.

## When to Run

- Before starting a new phase
- After recovering from errors
- When behavior seems inconsistent
- Periodically as a health check
