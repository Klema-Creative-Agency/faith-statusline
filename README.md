# faith-statusline

A faith-forward custom status line and management skill for [Claude Code](https://claude.com/claude-code).

It replaces the bottom status bar with a rotating message that cycles through faith-aligned encouragements, biblical-tech crossovers, short scripture, and clean dev humor. Jesus-centered lines are shown in red with a cross, followed by the model name, context percentage, and session cost.

```
✝ The joy of the Lord is your strength — Nehemiah 8:10 | Opus 4.8 | 7% context | $0.21
```

Jesus at the center, even in the terminal. For those with eyes to see.

## What is in this repo

| File | Purpose |
| --- | --- |
| `statusline.sh` | The status line script. Reads Claude Code status JSON on stdin and prints the bar. |
| `SKILL.md` | A Claude Code skill that installs and manages the status line for you. |

The script ships with about 200 phrases across five categories: Pleading the Blood, Jesus at the Center, Biblical-Tech Crossovers, Short Encouraging Verses, and Clean Tech Humor.

## Requirements

- [Claude Code](https://claude.com/claude-code)
- `jq` (`brew install jq` on macOS, or your package manager of choice)
- bash

## Install

### Option A: the easy way (install the skill, let Claude do it)

Clone into your user-level skills folder, then ask Claude Code to install it.

```bash
git clone https://github.com/Klema-Creative-Agency/faith-statusline.git ~/.claude/skills/faith-statusline
```

Then, in any Claude Code session:

```
/faith-statusline install
```

Claude will copy the script to `~/.claude/statusline.sh`, wire up your `~/.claude/settings.json`, and verify it runs.

### Option B: the manual way (status line only)

```bash
# 1. Drop the script in place
curl -fsSL https://raw.githubusercontent.com/Klema-Creative-Agency/faith-statusline/main/statusline.sh -o ~/.claude/statusline.sh
chmod +x ~/.claude/statusline.sh
```

Then add this to `~/.claude/settings.json` (merge it with your existing settings, do not overwrite the file):

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh"
  }
}
```

Restart Claude Code and the status line is live.

## Managing it with the skill

Once the skill is in `~/.claude/skills/faith-statusline/`, you can talk to it in plain language or with arguments:

| Command | What it does |
| --- | --- |
| `/faith-statusline install` | Set up the script and wire `settings.json` on this machine |
| `/faith-statusline uninstall` | Remove the status line config (leaves the script in place) |
| `/faith-statusline list` | Show all phrases grouped by category |
| `/faith-statusline add "Anointed and deployed"` | Add a phrase (mark it red for Jesus-centered lines) |
| `/faith-statusline new 10 humor` | Generate 10 fresh phrases in the same voice |
| `/faith-statusline speed 4` | Rotate every 4 seconds instead of 6 |
| `/faith-statusline dedupe` | Remove duplicate phrases |
| `/faith-statusline color off` | Turn off the red coloring (reversible with `color on`) |

## Customizing the phrases by hand

All phrases live in the `PHRASES=( ... )` array in `statusline.sh`. Prefix a line with `RED|` to render it red with a cross. The rotation interval is the divisor in this line:

```bash
INDEX=$(( $(date +%s) / 6 % ${#PHRASES[@]} ))
```

Change `6` to rotate faster or slower.

## License

MIT. Use it, fork it, make it your own. Built by [Klema Creative](https://klemacreative.com).
