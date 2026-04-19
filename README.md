# workspace

A Mac CLI tool that launches and arranges apps on your screen from a JSON config file. Define your layout once, run it anytime.

```bash
workspace work
# 🚀  Launching workspace: work (2 apps)
# →  [1/2] Slack → right-half
# →  [2/2] Safari → left-half
# ✅  Done.
```

---

## Requirements

- macOS
- [Homebrew](https://brew.sh) — for installing jq
- [jq](https://stedolan.github.io/jq/)

---

## Installation

**1. Clone the repo**

```bash
git clone https://github.com/yourusername/workspace.git
cd workspace
```

**2. Install jq**

```bash
brew install jq
```

**3. Move the script onto your PATH and make it executable**

```bash
sudo cp workspace /usr/local/bin/workspace
sudo chmod +x /usr/local/bin/workspace
```

**4. Grant Accessibility permission**

The tool uses AppleScript to move and resize windows, which requires Accessibility access.

Go to **System Settings → Privacy & Security → Accessibility** and add your terminal app (Terminal, iTerm2, etc.).

> After granting permission, restart your terminal for it to take effect.

**5. Set up your workspaces folder**

```bash
mkdir -p ~/.workspaces
cp examples/*.json ~/.workspaces/
```

The repo includes `work.json` and `focus.json` as starting points — edit them to match your apps, or create your own.

---

## Usage

```bash
workspace <name>          # Launch a workspace by name
workspace list            # List all saved workspaces
workspace edit <name>     # Create or edit a workspace config
workspace shortcuts       # Print all registered shortcuts
workspace help            # Show help
```

### Example

```bash
workspace work            # Launches ~/.workspaces/work.json
workspace focus           # Launches ~/.workspaces/focus.json
```

---

## Config Files

Workspace configs live in `~/.workspaces/<name>.json`.

### Format

```json
{
  "name": "work",
  "shortcut": "ctrl+shift+1",
  "apps": [
    { "app": "Slack",   "zone": "right-half" },
    { "app": "Safari",  "zone": "left-half"  }
  ]
}
```

| Field      | Description                                              |
|------------|----------------------------------------------------------|
| `name`     | Display name for the workspace                           |
| `shortcut` | Optional shortcut key (for reference — see note below)   |
| `app`      | Exact app name as it appears in Activity Monitor         |
| `zone`     | Where to place the window on screen                      |

> **App names:** If an app doesn't position correctly, open Activity Monitor and use the exact process name shown there. For example, VS Code is `"Code"`, not `"Visual Studio Code"`.

---

## Zones

| Zone                    | Position              |
|-------------------------|-----------------------|
| `full`                  | Full screen           |
| `left-half`             | Left 50%              |
| `right-half`            | Right 50%             |
| `top-half`              | Top 50%               |
| `bottom-half`           | Bottom 50%            |
| `top-left-quarter`      | Top-left quadrant     |
| `top-right-quarter`     | Top-right quadrant    |
| `bottom-left-quarter`   | Bottom-left quadrant  |
| `bottom-right-quarter`  | Bottom-right quadrant |
| `center-large`          | Centered, 80% of screen |
| `center-small`          | Centered, 60% of screen |

---

## Example Configs

### Work layout — Slack + Safari side by side

```json
{
  "name": "work",
  "shortcut": "ctrl+shift+1",
  "apps": [
    { "app": "Slack",  "zone": "right-half" },
    { "app": "Safari", "zone": "left-half"  }
  ]
}
```

### Focus layout — code + terminal + browser

```json
{
  "name": "focus",
  "shortcut": "ctrl+shift+2",
  "apps": [
    { "app": "Code",     "zone": "left-half"             },
    { "app": "Terminal", "zone": "bottom-right-quarter"  },
    { "app": "Safari",   "zone": "top-right-quarter"     }
  ]
}
```

---

## Keyboard Shortcuts

The `shortcut` field in your JSON is for documentation. To actually trigger a workspace with a keypress, use one of these tools to run `workspace <name>`:

- **[Raycast](https://raycast.com)** — assign a hotkey to a Script Command
- **[BetterTouchTool](https://folivora.ai)** — bind a key combo to a shell script
- **macOS Shortcuts app** — create a shortcut that runs a shell command

---

## Creating a New Workspace

Use the built-in editor to scaffold a new config:

```bash
workspace edit mysetup
```

This creates `~/.workspaces/mysetup.json` with a template and opens it in your `$EDITOR`. Save and close, then run `workspace mysetup`.

---

## Troubleshooting

**`zsh: permission denied: workspace`**
Run `sudo chmod +x /usr/local/bin/workspace`.

**`osascript is not allowed assistive access`**
Grant Accessibility permission in System Settings → Privacy & Security → Accessibility, then restart your terminal.

**App doesn't move / "Can't get process" error**
The app name in your JSON doesn't match the process name. Check the exact name in Activity Monitor and update your config.

**`jq: command not found`**
Install jq: `brew install jq`
