# my-opencode-settings

My own OpenCode Settings

## What's Included

- Non-Thinking variants for z.ai coding plan models
- `context-mode`
- `codegraph`
- `rtk`
- custom Go setting with
  - `golangci-lint`
- custum Python setting
  - `ruff`
  - `ty`
  - `pyrefly`
- custom Rust setting
  - disable `rust-analyzer` to save resources
- skills
  - `simple-agent-memory`
  - `ddd-layout-rules`
  - `conventional-commit`
  - `karpathy-guidelines`

## Instructions

### Pre-requisites

- Install [grd](https://github.com/lucidfrontier45/grd)

### Base Settings

`opencode.jsonc` already contains following settings. Copy it to `~/.config/opencode/opencode.jsonc` or your project root.

- Anthropic API based Z.AI coding plan provider
- context-mode
- codegraph
- disable rust-analyzer to save resources
- golangci-lint
- ruff
- ty
- pyrefly (disabled by default)

### codegraph

`bun i -g @colbymchenry/codegraph@latest`

Check the official site to see how to index your own project.
https://colbymchenry.github.io/codegraph/

### RTK

1. Install binary : `grd -d .local/bin/ rtk-ai/rtk`
2. Initialize: `rtk init -g opencode`

### Context Mode

`bun i -g context-mode@latest`

### Skills

1. copy `skills/*` folder to your project or `~/.agents/skills`
2. `bunx skills add https://github.com/marcelorodrigo/agent-skills --skill conventional-commit`
3. `bunx skills add https://github.com/szkocot/andrej-karpathy-skills --skill karpathy-guidelines`

### Custom Formatters and LSPs

- [golangci-lint](https://github.com/golangci/golangci-lint)
  - `go install github.com/golangci/golangci-lint/v2/cmd/golangci-lint@latest`
- [golangci-lint-langserver](https://github.com/nametake/golangci-lint-langserver)
  - `go install github.com/nametake/golangci-lint-langserver@latest`
- [ruff](https://github.com/astral-sh/ruff)
  - `uv tool install ruff@latest`
- [ty](https://github.com/astral-sh/ty)
  - `uv tool install ty@latset`
- [pyrefly](https://github.com/facebook/pyrefly)
  - `uv tool install pyrefly@latest`
