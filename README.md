# workspace

A Mac CLI tool that launches and arranges apps on your screen from a JSON config file. Define your layout once, run it anytime.

```bash
workspace work
# 🚀  Launching workspace: work (2 apps)
# →  Running scripts...
# →    $ cd ~/Desktop
# →  [1/2] Slack → right-half (monitor 1)
# →  [2/2] Safari → left-half (monitor 1)  [https://en.wikipedia.org/wiki/Hippocrates]
# ✅  Done.
```

---

## Requirements

- macOS

No external dependencies. JSON parsing, window management, and display detection all use JXA (JavaScript for Automation) and AppleScript, which are built into every Mac.

---

## Installation

```bash
git clone https://github.com/mvdokh/workspace.git && cd workspace
sudo cp workspace /usr/local/bin/workspace && sudo chmod +x /usr/local/bin/workspace
mkdir -p ~/.workspaces && cp examples/*.json ~/.workspaces/
```

Then go to **System Settings → Privacy & Security → Accessibility** and add your terminal app. Restart your terminal once after granting access.

---

## Usage

```bash
workspace <n>                  # Launch a workspace by name
workspace list                    # List all saved workspaces
workspace edit <n>             # Create or edit a workspace config
workspace schedule <n> <time>  # Auto-launch daily at a given time
workspace unschedule <n>       # Remove a scheduled launch
workspace schedules               # List all scheduled workspaces
workspace shortcuts               # Print all registered shortcut keys
workspace help                    # Show help
```

### Examples

```bash
workspace work                    # Launch ~/.workspaces/work.json
workspace focus                   # Launch ~/.workspaces/focus.json
workspace schedule work 9:00      # Auto-launch 'work' every day at 9:00 AM
workspace unschedule work         # Remove that schedule
workspace schedules               # See all active schedules
```

---

## Config Files

Workspace configs live in `~/.workspaces/<n>.json`.

### Format

```json
{
  "name": "work",
  "shortcut": "ctrl+shift+1",
  "scripts": ["cd ~/Desktop"],
  "apps": [
    { "app": "Slack",   "zone": "right-half", "monitor": 1 },
    { "app": "Safari",  "zone": "left-half",  "monitor": 1, "url": "https://en.wikipedia.org/wiki/Hippocrates" }
  ]
}
```

| Field      | Description                                                            |
|------------|------------------------------------------------------------------------|
| `name`     | Display name for the workspace                                         |
| `shortcut` | Optional shortcut key (for reference — see Shortcuts section below)    |
| `scripts`  | Shell commands to run before launching apps                            |
| `app`      | Exact app name as shown in Activity Monitor                            |
| `zone`     | Where to place the window on screen                                    |
| `monitor`  | Which display to use: `1` = primary, `2` = secondary (default: `1`)   |
| `url`      | URL to open in the app (browsers only, optional)                       |

> **App names:** If an app doesn't position correctly, open Activity Monitor and use the exact process name shown there. For example, VS Code is `"Code"`, not `"Visual Studio Code"`.

---

## Zones

| Zone                    | Position                  |
|-------------------------|---------------------------|
| `full`                  | Full screen               |
| `left-half`             | Left 50%                  |
| `right-half`            | Right 50%                 |
| `top-half`              | Top 50%                   |
| `bottom-half`           | Bottom 50%                |
| `top-left-quarter`      | Top-left quadrant         |
| `top-right-quarter`     | Top-right quadrant        |
| `bottom-left-quarter`   | Bottom-left quadrant      |
| `bottom-right-quarter`  | Bottom-right quadrant     |
| `center-large`          | Centered, 80% of screen   |
| `center-small`          | Centered, 60% of screen   |

---

## Multi-Monitor Support

Set `"monitor": 1` or `"monitor": 2` per app to target a specific display. Monitor `1` is always your primary display (the one with the menu bar). Monitor `2` is the next connected display.

