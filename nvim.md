# Neovim Configuration

## Prerequisites

- Neovim (0.9+)
- git
- A [Nerd Font](https://www.nerdfonts.com/) installed in your terminal (for icons)

### Install Neovim

| Platform | Command |
|----------|---------|
| macOS | `brew install neovim` |
| Debian/Ubuntu | `sudo apt install neovim` or download from [GitHub releases](https://github.com/neovim/neovim/releases) |
| Arch | `sudo pacman -S neovim` |
| Fedora | `sudo dnf install neovim` |

## Framework

Uses [LazyVim](https://www.lazyvim.org/) — a Neovim setup powered by [lazy.nvim](https://github.com/folke/lazy.nvim) with sensible defaults and a plugin-friendly structure.

### Bootstrap (`~/.config/nvim/lua/config/lazy.lua`)

LazyVim is bootstrapped with lazy.nvim. The setup auto-clones lazy.nvim on first run and loads all plugin specs from `~/.config/nvim/lua/plugins/`.

```lua
require("lazy").setup({
  spec = {
    { "LazyVim/LazyVim", import = "lazyvim.plugins" },
    { import = "plugins" },
  },
  defaults = {
    lazy = false,
    version = false,
  },
  install = { colorscheme = { "tokyonight", "habamax" } },
  checker = { enabled = true, notify = false },
})
```

## Directory Structure

```
~/.config/nvim/
  lua/
    config/
      lazy.lua       # lazy.nvim bootstrap and LazyVim setup
      options.lua    # Custom options (currently using LazyVim defaults)
      keymaps.lua    # Custom keymaps (currently using LazyVim defaults)
      autocmds.lua   # Custom autocmds (currently using LazyVim defaults)
    plugins/
      tmux-navigator.lua  # vim-tmux-navigator
      sops.lua            # SOPS encrypted file editing
```

## Custom Plugins

### vim-tmux-navigator

Seamless `Ctrl+h/j/k/l` navigation between Neovim splits and tmux panes. Requires the matching tmux plugin (see [tmux.md](tmux.md)).

`~/.config/nvim/lua/plugins/tmux-navigator.lua`:

```lua
return {
  "christoomey/vim-tmux-navigator",
  cmd = {
    "TmuxNavigateLeft",
    "TmuxNavigateDown",
    "TmuxNavigateUp",
    "TmuxNavigateRight",
    "TmuxNavigatePrevious",
  },
  keys = {
    { "<c-h>", "<cmd>TmuxNavigateLeft<cr>" },
    { "<c-j>", "<cmd>TmuxNavigateDown<cr>" },
    { "<c-k>", "<cmd>TmuxNavigateUp<cr>" },
    { "<c-l>", "<cmd>TmuxNavigateRight<cr>" },
    { "<c-\\>", "<cmd>TmuxNavigatePrevious<cr>" },
  },
}
```

| Binding | Action |
|---------|--------|
| `Ctrl+h` | Navigate left (across Neovim splits and tmux panes) |
| `Ctrl+j` | Navigate down |
| `Ctrl+k` | Navigate up |
| `Ctrl+l` | Navigate right |
| `Ctrl+\` | Navigate to previous pane/split |

### SOPS

Plugin for editing [SOPS](https://github.com/getsops/sops)-encrypted files inline.

`~/.config/nvim/lua/plugins/sops.lua`:

```lua
return {
  "lemarsu/sops.nvim",
  config = function()
    local config = require("sops.config")
    config.env = {
      XDG_CONFIG_HOME = vim.fn.expand("~/.config"),
    }
  end,
  keys = {
    { "<leader>se", "<cmd>Sops edit<cr>", desc = "Sops edit" },
    { "<leader>sc", "<cmd>Sops close<cr>", desc = "Sops close" },
    { "<leader>st", "<cmd>Sops toggle<cr>", desc = "Sops toggle" },
  },
}
```

| Binding | Action |
|---------|--------|
| `<leader>se` | Decrypt and edit SOPS file |
| `<leader>sc` | Re-encrypt and close |
| `<leader>st` | Toggle decrypted view |
