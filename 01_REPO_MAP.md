# gh-dash Repository Map

## Basic Info
- **Repository**: dlvhdr/gh-dash
- **Language**: Go 1.25.8
- **Module**: github.com/dlvhdr/gh-dash/v4
- **License**: Proprietary (LICENSE.txt)
- **Docs**: Astro-based site at gh-dash.dev

## Top-Level Files
```
/root/hard-pr-1/repos/gh-dash/
├── README.md                           # Main entry point (HTML-heavy, 118 lines)
├── CONTRIBUTING.md                    # Contribution guidelines (121 lines)
├── AI_POLICY.md                        # AI usage policy
├── gh-dash.go                          # Entry point
├── go.mod / go.sum                     # Go modules
├── .gh-dash.yml                        # Example config
├── Taskfile.yaml                       # Dev tasks (lint, test, docs, etc.)
├── .golangci.yml                       # Linter config
├── .goreleaser.yaml                    # Release config
├── devbox.json / devbox.lock          # Dev environment
├── cmd/root.go                         # CLI root command (5712 bytes)
├── cmd/sponsors.go                      # Sponsors command
├── internal/
│   ├── config/                         # Configuration parsing
│   │   ├── parser.go                   # Main parser (825 lines)
│   │   ├── parser_test.go
│   │   ├── utils.go
│   │   ├── feature_flags.go
│   │   └── testdata/                   # Test fixtures
│   ├── data/                           # GitHub API data fetching
│   │   ├── prapi.go                    # PR GraphQL queries
│   │   ├── issueapi.go
│   │   ├── prapi_test.go
│   │   ├── donestore.go
│   │   ├── labelapi.go
│   │   ├── notificationapi.go
│   │   ├── userapi.go
│   │   ├── assignee.go
│   │   ├── bookmarks.go
│   │   └── commonapi.go                # Version checking, shared
│   ├── tui/                            # Terminal UI
│   │   ├── ui.go                       # Main model (54329 bytes)
│   │   ├── ui_test.go                  # (67994 bytes)
│   │   ├── modelUtils.go               # Helpers (10834 bytes)
│   │   ├── tasks.go
│   │   ├── keys/                      # Keybinding definitions
│   │   ├── components/                # TUI components
│   │   ├── constants/                  # Messages, constants
│   │   ├── context/                   # App context & styles
│   │   ├── markdown/                  # Markdown rendering
│   │   └── theme/                      # Theming
│   ├── git/                            # Git operations
│   └── utils/                          # Utilities
├── docs/                               # Astro docs site
└── testdata/                           # Test fixtures
```

## Architecture

### Go Module Structure
Single-module Go project (`github.com/dlvhdr/gh-dash/v4`) with internal packages:

- **`internal/config`** — YAML config parsing via `knadh/koanf`, validation with `go-playground/validator`
- **`internal/data`** — GitHub GraphQL API via `go-gh` and `shurcooL-graphql`
- **`internal/tui`** — Bubbletea TUI with `lipgloss` styling and `glamour` markdown
- **`internal/git`** — Local git operations
- **`internal/utils`** — Shared utilities

### Key Dependencies
| Package | Purpose |
|---------|---------|
| `charm.land/bubbletea/v2` | TUI framework |
| `charm.land/lipgloss/v2` | Terminal styling |
| `charm.land/glamour/v2` | Markdown rendering |
| `github.com/cli/go-gh/v2` | GitHub CLI integration |
| `github.com/spf13/cobra` | CLI commands |
| `github.com/go-playground/validator/v10` | Config validation |
| `github.com/knadh/koanf/v2` | Config file parsing |

### TUI Architecture (Elm-style)
- **Model** in `ui.go` — Main state container
- **Update** in `ui.go` — Event loop handling keys/messages
- **View** via tea components in `internal/tui/components/`

### Config System
- **Parser**: `internal/config/parser.go` — Loads YAML from `~/.config/gh-dash/config.yml` or `.gh-dash.yml`
- **Schema** (referenced in docs): `https://gh-dash.dev/schema.json` (hosted externally, not in repo)
- **Validation**: Uses `go-playground/validator/v10`
- **Template functions**: `nowModify` for relative time filtering

## Build & Release
- **Releaser**: `.goreleaser.yaml` — Cross-platform builds (darwin, linux, windows; amd64 + arm64)
- **GoReleaser check**: Issue #827 (i686 build failure)
- **Dev environment**: `devbox` + `direnv`

## Development Tools
- **Task runner**: `Taskfile.yaml` targets: `lint`, `lint:fix`, `test`, `debug`, `docs`
- **Linter**: `.golangci.yml` — golangci-lint configuration
- **Docs**: Astro site in `docs/` on `gh-dash.dev`