```json
{
  "name": "dual",
  "apps": [
    { "app": "Slack",   "zone": "full",      "monitor": 2 },
    { "app": "Safari",  "zone": "left-half", "monitor": 1 },
    { "app": "Code",    "zone": "right-half","monitor": 1 }
  ]
}
```

If `"monitor"` is omitted it defaults to `1`.

---

## Scripts

The `scripts` array runs shell commands before any apps are launched. Useful for navigating to a directory, starting a server, or any other setup step.

```json
{
  "scripts": [
    "cd ~/Desktop",
    "cd ~/Desktop && npm start"
  ]
}
```

Scripts run in order and in the context of your current shell session.

---

## Scheduling

Use `workspace schedule` to auto-launch a workspace daily at a set time via macOS launchd.

```bash
workspace schedule work 9:00      # Launch 'work' every day at 9:00 AM
workspace schedule focus 13:30    # Launch 'focus' every day at 1:30 PM
workspace schedules               # See all active schedules
workspace unschedule work         # Remove the 'work' schedule
```

Logs are written to `~/.workspaces/<name>.schedule.log` so you can debug if something doesn't launch as expected.

> The schedule will only fire if your Mac is awake at that time. If it's asleep, the launch will be skipped until the next day.

---

## Example Configs

### work — Slack + Safari side by side

```json
{
  "name": "work",
  "shortcut": "ctrl+shift+1",
  "scripts": ["cd ~/Desktop"],
  "apps": [
    { "app": "Slack",  "zone": "right-half", "monitor": 1 },
    { "app": "Safari", "zone": "left-half",  "monitor": 1, "url": "https://en.wikipedia.org/wiki/Hippocrates" }
  ]
}
```

### focus — code + terminal + browser

```json
{
  "name": "focus",
  "shortcut": "ctrl+shift+2",
  "scripts": ["cd ~/Desktop"],
  "apps": [
    { "app": "Code",     "zone": "left-half",            "monitor": 1 },
    { "app": "Terminal", "zone": "bottom-right-quarter", "monitor": 1 },
    { "app": "Safari",   "zone": "top-right-quarter",    "monitor": 1, "url": "https://en.wikipedia.org/wiki/Hippocrates" }
  ]
}
```

### dual — apps spread across two monitors

```json
{
  "name": "dual",
  "shortcut": "ctrl+shift+3",
  "scripts": ["cd ~/Desktop"],
  "apps": [
    { "app": "Code",     "zone": "full",      "monitor": 2 },
    { "app": "Safari",   "zone": "left-half", "monitor": 1, "url": "https://en.wikipedia.org/wiki/Hippocrates" },
    { "app": "Terminal", "zone": "right-half","monitor": 1 }
  ]
}
```

---

## Keyboard Shortcuts

The `shortcut` field in your JSON is for documentation. To actually trigger a workspace with a keypress, use one of these tools to run `workspace <n>`:

- **[Raycast](https://raycast.com)** — assign a hotkey to a Script Command
- **[BetterTouchTool](https://folivora.ai)** — bind a key combo to a shell script
- **macOS Shortcuts app** — create a shortcut that runs a shell command

---

## Creating a New Workspace

```bash
workspace edit mysetup
```

This creates `~/.workspaces/mysetup.json` from a template and opens it in your `$EDITOR`. Save, close, then run `workspace mysetup`.

---

## Troubleshooting

**`zsh: permission denied: workspace`**
Run `sudo chmod +x /usr/local/bin/workspace`.

**`osascript is not allowed assistive access`**
Grant Accessibility permission in System Settings → Privacy & Security → Accessibility, then restart your terminal.

**App doesn't move / "Can't get process" error**
The app name in your JSON doesn't match the process name. Check the exact name in Activity Monitor and update your config.

**App opens on the wrong monitor**
Make sure `"monitor": 2` is set and a second display is connected. Monitor indices are `1`-based; `1` is always the primary (menu bar) display.

**Scheduled workspace didn't launch**
Check the log at `~/.workspaces/<name>.schedule.log`. The most common cause is the Mac being asleep at the scheduled time.

