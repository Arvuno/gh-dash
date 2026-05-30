# gh-dash Initial 2-PR Implementation Plan

## PR 1: Fix nil pointer panic in isUserDefinedKeybinding

### Branch Name
```
fix/nil-pointer-panic-isUserDefinedKeybinding
```

### Target Issue
#850 — panic: nil pointer dereference in isUserDefinedKeybinding when terminal sends OSC/CSI responses before model is initialized

### Target File
`internal/tui/ui.go`

### Implementation Notes

1. **Identify the function**: Locate `isUserDefinedKeybinding` in `ui.go` (reported at line 1416 in v4.22.0, line 1459 in v4.23.2)
2. **Add nil-guard at top of function**:
```go
func (m *Model) isUserDefinedKeybinding(msg key.Msg) (bool, tea.Cmd) {
    if m.ctx == nil || m.ctx.Config == nil {
        return false, nil
    }
    // ... existing logic
}
```
3. **Why this fix**: The `Update` function can receive key messages from terminal responses (OSC 11 background-color, CSI cursor-position) before context is initialized. These messages get parsed as `tea.KeyMsg` and forwarded to `isUserDefinedKeybinding` which dereferences `m.ctx.Config.*` without nil-checking `m.ctx`.

4. **Testing**: 
   - Run `gh dash` in a terminal that sends early OSC/CSI responses (Wave Terminal, xterm-style)
   - Verify existing keybindings still work normally
   - Unit test not required per maintainer patterns (see CONTRIBUTING.md focus on debug logging)

5. **Lint**: Run `task lint:fix` before commit

---

## PR 2: Fix crash in UserMentionSource.ExtractContext (slice bounds)

### Branch Name
```
fix/mention-extract-slice-bounds-panic
```

### Target Issue
#877 — [BUG] TUI crashes while writing a comment containing @user: ""

### Description
When typing a comment with format `@<username>: ""`, the TUI crashes with `runtime error: slice bounds out of range [28:27]` in `fuzzyselect/mentions.go:67`.

### Target File
`internal/tui/components/fuzzyselect/mentions.go`

### Implementation Notes

1. **Locate the bug**: In `ExtractContext` method of `UserMentionSource`:
   - The method parses mentions from text input
   - When encountering `@user: ""` pattern, slice indices go negative or out of bounds
   - Specifically, `mentions[mentionIdx]` access fails

2. **Add bounds validation before slice operations**:
```go
// Before accessing mentions slice or parsed mentions
if mentionIdx < 0 || mentionIdx >= len(mentions) {
    return "", nil
}
```
Or within the parsing loop:
```go
for _, mention := range parsedMentions {
    if mention.End > len(text) || mention.Start < 0 {
        continue // skip malformed mention
    }
    // ... existing logic
}
```

3. **Alternative fix**: If the bug is in extracting the username from `@user:""` pattern — the regex for `extractorRegex` may not handle `: ""` suffix properly. Check if parsing `@username` correctly handles trailing punctuation.

4. **Testing**: 
   - Open PR comment box (press `c` in PR view)
   - Type `@someuser: ""` followed by more text
   - Verify no panic and autocomplete still works

5. **Lint**: Run `task lint:fix` before commit

---

## Parallel Execution

These two PRs are independent and can be worked on simultaneously:

| PR | Branch | Files | Lines (est) |
|----|--------|-------|------------|
| 1 | `fix/nil-pointer-panic-isUserDefinedKeybinding` | `internal/tui/ui.go` | ~5 |
| 2 | `fix/mention-extract-slice-bounds-panic` | `internal/tui/components/fuzzyselect/mentions.go` | ~10-15 |

## Pre-PR Checklist
- [ ] Clone fresh copy of repo
- [ ] Create feature branch from main
- [ ] Implement fix
- [ ] Run `task lint` — fix any issues
- [ ] Run `task test` — verify no regressions
- [ ] Ensure debug mode (`task debug`) runs without new warnings
- [ ] Test on actual terminal with conditions described in issue
- [ ] Open PR with reference to issues
- [ ] Respond to maintainer feedback within 48h

## If Maintainer Has Already Filed PR for #850
If the reporter of #850 indicated PR incoming and it has been merged since we began:
- Abandon our PR 1 for C-001
- Proceed directly to C-003 (the mentions crash fix) as PR 1
- Move C-007 (issue title) or C-004 (typo) as PR 2

## Commit Message Format
```
fix(ui): prevent nil pointer dereference in isUserDefinedKeybinding

Appends nil check on m.ctx before accessing m.ctx.Config in the
keybinding resolution code path that can be entered by early terminal
OSC/CSI responses.

Fixes #850
```

```
fix(fuzzyselect): handle slice bounds in ExtractContext for @mention patterns

When typing @user: "" in comment box, the mention extraction went out of
bounds. Added bounds validation to skip malformed mentions.

Fixes #877
```
