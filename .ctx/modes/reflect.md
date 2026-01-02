# Reflect Mode

You are in reflect mode. Your job is to consolidate learning and update project memory.

## When to Reflect

- After completing a phase
- When project.md feels stale
- When scratchpad has accumulated notes
- When context feels fragmented

## Inputs

1. `.ctx/sessions/{phase}/*/report.md` - Recent session reports
2. `.ctx/scratchpad.md` - Accumulated notes
3. `.ctx/memory/project.md` - Current project understanding
4. `.ctx/memory/modules/` - Module summaries
5. Actual codebase - Verify understanding matches reality

## Process

1. **Review recent work** - Read session reports since last reflect
2. **Process scratchpad** - Categorize notes, resolve what's possible
3. **Update project.md** - Revise structure, architecture, patterns
4. **Update module summaries** - Add/revise based on new understanding
5. **Clean scratchpad** - Remove processed items, keep open questions
6. **Archive if needed** - Move completed phases to history

## Memory Guidelines

**project.md should contain:**
- Project purpose and structure
- Key architectural decisions
- File organization patterns
- Important conventions
- Entry points and flows

**Module summaries should contain:**
- Purpose of the module
- Key files and their roles
- Public interfaces
- Dependencies
- Patterns used

**Keep it minimal:**
- If you can re-derive it from code, don't store it
- Focus on "why" not "what"
- Summaries, not documentation

## Output

Update these files:
- `.ctx/memory/project.md` - Revised understanding
- `.ctx/memory/modules/*.md` - New or updated summaries
- `.ctx/scratchpad.md` - Cleaned up
- `.ctx/state.md` - Update last action

## When Done

Set state.md:
- mode: idle
- last action: "reflected on {phase/work}"
- Reset `Current Streak` to 0
- Update `Last Reflect` to current date

Summarize what was updated for the user.

---

## Mode Transitions

**After reflecting, suggest based on state:**

| Condition | Suggestion |
|-----------|------------|
| More sessions pending | `next` - Continue work |
| Plan complete | Celebrate, ask about next goal |
| Issues discovered during reflect | `plan` to address them |
| Project understanding changed significantly | Review plan validity |

**Automatic reflect triggers (check in other modes):**
- 3+ sessions completed since last reflect
- Scratchpad has 5+ unprocessed items
- Phase boundary reached
- project.md feels stale (subjective)
- Before starting a complex new phase

## Reflect Checklist

Before marking reflect complete:
- [ ] Session reports reviewed
- [ ] Scratchpad processed (items moved or deleted)
- [ ] project.md updated if needed
- [ ] Module summaries updated if needed
- [ ] Completed phases archived to history/
- [ ] State.md progress tracking updated
