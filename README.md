# my-opencode-settings
My own OpenCode Settings

## What's Included

- Anthropic API based Z.AI coding plan provider
- disable rust-analyzer to save resources
- `context-mode`
- `tree-sitter-analyzer`
- `rtk`
- `simple-agent-memory` skill
- `conventional-commit` skill

## Instructions

### Pre-requisites

- Install [grd](https://github.com/lucidfrontier45/grd)

### Base Settings

`opencode.jsonc` already contains following settings. Copy it to `~/.config/opencode/opencode.jsonc` or your project root.

- Anthropic API based Z.AI coding plan provider
- context-mode
- tree-sitter-analyzer
- disable rust-analyzer to save resources

### tree-sitter-analyzer dependencies

Install `fd` and `ripgrep` with `grd` or `cargo-binstall`
- `grd -d .local/bin/ sharkdp/fd`
- `grd -d .local/bin/ BurntSushi/ripgrep`

### RTK

1. Install binary : `grd -d .local/bin/ rtk-ai/rtk` 
2. Initialize: `rtk init -g opencode`

### Skills

1. copy `skills/*` folder to your project or `~/.agents/skills`
2. `bunx skills add https://github.com/marcelorodrigo/agent-skills --skill conventional-commit`