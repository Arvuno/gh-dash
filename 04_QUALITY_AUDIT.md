# gh-dash Quality Audit

## 1. README Gaps

### 1.1 No Installation Instructions
README says "docs at gh-dash.dev/getting-started" but no quick install command upfront. First-time users must navigate to docs site just to know `gh extension install dlvhdr/gh-dash`.

**Recommendation**: Add `gh extension install dlvhdr/gh-dash` or `go install` one-liner at top of README.

### 1.2 No Usage Examples
README is visually rich (logos, GIF, sponsors) but lacks:
- Example `.gh-dash.yml` showing actual sections/filters
- Description of what the keybindings do
- Explanation of view types (prs/issues/notifications/repo)

### 1.3 No Keyboard Shortcut Reference
No built-in shortcut reference in README. CONTRIBUTING.md has more detail on debugging than README has on usage.

### 1.4 Dead Badge Link
Issue #897: Badge link to devbox is dead (`www.jetify.com/docs/devbox`).

## 2. Config Validation Gaps

### 2.1 Schema Not in Repository
Issue #846: The JSON Schema referenced in config comments (`$schema=https://gh-dash.dev/schema.json`) is hosted externally at `gh-dash.dev`. If the schema has errors (`properties: null` in `schema/layout/pr.json` per issue #846), users with yaml-language-server get validation errors with no ability to fix/.contribute.

### 2.2 Validator struct tag issues
`internal/config/parser.go` uses `go-playground/validator/v10`. Error messages returned by validator can be cryptic (e.g., field path dots without context). No custom error rendering for config validation failures.

### 2.3 No Config Validation on Load
If config has errors, does gh-dash fail gracefully or silently ignore bad fields? Evidence: Issue #854 shows config parsing produces identical output in affected cases — config loading appears functional, but error handling for bad values is unclear.

### 2.4 Inconsistent Config Delim
`const Delim = "."` in koanf config but YAML uses nested structures with dashes. Comment in parser.go: `// TODO: use this` for `ConfigYamlFileName`.

## 3. Docs Gaps

### 3.1 No Complete Config Schema Documentation
The schema at `gh-dash.dev/schema.json` (external) isn't documented in the repo's docs. Users can't understand all available layout fields without external lookup.

### 3.2 No Custom Action/Command Documentation
README mentions "custom actions" but there's no docs/example in main README. CONTRIBUTING.md says "See contribution guide" but the main docs site URL would need to be followed.

### 3.3 No Architecture/Security Design Docs
- No SECURITY.md
- AI_POLICY.md is present but unusual (AI policy is typically a CONTRIBUTING section, not separate top-level file)

## 4. Missing Error Handling

### 4.1 Panic on Startup (Multiple Unguarded Nil Derefs)
Issues #850, #876, and #877 all involve runtime panics:

- **#850**: `isUserDefinedKeybinding` at `ui.go:1416` dereferences `m.ctx.Config` without nil-checking `m.ctx`
- **#876**: `GetMarkdownRenderer` at `markdown/markdownRenderer.go` dereferences `markdownStyle` without nil check
- **#877**: `ExtractContext` at `fuzzyselect/mentions.go:67` has slice bounds error

### 4.2 Exit Status 4 on Pager (Issue #866)
Pressing `d` to diff returns `exit status 4` for multiple diff tools (``, `less`, `delta`). The external command execution in `data/commonapi.go` or `git/git.go` has no error handling for pager failures — exits silently with status 4.

### 4.3 No Graceful Degradation for Network Failures
`internal/data/` API calls hit GitHub GraphQL. If network fails:
- Logs show `DEBU ... Successfully fetched` (false positives?) per issue #816
- No user-visible error message in TUI for fetch failures
- Issue #816 "Loading forever" suggests poor error handling on auth/network failures

### 4.4 Custom Command Template Errors
`modelUtils.go:229`: `fmt.Errorf("failed to parsetemplate %s")` returns error but called in `runCustomCommand` which may not surface these errors to user in all code paths.

### 4.5 Git Operations Error Handling
`internal/git/git.go:60`: `return "", errors.New("no origin remote found")` — bare error, no context. Similarly in `prssection/checkout.go` and `issueview/checkout.go`.

## 5. API/Data Layer Issues

### 5.1 No Rate Limiting Handling
GraphQL API calls in `data/` have no visible rate limit handling. GitHub's GraphQL has strict limits — no exponential backoff visible in code.

### 5.2 Version Check on Every Launch
`data/commonapi.go:32` fetches latest version on every run via `data/commonapi.go`. Adds startup latency; no caching of version check result.

## 6. Test Coverage Gaps

### 6.1 UI Tests are Large but Same-Package
`ui_test.go` (67994 bytes) is a large single-file test. Key functions like `isUserDefinedKeybinding` and `GetMarkdownRenderer` have no dedicated unit tests.

### 6.2 Flaky Tabs Test (Issue #827)
`TestTabs/Should_show_overflow_symbol` in `tabs_test.go` fails on i686 due to font/terminal rendering differences. Test is timing-dependent (1s timeout).

### 6.3 No Integration Tests
No test files exercise the full config load → data fetch → render cycle.

## 7. Security/Observability

### 7.1 No Structured Logging Standardization
Logs use `log.Debug`, `log.Info`, `log.Warn` inconsistently. No correlation IDs across task IDs shown in logs (task IDs like `fetching_prs_1_2026-05-06 22:48:07` in issue #866 debug log).

### 7.2 Debug Mode Leaks Potentially Sensitive Info
Debug logs shown in issue #866 include repo paths, URLs. `--debug` flag could leak sensitive data when debug logs shared publicly.

## 8. Release/Binary Issues

### 8.1 Binary Size Discrepancy (Issue #854)
Release binary: 20,607,152 bytes vs locally-built: 29,456,610 bytes. 9MB delta suggests aggressive symbol stripping in goreleaser config affecting label rendering — likely `goreleaser` flags stripping debug/symbol info that label rendering depends on.
