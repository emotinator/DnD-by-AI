# Claude as DM — campaign repo

This repo runs D&D campaigns with Claude as Dungeon Master or co-DM. It holds two things, kept separate:

- **The DM core** (`dm-core/`): how Claude runs *any* campaign. Dice, turn format, encounter philosophy, sheets, continuity, handoffs. Same for every campaign.
- **Campaigns** (`campaigns/<name>/`): one folder per campaign. Its settings, world, party and history.

Everything is plain Markdown, so any co-DM can rebuild the same DM on their own Claude account or machine.

## Layout

```
README.md                  # this file
dm-core/
  bootstrap.md             # paste into the Claude project's instructions (set campaign + repo path)
  instructions.md          # DM core: Claude reads it from the repo every chat
  modules/
    drama-deck.md          # initiative module (used when campaign.md says drama-deck)
campaigns/
  _template/               # blank campaign: copy this to start a new one
  lapaliiya/               # the campaign in progress
    campaign.md            # SETTINGS: ruleset, leveling, initiative, lethality, tone, lines/veils, table, tools
    bible.md               # world, factions, NPCs, threads, DM-only secrets
    roster.md              # party: classes, levels, D&D Beyond IDs, house items, hooks
    house-rules.md         # table conventions, homebrew, rulings that stuck
    log-recent.md          # rolling summary of the current arc
    sessions/              # one file per session; never edited after confirmation
    archive/               # arc summaries, old prep, player chronicles, maps
```

**Working set vs. archive.** Each campaign's five top-level files (`campaign.md`, `bible.md`, `roster.md`, `house-rules.md`, `log-recent.md`) are the *working set*: small, read every session. `sessions/` and `archive/` are read only when an old detail is needed.

**Claude reads everything straight from your clone of this repo.** The Claude project holds only a short bootstrap (`dm-core/bootstrap.md`) that says which campaign to load and where the repo is. Nothing is uploaded to the project, so there's nothing to keep in sync there, but **your clone must be up to date** (see *Handing off between co-DMs*).

> **Spoiler warning:** `bible.md` and `archive/prep/` contain DM-only secrets. Players shouldn't browse this repo.

## What you need

- A Claude account with **Projects** (claude.ai or the Claude desktop app).
- Git access to this repo, or a downloaded copy.
- **The Claude desktop app** on the computer that has your clone of the repo. Claude reads the campaign from there. On the web or phone it can't, and will ask you to paste files in.
- **Node.js** (current LTS), for the MCP servers below.
- **The D&D Beyond login** for the shared scribe account, the one that can see the campaign's character sheets.

The next section walks through installing all of it.

## Set up Claude on a new machine

Do this once per computer. It gives Claude two local tools: a **filesystem** server to read and write the repo, and a **D&D Beyond** server to read character sheets.

### 1. Install the basics
1. Install the **Claude desktop app** and sign in.
2. Install **Node.js** (current LTS) from nodejs.org. Check with `node -v` and `npx -v`.
3. **Clone this repo** somewhere permanent, e.g. `~/Documents/Development/DnD by AI`. Note the full path; you'll need it twice.

