# tmux Configuration

## Prerequisites

- tmux (3.0+)
- git (for TPM clone)
- On Linux: `xclip` or `xsel` for tmux-yank clipboard support

## Install TPM

```sh
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

## Config (`~/.tmux.conf`)

### Prefix

Prefix is rebound to `Ctrl+Space`:

```tmux
unbind C-b
set -g prefix C-Space
bind C-Space send-prefix
```

### Core Settings

```tmux
set -g default-terminal "tmux-256color"
set -ag terminal-overrides ",xterm-256color:RGB"
set -g mouse on
set -g base-index 1
setw -g pane-base-index 1
set -g renumber-windows on
set -g escape-time 0
set -g history-limit 50000
set -g focus-events on
set -g status-position top
set -g allow-passthrough on
```

### Key Bindings

| Binding | Action |
|---------|--------|
| `prefix + \|` | Split pane horizontally (in current path) |
| `prefix + -` | Split pane vertically (in current path) |
| `prefix + c` | New window (in current path) |
| `prefix + z` | Zoom pane (default) |
| `prefix + Ctrl+f` | Zoom pane (additional binding) |
| `prefix + r` | Reload config |
| `Ctrl+h/j/k/l` | Navigate panes/Neovim splits (vim-tmux-navigator, no prefix) |
| `F12` | Toggle outer tmux off/on for nested sessions |

### Vi Copy Mode

```tmux
setw -g mode-keys vi
bind-key -T copy-mode-vi v send-keys -X begin-selection
bind-key -T copy-mode-vi y send-keys -X copy-selection-and-cancel
```

`tmux-yank` handles copying to the system clipboard automatically. On Linux, install `xclip` or `xsel`.

### Nested tmux Support

`F12` toggles the outer tmux session off so keybindings pass through to an inner (e.g., remote) tmux session. The status bar shows `(OFF)` in red when disabled.

## Plugins

Managed by [TPM](https://github.com/tmux-plugins/tpm). After writing the config, run `prefix + I` to install.

| Plugin | Purpose |
|--------|---------|
| `tmux-plugins/tmux-sensible` | Sensible defaults (display-time, status-interval, aggressive-resize) |
| `tmux-plugins/tmux-yank` | System clipboard integration for copy mode |
| `tmux-plugins/tmux-resurrect` | Save/restore sessions across restarts |
| `tmux-plugins/tmux-continuum` | Auto-save resurrect sessions, auto-restore on start |
| `christoomey/vim-tmux-navigator` | Seamless Ctrl+h/j/k/l between tmux panes and Neovim splits |
| `catppuccin/tmux` | Catppuccin Mocha theme |

### Plugin Config

```tmux
set -g @catppuccin_flavor 'mocha'
set -g @continuum-restore 'on'
```

## Full Config

See [`~/.tmux.conf`](https://github.com/caseyjbenko/system-setup/blob/main/tmux.conf) or copy the complete config below:

```tmux
# Ctrl+Space as prefix
unbind C-b
set -g prefix C-Space
bind C-Space send-prefix

# Modern settings
set -g default-terminal "tmux-256color"
set -ag terminal-overrides ",xterm-256color:RGB"
set -g mouse on
set -g base-index 1
setw -g pane-base-index 1
set -g renumber-windows on
set -g escape-time 0
set -g history-limit 50000
set -g focus-events on

# Vi-style copy mode
setw -g mode-keys vi
bind-key -T copy-mode-vi v send-keys -X begin-selection
bind-key -T copy-mode-vi y send-keys -X copy-selection-and-cancel

# Split panes with | and -
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"
unbind '"'
unbind %

# New window in current path
bind c new-window -c "#{pane_current_path}"

# Zoom pane (also Ctrl+f)
bind C-f resize-pane -Z

# Reload config
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# Status bar position
set -g status-position top

set -g allow-passthrough on

# ----------------------------
# Nested tmux support (F12 to toggle)
# ----------------------------

bind -T root F12 \
  set prefix None \;\
  set key-table off \;\
  set status-left "#[bg=#f38ba8,fg=#1e1e2e,bold] #S (OFF) #[bg=#1e1e2e] " \;\
  refresh-client -S

bind -T off F12 \
  set -u prefix \;\
  set -u key-table \;\
  set status-left "#[bg=#89b4fa,fg=#1e1e2e,bold] #S #[bg=#1e1e2e] " \;\
  refresh-client -S

# ----------------------------
# Plugins (TPM)
# ----------------------------
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-yank'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @plugin 'christoomey/vim-tmux-navigator'
set -g @plugin 'catppuccin/tmux'

# Catppuccin config
set -g @catppuccin_flavor 'mocha'

# Continuum - auto-restore
set -g @continuum-restore 'on'

# Initialize TPM (keep at very bottom)
run '~/.tmux/plugins/tpm/tpm'
```
