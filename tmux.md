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

Prefix is rebound to `Ctrl+Space` (primary) with `Ctrl+a` as secondary:

```tmux
unbind C-b
set -g prefix C-Space
set -g prefix2 C-a
bind C-Space send-prefix
bind C-a send-prefix -2
```

### Core Settings

```tmux
set -g default-terminal "tmux-256color"
set -ag terminal-overrides ",xterm-256color:RGB"
set-option -g default-shell /bin/bash
set -g mouse on
set -g base-index 1
set -g pane-base-index 1
set -g renumber-windows on
set -g escape-time 0
set -g history-limit 50000
set -g focus-events on
set -g status-position top
set -g allow-passthrough on
set -s set-clipboard on
```

### Clipboard in Nested Sessions

For OSC 52 clipboard to work through nested tmux (e.g., local tmux -> SSH -> remote tmux), all three layers must cooperate:

1. **Ghostty:** `clipboard-read = allow` and `clipboard-write = allow`
2. **Outer tmux:** `allow-passthrough on` and `set-clipboard on`
3. **Inner tmux:** `allow-passthrough on` and `set-clipboard on`

On Linux, also install `xclip` or `xsel`. The `@override_copy_command` is set to `true` (no-op) so that OSC 52 via `set-clipboard` handles all clipboard operations.

### Key Bindings

| Binding | Action |
|---------|--------|
| `prefix + \|` | Split pane horizontally (in current path) |
| `prefix + -` | Split pane vertically (in current path) |
| `prefix + c` | New window (in current path) |
| `prefix + Ctrl+f` | Zoom pane |
| `prefix + r` | Reload config |
| `prefix + h/j/k/l` | Select pane (vim-like fallback) |
| `prefix + H/J/K/L` | Resize pane by 5 |
| `prefix + Ctrl+h/Ctrl+l` | Previous/next window |
| `Ctrl+h/j/k/l` | Navigate panes/Neovim splits (vim-tmux-navigator, no prefix) |
| `F12` | Toggle outer tmux off/on for nested sessions |

### Vi Copy Mode

```tmux
setw -g mode-keys vi
bind -T copy-mode-vi v send -X begin-selection
bind -T copy-mode-vi y send -X copy-selection-and-cancel
bind -T copy-mode-vi Escape send -X cancel
```

`tmux-yank` handles copying to the system clipboard via OSC 52. On Linux, install `xclip` or `xsel`.

### Nested tmux Support

`F12` toggles the outer tmux session off so keybindings pass through to an inner (e.g., remote) tmux session. The status bar shows `(OFF)` in red (`#f38ba8`, Catppuccin red) when disabled. Toggling back on re-sources the config to restore the full status bar including the SSH indicator.

### SSH Indicator

When connected via SSH, an orange `SSH` badge (Catppuccin peach `#fab387`) appears in the status-left after the session name. This is set as a post-plugin override (after TPM init) using a `#()` shell command that checks `$SSH_CONNECTION`.

## Plugins

Managed by [TPM](https://github.com/tmux-plugins/tpm). After writing the config, run `prefix + I` to install.

| Plugin | Purpose |
|--------|---------|
| `tmux-plugins/tmux-sensible` | Sensible defaults (display-time, status-interval, aggressive-resize) |
| `tmux-plugins/tmux-yank` | System clipboard integration for copy mode |
| `tmux-plugins/tmux-resurrect` | Save/restore sessions across restarts (with nvim session strategy) |
| `tmux-plugins/tmux-continuum` | Auto-save resurrect sessions, auto-restore on start |
| `tmux-plugins/tmux-open` | Open highlighted file/URL from copy mode |
| `christoomey/vim-tmux-navigator` | Seamless Ctrl+h/j/k/l between tmux panes and Neovim splits |
| `catppuccin/tmux` | Catppuccin Mocha theme |

### Plugin Config

```tmux
set -g @catppuccin_flavor 'mocha'
set -g @continuum-restore 'on'
set -g @resurrect-strategy-nvim 'session'
set -g @yank_selection_mouse 'clipboard'
set -g @override_copy_command 'true'
```

## Post-Plugin Overrides

These must come **after** `run '~/.tmux/plugins/tpm/tpm'` so that Catppuccin's defaults are set first and our overrides take effect. Using `set -g` (not `set -ga`) ensures reloads don't duplicate entries.

```tmux
set -g status-left-length 60
set -g status-right-length 100
set -g status-left '#{E:@catppuccin_status_session}#([ -n "$SSH_CONNECTION" ] && echo "#[bg=#fab387,fg=#1e1e2e,bold] SSH #[default] ")'
set -g status-right "#{E:@catppuccin_status_host}"
```

## Full Config

```tmux
# Tmux Configuration with Catppuccin Theme

# Set true color support
set -g default-terminal "tmux-256color"
set -ag terminal-overrides ",xterm-256color:RGB"

# Set default shell
set-option -g default-shell /bin/bash

# Enable mouse support
set -g mouse on

# Performance and history
set -s escape-time 0
set -g history-limit 50000

# Status bar position
set -g status-position top

# Allow OSC 52 to set the clipboard (needed for nested tmux over SSH)
set -g allow-passthrough on
set -s set-clipboard on

# Enable focus events for vim-tmux integration
set -g focus-events on

# Set vi mode for copy mode
setw -g mode-keys vi

# Set prefix to Ctrl-Space (primary) and Ctrl-a (secondary)
unbind C-b
set -g prefix C-Space
set -g prefix2 C-a
bind C-Space send-prefix
bind C-a send-prefix -2

# Start windows and panes at 1, not 0
set -g base-index 1
set -g pane-base-index 1
set-window-option -g pane-base-index 1
set-option -g renumber-windows on

# Reload config
bind r source-file ~/.tmux.conf \; display "Config reloaded!"

# vim-tmux-navigator plugin handles the smart pane switching

# Pane navigation (vim-like) - fallback bindings
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Window navigation
bind -r C-h select-window -t :-
bind -r C-l select-window -t :+

# Pane resizing
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# Split panes (keep current path)
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"
unbind '"'
unbind %

# New window in current path
bind c new-window -c "#{pane_current_path}"

# Zoom pane (also Ctrl+f)
bind C-f resize-pane -Z

# Copy mode bindings (vi-like)
bind -T copy-mode-vi v send -X begin-selection
bind -T copy-mode-vi y send -X copy-selection-and-cancel
bind -T copy-mode-vi Escape send -X cancel

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
  source-file ~/.tmux.conf \;\
  refresh-client -S

# Catppuccin theme configuration
set -g @catppuccin_flavor 'mocha'

# Plugin manager
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @plugin 'tmux-plugins/tmux-yank'
set -g @plugin 'tmux-plugins/tmux-open'
set -g @plugin 'christoomey/vim-tmux-navigator'
set -g @plugin 'catppuccin/tmux'

# Plugin configurations
set -g @continuum-restore 'on'
set -g @resurrect-strategy-nvim 'session'
set -g @yank_selection_mouse 'clipboard'
# Use true (no-op) as copy command — OSC 52 via set-clipboard handles it
set -g @override_copy_command 'true'

# Initialize TMUX plugin manager
run '~/.tmux/plugins/tpm/tpm'

# Post-plugin overrides (must be after TPM init so catppuccin defaults are set first)
set -g status-left-length 60
set -g status-right-length 100
set -g status-left '#{E:@catppuccin_status_session}#([ -n "$SSH_CONNECTION" ] && echo "#[bg=#fab387,fg=#1e1e2e,bold] SSH #[default] ")'
set -g status-right "#{E:@catppuccin_status_host}"
```
