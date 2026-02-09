# System Setup

Configuration reference for LLM agents to set up a development environment to my preferences. Supports macOS and Linux.

## Environment

- **Terminal:** [Ghostty](https://ghostty.org)
- **Shell:** Fish
- **Multiplexer:** tmux with TPM (Tmux Plugin Manager)
- **Editor:** Neovim with LazyVim
- **Theme:** Catppuccin Mocha (across tmux and editor)

## Config File Locations

| Tool | Path |
|------|------|
| Ghostty | `~/.config/ghostty/config` |
| tmux | `~/.tmux.conf` |
| Neovim | `~/.config/nvim/` |
| TPM | `~/.tmux/plugins/tpm` |

## Platform Notes

| | macOS | Linux |
|---|---|---|
| Package manager | Homebrew (`brew`) | apt / dnf / pacman |
| Fish shell path | `/opt/homebrew/bin/fish` | `/usr/bin/fish` |
| Clipboard | pbcopy/pbpaste (native) | xclip / xsel / wl-copy |
| Ghostty install | `brew install ghostty` | Build from source or package manager |

## Setup Guides

- [tmux.md](tmux.md) — tmux configuration and plugins
- [nvim.md](nvim.md) — Neovim configuration and plugins

## Ghostty

```
command = /path/to/fish
shell-integration-features = ssh-terminfo
```

Set `command` to the Fish binary path for your platform.
