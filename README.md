# Dotfiles

My configuration files, managed with [chezmoi](https://www.chezmoi.io/). The repo is a
chezmoi **source directory**: files are named for their target paths relative to `$HOME`
(`dot_config/nvim` → `~/.config/nvim`, `dot_gitconfig` → `~/.gitconfig`, ...). One source
tree serves both Windows and Linux.

## Usage

- First time on a machine: `chezmoi init --apply brian-guerrero/dotfiles`
- Pull + apply updates: `chezmoi update`
- Preview pending changes: `chezmoi diff`
- Edit a config: `chezmoi edit ~/.config/nvim/init.lua` then `chezmoi apply`
- Pick up an out-of-band change: `chezmoi re-add`

## What gets deployed

| Config | Target (Linux) | Target (Windows) |
|---|---|---|
| Neovim | `~/.config/nvim` | `~/.config/nvim` (+ `%LOCALAPPDATA%\nvim` junction) |
| Oh My Posh theme | `~/.config/oh-my-posh/brianguerrero.omp.json` | same |
| PowerShell profile | `~/.config/powershell/Microsoft.PowerShell_profile.ps1` | `…/Documents/PowerShell/Microsoft.PowerShell_profile.ps1` |
| herdr | `~/.config/herdr/config.toml` | `%APPDATA%\herdr\config.toml` |
| Ghostty | `~/.config/ghostty/config` | n/a (no Windows build) |
| tmux | `~/.tmux.conf` | n/a |
| Git | `~/.gitconfig` | `~/.gitconfig` |

## Platform notes

- **Neovim on Windows** reads `%LOCALAPPDATA%\nvim`; `run_onchange_after_windows-nvim-link.ps1.tmpl`
  makes that a directory junction to `~/.config/nvim`, so the config lives in one place.
  Junctions need no admin rights or Developer Mode.
- **PowerShell profile on Windows**: `$PROFILE` is fixed under Documents (and this account's
  Documents is OneDrive-redirected), so `run_onchange_after_windows-powershell-profile.ps1.tmpl`
  writes the profile to the real path resolved via `[Environment]::GetFolderPath('MyDocuments')`.
  On Linux the profile is deployed normally to `~/.config/powershell/`.
- **herdr on Windows** reads `%APPDATA%\herdr\config.toml` (that dir also holds herdr's
  own runtime state — logs, sockets, `session.json` — so it can't just be a junction to
  the repo). `run_onchange_after_windows-herdr-config.ps1.tmpl` writes the managed config
  there whenever its contents change; `.config/herdr/**` is ignored on Windows so the
  config isn't also dropped in `~/.config`. This is one-way (source → `%APPDATA%`); pick up
  a hand-edit with `chezmoi re-add ~/.config/herdr/config.toml` after editing the source.
  On Linux the config is deployed normally to `~/.config/herdr/`.
- **Ghostty / tmux** are ignored on Windows via `.chezmoiignore`.
