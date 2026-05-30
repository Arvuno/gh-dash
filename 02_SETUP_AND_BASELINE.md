# gh-dash Setup and Baseline

## Go Version
```
go 1.25.8
```
Declared in `go.mod` line 3.

## Environment Note
Go is NOT installed in the current environment at `/usr/bin/go`. Devbox shell is required for local development per CONTRIBUTING.md:
```sh
devbox shell
```

## Install / Build
Per CONTRIBUTING.md, cloning and entering devbox shell:
```sh
git clone git@github.com:dlvhdr/gh-dash.git && cd gh-dash
curl -fsSL https://get.jetpack.io/devbox | bash
devbox shell
```

The project uses `go build` for local builds and goreleaser for releases.

## Available Build/Test Targets
From `Taskfile.yaml`:
```yaml
lint        # run golangci-lint
lint:fix   # auto-fix lint issues
test       # go test -p 4 ./...
debug      # run gh-dash in debug mode (tail debug.log)
docs       # serve docs locally
```

## Baseline Behavior (per README/docs)
1. User launches `gh dash` from a Git repository
2. TUI renders with sidebar sections (PRs, Issues, Notifications, Repo)
3. Default view configurable via `.gh-dash.yml`
4. Keyboard-driven navigation (vim-style hotkeys)
5. Data fetched from GitHub GraphQL API (~5 min refetch interval by default)
6. Supports per-repo config (`.gh-dash.yml`) and global config (`~/.config/gh-dash/config.yml`)

## Key Config Options (`.gh-dash.yml`)
```yaml
prSections:          # Per-repo PR sections
issuesSections:       # Per-repo issue sections
notificationsSections:
repositoriesSection:
defaults:
  view: prs|issues|notifications|repo
  refetchIntervalMinutes: 5
  prsLimit: 20
  issuesLimit: 20
  layout.prs.*         # PR list column config
  preview.open: bool   # Auto-open preview pane
keybindings:          # Custom keybindings
```

## Test & Lint Status
- **Test suite** lives across `_test.go` files in `internal/` subpackages
- CI runs `go test -p 4 ./...` via Taskfile
- Issue #827: i686 Linux build test failure (tabs rendering test flaky on 32-bit)
- Labels column rendering bug (#854): Release binary vs locally-built binary behaves differently (size delta ~9MB, suggests symbol stripping)

## Reported Issues Affecting Baseline
- Issue #816: "Loading forever" — never shows PRs on startup
- Issue #829: Display corruption when opening browser with `o` key
- Issue #811: Paste doesn't work in inputbox after Bubble Tea v2 upgrade
