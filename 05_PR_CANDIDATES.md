# gh-dash PR Candidates

## 10 Candidates Derived from Issues + Quality Audit

---

### Candidate 1: Fix nil pointer panic in isUserDefinedKeybinding
- **ID**: C-001
- **Title**: Fix nil pointer dereference in isUserDefinedKeybinding at startup
- **Type**: Bug fix
- **Linked Issue**: #850
- **Source**: Issue triage
- **Risk**: Low — single nil-check guard
- **Size**: XS (3-line change)
- **Mergeability**: High — isolated fix, well-understood root cause
- **Selected**: —
- **Files**: `internal/tui/ui.go`
- **Notes**: Reporter indicated PR incoming. Nil-guard on `m.ctx` needed at top of `isUserDefinedKeybinding`.

---

### Candidate 2: Fix nil pointer dereference in markdown renderer
- **ID**: C-002
- **Title**: Fix nil pointer dereference in GetMarkdownRenderer when BackgroundColorMsg not received
- **Type**: Bug fix
- **Linked Issue**: #876
- **Source**: Issue triage
- **Risk**: Low — nil-check guard with fallback
- **Size**: XS (5-8 lines)
- **Mergeability**: High — isolated fix
- **Selected**: —
- **Files**: `internal/tui/markdown/markdownRenderer.go`
- **Notes**: Need to handle `markdownStyle` being nil when terminal doesn't respond to `tea.RequestBackgroundColor`. Use default style as fallback.

---

### Candidate 3: Fix crash in UserMentionSource.ExtractContext
- **ID**: C-003
- **Title**: Fix slice bounds panic in fuzzyselect mentions when typing @user: ""
- **Type**: Bug fix
- **Linked Issue**: #877
- **Source**: Issue triage
- **Risk**: Low — bounds check in string parsing
- **Size**: S (10-15 lines)
- **Mergeability**: High — contained to mentions.go
- **Selected**: —
- **Files**: `internal/tui/components/fuzzyselect/mentions.go`
- **Notes**: Slice index goes negative when processing trailing `""` after `@user:`. Add bounds validation.

---

### Candidate 4: Fix typo "Non requested" → "None requested"
- **ID**: C-004
- **Title**: Fix typo: "Non requested" should be "None requested" in PR checks sidebar
- **Type**: Bug fix (typo)
- **Linked Issue**: #801
- **Source**: Issue triage
- **Risk**: None — string replacement
- **Size**: XS (1 line)
- **Mergeability**: High — trivial
- **Selected**: —
- **Files**: `internal/tui/components/prview/checks.go` (or similar)
- **Notes**: "Non requested" is grammatically incorrect. Simple grep to find.

---

### Candidate 5: Fix JSON schema properties: null
- **ID**: C-005
- **Title**: Fix invalid JSON schema: remove properties: null from schema/layout/pr.json
- **Type**: Docs/Config fix
- **Linked Issue**: #846
- **Source**: Issue triage
- **Risk**: Low — schema property removal
- **Size**: XS (1 line in JSON)
- **Mergeability**: Medium — schema is externally hosted at gh-dash.dev. Fix in-repo is step 1, but schema deploy is separate.
- **Selected**: —
- **Files**: Schema file (needs to be created/identified in docs or external)
- **Notes**: Issue notes schema is not in repo. Fix may require PR to gh-dash.dev docs repo or coordination with maintainer.

---

### Candidate 6: Add pane resizing support
- **ID**: C-006
- **Title**: Add keyboard shortcut to resize PR list and detail panes
- **Type**: Feature request
- **Linked Issue**: #848
- **Source**: Issue triage
- **Risk**: Medium — layout changes for all users
- **Size**: M (may require resize handle component)
- **Mergeability**: High — additive feature, opt-in via keybinding
- **Selected**: —
- **Files**: TUI layout components
- **Notes**: Feature request for resize capability. Need to identify which layouts support resizing (prssection, issueview, etc.)

---

### Candidate 7: Add issue title to custom issue command arguments
- **ID**: C-007
- **Title**: Expose IssueTitle in custom issue command template variables
- **Type**: Feature enhancement
- **Linked Issue**: #840
- **Source**: Issue triage
- **Risk**: None — additive template variable
- **Size**: XS (1-liner in addRowData)
- **Mergeability**: High — simple additive change
- **Selected**: —
- **Files**: `internal/tui/modelUtils.go`
- **Notes**: Issue author identified exact location. Title already fetched in struct.

---

### Candidate 8: Fix notifications cursor behavior when last item marked done
- **ID**: C-008
- **Title**: Keep cursor on new last item when last notification marked as done
- **Type**: Bug fix
- **Linked Issue**: #858
- **Source**: Issue triage
- **Risk**: Low — cursor/index adjustment only
- **Size**: S (10-20 lines)
- **Mergeability**: High — isolated notification section logic
- **Selected**: —
- **Files**: `internal/tui/components/notificationssection/notificationssection.go`
- **Notes**: Cursor stays at index after deletion. When deleting last item, need to move cursor to previous (now-last) item.

---

### Candidate 9: Add built-in auto-merge keybinding
- **ID**: C-009
- **Title**: Add built-in auto-merge keybinding to PR actions
- **Type**: Feature request
- **Linked Issue**: #805
- **Source**: Issue triage
- **Risk**: Medium — modifies GitHub PR state
- **Size**: M (new command + API call)
- **Mergeability**: Medium — requires GitHub API integration, confirmation dialog
- **Selected**: —
- **Files**: `internal/tui/keys/prKeys.go`, `internal/data/prapi.go` (add autoMergeRequest field)
- **Notes**: Many moving parts. Workaround exists via custom keybinding. Built-in would need confirmation prompt and visual indicator.

---

### Candidate 10: Fix display corruption when opening browser (o key)
- **ID**: C-010
- **Title**: Fix display corruption after opening PR in browser with 'o' key
- **Type**: Bug fix
- **Linked Issue**: #829
- **Source**: Issue triage + Quality audit
- **Risk**: Medium — terminal state handling
- **Size**: M (terminal restore sequence)
- **Mergeability**: Medium — occurs across multiple terminals, hard to test locally
- **Selected**: —
- **Files**: `internal/tui/ui.go`, browser opening code paths
- **Notes**: This is a known issue (#679, #584) with display corruption on browser open. Likely needs terminal restore sequence after external command. May require ` TEA`程序 careful terminal cleanup.
