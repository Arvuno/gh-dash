# gh-dash Selected 5-PR Plan

## Selection Rationale

Selected PRs based on:
1. **Severity** — Panic-level bugs that crash the app (C-001, C-003)
2. **Clarity** — Issues with clear problem statements and known fix locations (C-001, C-003, C-004)
3. **Size** — XS/S candidates that can ship quickly (C-001, C-003, C-004, C-007)
4. **Low risk** — No behavioral changes to existing features, isolated changes
5. **User impact** — Large number of users affected by startup panic (C-001)

---

## Selected PR 1: C-001 — Fix nil pointer panic in isUserDefinedKeybinding

| Field | Value |
|-------|-------|
| **Candidate ID** | C-001 |
| **Title** | Fix nil pointer dereference in isUserDefinedKeybinding at startup |
| **Type** | Bug fix |
| **Risk** | Low — nil-check guard |
| **Size** | XS |
| **Rationale** | High-severity panic (#850) that crashes on launch for users with terminals that send OSC/CSI responses early. Fix is isolated to 3-line guard. Reporter says PR incoming. Lower priority than actively crashing bugs, but fixes unhandled nil deref pattern. |
| **Files** | `internal/tui/ui.go` |
| **Implementation** | Add nil-check `if m.ctx == nil || m.ctx.Config == nil { return false, nil }` at top of `isUserDefinedKeybinding` |

---

## Selected PR 2: C-003 — Fix crash in UserMentionSource.ExtractContext

| Field | Value |
|-------|-------|
| **Candidate ID** | C-003 |
| **Title** | Fix slice bounds panic in fuzzyselect mentions when typing @user: "" |
| **Type** | Bug fix |
| **Risk** | Low — bounds check |
| **Size** | S |
| **Rationale** | Actively crashes (#877) when user types `@<user>: ""` in comment box. User-facing crash blocking comment functionality. Clear stack trace pointing to `mentions.go:67`. Fix is contained to string parsing in mentions extractor. |
| **Files** | `internal/tui/components/fuzzyselect/mentions.go` |
| **Implementation** | Add bounds validation before slice access in `ExtractContext`. Check if parsedMention.End > len(text) or if indices go negative after processing. |

---

## Selected PR 3: C-007 — Add issue title to custom issue command arguments

| Field | Value |
|-------|-------|
| **Candidate ID** | C-007 |
| **Title** | Expose IssueTitle in custom issue command template variables |
| **Type** | Feature enhancement |
| **Risk** | None — additive |
| **Size** | XS |
| **Rationale** | Requested in #840 with exact location identified by author. 1-liner change — Title is already fetched in struct but not exposed to template. High value (enables automation), no risk. Good stepping-stone PR to build relationship with maintainers. |
| **Files** | `internal/tui/modelUtils.go` |
| **Implementation** | In `runCustomIssueCommand`, add `"IssueTitle": issueData.Title` to the map[string]any passed to `runCustomCommand` alongside existing fields. |

---

## Selected PR 4: C-004 — Fix typo "Non requested" → "None requested"

| Field | Value |
|-------|-------|
| **Candidate ID** | C-004 |
| **Title** | Fix typo: "Non requested" should be "None requested" in PR checks sidebar |
| **Type** | Bug fix (typo) |
| **Risk** | None |
| **Size** | XS |
| **Rationale** | Trivial fix (1-line string replacement) that shows attention to polish. User-facing minor issue in PR view. No chance of regression. Good first PR candidate for community contributor — low stakes, clear fix. |
| **Files** | `internal/tui/components/prview/checks.go` |
| **Implementation** | Grep for "Non requested" string, replace with "None requested". Verify no other instances in codebase. |

---

## Selected PR 5: C-002 — Fix nil pointer dereference in markdown renderer

| Field | Value |
|-------|-------|
| **Candidate ID** | C-002 |
| **Title** | Fix nil pointer dereference in GetMarkdownRenderer when BackgroundColorMsg not received |
| **Type** | Bug fix |
| **Risk** | Low — fallback style |
| **Size** | S |
| **Rationale** | Secondary panic (#876) on terminals that don't respond to `tea.RequestBackgroundColor`. Affects WSL/Alacritty/tmux users. Fix adds nil check with fallback default style rather than crashing. |
| **Files** |=`internal/tui/markdown/markdownRenderer.go` |
| **Implementation** | Check if `markdownStyle` is nil before use. If nil, initialize with default style (extract from existing code or use sensible terminal defaults). Add doc comment explaining why nil is possible. |

---

## Summary Table

| Priority | ID | Title | Type | Risk | Size |
|----------|----|-------|------|------|------|
| 1 | C-001 | Fix nil pointer panic in isUserDefinedKeybinding | Bug | Low | XS |
| 2 | C-003 | Fix crash in UserMentionSource.ExtractContext | Bug | Low | S |
| 3 | C-007 | Add issue title to custom issue command arguments | Feature | None | XS |
| 4 | C-004 | Fix typo "Non requested" → "None requested" | Bug | None | XS |
| 5 | C-002 | Fix nil pointer dereference in markdown renderer | Bug | Low | S |

## Anticipated Challenges
- **C-001**: Need to verify exact line in current `ui.go` — line numbers may have shifted since issue #850 was filed
- **C-003**: Need to parse `mentions.go` `ExtractContext` to understand full logic before adding bounds check
- **C-002**: Need to understand the style initialization chain to provide correct default fallback

## Out of Scope (for this 5-PR plan)
- C-005: Schema not in-repo, requires external coordination
- C-006: Pane resizing — more complex UI work, could be follow-up
- C-008: Notifications cursor — needs careful cursor state management review
- C-009: Auto-merge — too many moving parts for initial PRs
- C-010: Display corruption — hard to test locally, known long-standing issue
