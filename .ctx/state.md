# State

## Mode
idle

## Active Session
none

## Active Phase
none

## Context Loaded
- project.md

## Last Action
initialized

## Blockers
none

---

## Waiting Info
(Only used when mode = waiting)

### Waiting For
none

### Waiting Since
n/a

---

## Progress Tracking

### Sessions Completed
0

### Sessions Blocked
0

### Current Streak
0

### Last Reflect
never

---

## Recovery Info

### Last Error
none

### Error Count
0

### Recovery Action
none

---

## Transition Hints

The system suggests mode transitions based on these conditions:

| Condition | Suggested Action |
|-----------|------------------|
| No active plan | Run `plan` |
| Plan exists, no active session | Run `next` |
| 3+ sessions completed since last reflect | Run `reflect` |
| Scratchpad has 5+ items | Run `reflect` |
| Session blocked | Run `recover` |
| Phase completed | Run `reflect`, then `next` |
| All sessions complete | Run `archive` |
| External dependency needed | Enter `waiting` mode |
