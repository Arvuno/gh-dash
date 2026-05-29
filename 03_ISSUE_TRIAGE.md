# gh-dash Issue Triage

## Issue Summary (30 most recent --state all)

| # | Title | Labels | Type | Priority |
|---|-------|--------|------|----------|
| 900 | Add a command for `acceptSelection` | — | feat | Feature request |
| 897 | badge link to devbox is dead | — | docs | Docs |
| 877 | [BUG] TUI crashes while writing a comment containing @user: "" | bug | Bug | High (panic) |
| 876 | [BUG] TUI crashes with nil pointer dereference when BackgroundColorMsg not reached | bug | Bug | High (panic) |
| 867 | `notifications` as an option in the default view | feat | Feature request | Feature |
| 866 | [BUG] pager issue | bug | Bug | Bug |
| 858 | For notifications, after marking last item as done keep cursor on new last item | bug | Bug | Bug |
| 854 | [BUG] Labels column not rendering with released binary; works when built locally | bug | Bug | High (regression) |
| 852 | PR activity view flattens review threads and drops the code context | feat | Feature request | Feature |
| 850 | [BUG] panic: nil pointer dereference in isUserDefinedKeybinding when terminal sends OSC/CSI responses | — | Bug | High (panic) |
| 848 | [FEATURE REQUEST] Resize panes | feat | Feature request | Feature |
| 846 | JSON Schema validation error: `properties: null` is invalid | — | Docs/Config | Medium |
| 843 | Add more fields in the `PR view` | feat | Feature request | Feature |
| 842 | [BUG] `u` temporarily closes the PR during update from base branch | bug | Bug | Bug |
| 840 | Provide issue title as issue command argument | — | Feature request | Feature |
| 835 | [Feature Request] Ability to hide individual PRs | feat | Feature request | Feature |
| 834 | Improve completion popup styling | — | Feature request | Feature |
| 831 | [BUG] Label suggestions missing descriptions | bug | Bug | Bug |
| 830 | Cycle sidebar tabs switching | feat | Feature request | Feature |
| 829 | [BUG] Opening to a browser corrupts display | bug | Bug | Bug (display) |
| 827 | Is i686 supported? | feat | CI/Build | CI |
| 822 | System notification on new pull request Needs My Review | feat | Feature request | Feature |
| 816 | [BUG] Loading forever | bug | Bug | High (blocks usage) |
| 815 | [FEATURE REQUEST] Add shortcut to copy branch name | feat | Feature request | Feature |
| 812 | [BUG] crashed on navigating multiple windows | bug | Bug | High (crash) |
| 811 | [BUG] No longer can paste in inputbox | bug | Bug | Bug (regression) |
| 807 | Requesting reviewers by username on PRs from gh-dash | feat | Feature request | Feature |
| 806 | [BUG] No confirmation displayed after pressing `x` to close a PR | bug | Bug | Bug |
| 805 | Feature Request: Add built-in auto-merge keybinding support | feat | Feature request | Feature |
| 801 | [BUG] Typo in pull request sidebar | bug | Bug | Low (typo) |

## Category Breakdown (30 issues)
- **Bug**: 17 (55%)
- **Feature request**: 11 (37%)
- **Docs/Config**: 1 (3%)
- **CI/Build**: 1 (3%)

## Issues Selected for PR Candidates (clear problem statements)

### 1. Issue #850 — [BUG] panic nil pointer dereference in isUserDefinedKeybinding
**Severity**: High (panic on launch)
**Problem**: `m.ctx` is nil at startup when certain terminals send OSC/CSI responses before model initialization, causing panic in `isUserDefinedKeybinding` at `ui.go:1416`.
**Fix**: 3-line nil-guard at top of `isUserDefinedKeybinding` function. Contributor says PR incoming.

### 2. Issue #877 — [BUG] TUI crashes while writing comment with @user: ""
**Severity**: High (crash when commenting)
**Problem**: `runtime error: slice bounds out of range [28:27]` in `ExtractContext` in `fuzzyselect/mentions.go:67` when user types `@<person>: ""` format.
**Location**: `internal/tui/components/fuzzyselect/mentions.go:67` — slice index miscalculation.

### 3. Issue #876 — [BUG] TUI crashes with nil pointer dereference when BackgroundColorMsg not reached
**Severity**: High (panic on color-related terminal setups)
**Problem**: `markdownStyle` is nil because `case tea.BackgroundColorMsg` never fires on certain terminals (e.g., WSL/Alacritty/tmux), causing nil pointer dereference in `GetMarkdownRenderer`.
**Location**: `internal/tui/markdown/markdownRenderer.go` — no nil check before dereferencing.

## Low-Hanging Fruit Candidates

| Issue | Title | Why Easy |
|-------|-------|---------|
| #801 | Typo: "Non requested" vs "None requested" | Simple string fix in checks output |
| #846 | JSON Schema `properties: null` invalid | Remove null from schema JSON |
| #840 | Provide issue title as issue command argument | 1-liner in modelUtils.go |
| #806 | No confirmation after `x` to close PR | Missing confirmation prompt |
| #867 | `notifications` as default view option | Add ViewType case |
| #815 | Copy branch name shortcut | Add keybinding + command |