### 2. Log in to D&D Beyond
The server is [dndbeyond-mcp](https://github.com/AlexWorland/dndbeyond-mcp) (unofficial, MIT licensed). In a terminal:

```
npx dndbeyond-mcp setup
```

A browser window opens. Log in to D&D Beyond with the **shared scribe account**. The server saves the session cookie to `~/.dndbeyond-mcp/config.json`.

> **That file is a password.** It gives full access to the D&D Beyond account. Never commit it, paste it into a chat, or share it.

### 3. Register both servers with Claude
1. In the Claude desktop app, open **Settings → Developer → Edit Config**. This opens `claude_desktop_config.json`:
   - macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Windows: `%APPDATA%\Claude\claude_desktop_config.json`
2. Add both servers, using the path to **your** clone. If the file already has an `mcpServers` block, add these two entries to it rather than replacing it.

   ```json
   {
     "mcpServers": {
       "dndbeyond": {
         "command": "npx",
         "args": ["-y", "dndbeyond-mcp"]
       },
       "filesystem": {
         "command": "npx",
         "args": [
           "-y",
           "@modelcontextprotocol/server-filesystem",
           "/Users/<you>/Documents/Development/DnD by AI"
         ]
       }
     }
   }
   ```
   On Windows, write the path with double backslashes, e.g. `"C:\\Users\\<you>\\Documents\\DnD by AI"`.
3. **Quit Claude completely** (not just close the window) and reopen it. The servers start when the app starts.

The filesystem server only sees the folder you list. That's deliberate: Claude can read and write the campaign, and nothing else on your computer.

### 4. Create the Claude project
See *Continue the campaign in progress* below: create a project and paste in the bootstrap with your repo path.

### 5. Test it (2 minutes)
In a new chat in the project, send:

```
OOC: setup check. 1) Check D&D Beyond auth. 2) Read Nelgron's sheet as a summary. 3) List the files in campaigns/ in the repo. Don't start play.
```

You should get: "Authenticated", Nelgron's level, HP and AC, and the `_template` and `lapaliiya` folders.

### Troubleshooting
| Symptom | Fix |
|---|---|
| Claude has no D&D Beyond or filesystem tools | Check the config file is valid JSON (a missing comma breaks everything), then fully quit and reopen Claude. |
| Filesystem says access denied | The repo path in the config doesn't match where you cloned it. Fix the path and restart. |
| D&D Beyond auth fails or expires | Run `npx dndbeyond-mcp setup` again. |
| One character returns "403 Forbidden" | That sheet is private, or the scribe account isn't in the campaign. Ask the player to share it. |
| Character not found by name | Use the D&D Beyond ID from `roster.md` (the number at the end of the sheet URL). |
| D&D Beyond tools suddenly break for everyone | The server uses unofficial endpoints that can change. Check the server's GitHub page for an update; meanwhile, play from `roster.md` and player reports. |

### Safety notes
- The D&D Beyond server also has **write** tools (update HP, spell slots, currency, use abilities). The DM instructions forbid Claude from using them; players manage their own sheets. If Claude asks to run one, say no.
- **Optional, not set up yet:** a rules MCP server (e.g. Open5e) for exact spell text and stat blocks. Without it, Claude rules from its own knowledge and flags each ruling.

## Continue the campaign in progress

1. **Clone the repo** (first time) or **`git pull`** (every time after). Someone may have played since you last synced.
2. **Create a Claude project,** one per campaign, e.g. "DM — Lapaliiya". Leave its knowledge empty.
3. **Paste the bootstrap.** Copy the block from `dm-core/bootstrap.md` into the project's instructions, and set `Campaign: lapaliiya` and `Repo:` to the path of *your* clone.
4. **Use the desktop app** on the machine you set up above, so Claude can reach the repo and D&D Beyond.
5. **Start a chat** with:
   ```
   Handoff: <your name> is session lead
   ```
   Claude loads the files from your clone, tells you the date of the last session it sees, and asks you to confirm that's the latest. Then it briefs you: where the party is, active threats, open threads, any secrets about to matter.

**Quick check before your first live session:** ask Claude `OOC: which files did you load, and where is the party now?` If it can't reach the repo, check the folder link and the `Repo:` path in the bootstrap.

## Start a new campaign

1. **Copy the template:** `cp -r campaigns/_template campaigns/<new-name>`.
2. **Create a new Claude project** for it and paste the bootstrap from `dm-core/bootstrap.md`, with `Campaign:` set to the new folder name.
3. **Run session zero:** start a chat with
   ```
   OOC: run session zero for a new campaign
   ```
   Claude walks you through `campaign.md` (premise, ruleset, leveling, initiative, lethality, tone, lines and veils, table, tools), takes the party list, and drafts the opening `bible.md`, `roster.md` and `log-recent.md`.
4. Claude writes the drafts into `campaigns/<new-name>/`. Review them, then commit and push.

## During and after a session

- **Input:** the session lead types for the table, e.g. `Hurintha: attacks, 17 to hit, 14 damage.` Lines starting `DM:` or `OOC:` are instructions to Claude. Discord posts go in with `DISCORD:`.
- **`end session`:** Claude writes the session file and the changed working-set files straight into `campaigns/<name>/`, lists what changed, and suggests hooks for next time. (If it can't reach the repo, it gives you the full file text to save yourself.)
- **Review, commit, push:** `git diff` to check, then `git commit -am "<campaign>: Session YYYY-MM-DD: <short title>"` and `git push`.
- **Not using git?** Send your end-of-session output to Elmer to commit.

## Handing off between co-DMs

Claude only knows what's in **your** clone. A co-DM who hasn't pulled is running last month's campaign.

**Outgoing session lead** (right after the session):
1. `end session`, then review Claude's changes.
2. Commit and **push**. Unpushed work is invisible to everyone else.
3. Tell the next lead (Discord is fine): "Pushed Session YYYY-MM-DD: <title>."

**Incoming session lead** (before the session):
1. `git pull`.
2. Start the chat with `Handoff: <your name> is session lead`.
3. When Claude asks whether the last session it sees is the latest, check it against the outgoing lead's message.

**Rules of thumb**
- **One session lead at a time per campaign.** Two people editing the same campaign between pulls is how you get merge conflicts.
- **Discord side quests count too.** Whoever logs one commits and pushes it.
- **If a merge conflict happens** in a campaign file, keep both versions' facts, mark what disagrees as a question in `bible.md`'s *To resolve*, and settle it at the next session.

## Changing settings

- **One campaign's settings** (lethality, initiative method, tone, lines and veils): edit that campaign's `campaign.md`. Nothing else changes.
- **How Claude runs every campaign:** edit `dm-core/instructions.md`, update the date at the top, commit and push. Co-DMs get it on their next `git pull`; nothing to re-paste.
- **A new initiative or rules module:** add it to `dm-core/modules/`, and name it in the campaign's `campaign.md`.

## One source of truth

The repo holds the canonical copy of everything, and Claude reads it directly. Whoever runs a session commits and pushes; everyone else pulls before their next session. If the files and what happened at the table disagree, the table wins for that session, and Claude flags the conflict at `end session`.
