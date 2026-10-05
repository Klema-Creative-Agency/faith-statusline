---
name: faith-statusline
description: Install and manage the faith-tech-humor Claude Code status line — the rotating "Pleading the blood / Jesus-centered / dev humor" message bar with model, context %, and cost. Use when the user says "statusline", "status line", "install my status line", "add a status line phrase", "change rotation speed", "set up my terminal messages on this machine", or wants to manage the rotating terminal phrases.
---

# Faith Status Line

A portable installer and manager for the user's custom Claude Code status line: a rotating bar of faith-aligned, biblical-tech, and clean dev-humor phrases, with Jesus-centered lines shown in red with a ✝, followed by model name, context %, and session cost.

The canonical script lives beside this file at `statusline.sh`. **That bundled copy is the source of truth** — install copies it out, and phrase/config edits are made to BOTH the bundled copy and any installed copy so the skill stays portable across machines (the MacBook Pro `Klema-Creative-MB` and the Mac Studio).

House style: no em dashes anywhere. Faith-aligned, professional tone.

## Routing by argument

Read the user's argument after the skill name and pick the matching action. If no argument, show a short status: whether it's installed, the phrase count, the rotation interval, and the available commands.

### `install`
Set the status line up on the current machine.
1. Copy the bundled `statusline.sh` (next to this SKILL.md) to `~/.claude/statusline.sh` and `chmod +x` it.
2. Add or update the `statusLine` block in `~/.claude/settings.json` (create the file as `{}` if missing) to:
   ```json
   "statusLine": { "type": "command", "command": "~/.claude/statusline.sh" }
   ```
   Use a JSON-safe edit — read the file, parse, set the key, write it back. Do not clobber other keys (enabledPlugins, permissions, etc.).
3. Verify the script runs: `echo '{}' | ~/.claude/statusline.sh` and confirm it prints a phrase line.
4. Tell the user it's live and that the bar refreshes as Claude Code redraws.

### `uninstall`
Remove the `statusLine` key from `~/.claude/settings.json` (leave the script file in place unless the user asks to delete it).

### `list`
Read `~/.claude/statusline.sh`, show the phrases grouped by their section comments (Pleading the Blood, Jesus at the Center, Biblical-Tech Crossovers, Short Encouraging Verses, Clean Tech Humor), with a total count.

### `add "<phrase>"` (optionally a category + RED flag)
Append a phrase to the `PHRASES=( ... )` array in the script.
- If the user wants it red (blood/Jesus-centered), prepend `RED|` inside the quotes, exactly like the existing red lines.
- Place it under the right section comment. Keep alphabetical-ish grouping loose; section correctness matters more than order.
- Escape any characters that break bash double-quoted strings (notably `"`, backtick, `$`, `\`).
- Apply the edit to BOTH the installed `~/.claude/statusline.sh` and the bundled `statusline.sh` in this skill folder.

### `new <N> [category]`
Generate N fresh phrases in the user's voice (faith-aligned, no em dashes, clean humor only), matching the tone of the requested category, then add them via the `add` flow. Show the user the list and confirm before writing if N is large.

### `speed <seconds>`
Change the rotation interval. Edit the line `INDEX=$(( $(date +%s) / 6 % ${#PHRASES[@]} ))` — replace the `/ 6` divisor with the new seconds value. Apply to both copies.

### `dedupe`
Find and remove duplicate phrases in the array (case-insensitive, ignoring the `RED|` prefix). Report what was removed. Apply to both copies.

### `color <on|off>`
`RED|`-prefixed lines print red; every other phrase cycles through the `PALETTE` array (cyan, yellow, green, magenta, blue) by phrase index. `off`: make every phrase print uncolored (set `COLOR=""` for both branches). `on`: restore red for `RED|` lines and the palette for the rest. Prefer toggling behavior over deleting the RED prefixes so it's reversible.

## Notes
- The script reads Claude Code's status JSON on stdin and pulls `.model.display_name`, `.context_window.used_percentage`, and `.cost.total_cost_usd` via `jq`. `jq` must be installed (`brew install jq`).
- After any edit, re-run `echo '{}' | ~/.claude/statusline.sh` to confirm it still executes cleanly before telling the user it's done.
- Keep the leading `✝` glyph and the ` | model | context | cost` suffix intact unless the user asks to change the format.
