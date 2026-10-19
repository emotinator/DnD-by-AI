# Claude as DM — campaign repo

This repo runs D&D campaigns with Claude as Dungeon Master or co-DM. It holds two things, kept separate:

- **The DM core** (`dm-core/`): how Claude runs *any* campaign. Dice, turn format, encounter philosophy, sheets, continuity, handoffs. Same for every campaign.
- **Campaigns** (`campaigns/<name>/`): one folder per campaign. Its settings, world, party and history.

Everything is plain Markdown, so any co-DM can rebuild the same DM on their own Claude account or machine.

## Layout

```
README.md                  # this file
dm-core/
  instructions.md          # DM core → paste into the Claude project's instructions
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

**Working set vs. archive.** Each campaign's five top-level files (`campaign.md`, `bible.md`, `roster.md`, `house-rules.md`, `log-recent.md`) are the *working set*: small, read every session, uploaded to the Claude project. `sessions/` and `archive/` stay in the repo and are read only when an old detail is needed.

> **Spoiler warning:** `bible.md` and `archive/prep/` contain DM-only secrets. Players shouldn't browse this repo.

## What you need

- A Claude account with **Projects** (claude.ai or the Claude desktop app).
- Git access to this repo, or a downloaded copy.
- **Optional, desktop app only:**
  - **D&D Beyond MCP server**, so Claude can read character sheets live. Install it per that server's own README and log in with the shared scribe account. Claude only uses its read tools.
  - **A rules MCP server** (e.g. Open5e) for exact spell text and stat blocks. Not required; without it Claude rules from its own knowledge and flags rulings.
  - **The campaign folder linked to the chat**, so Claude can write end-of-session updates straight into the repo instead of handing you text to paste.

## Continue the campaign in progress

1. **Get the latest files.** `git pull` (or download the repo). Someone may have played since you last synced.
2. **Create a Claude project,** one per campaign, e.g. "DM — Lapaliiya".
3. **Paste `dm-core/instructions.md`** into the project's custom instructions.
4. **Upload the working set** to the project's knowledge:
   - `campaigns/lapaliiya/campaign.md`
   - `campaigns/lapaliiya/bible.md`
   - `campaigns/lapaliiya/roster.md`
   - `campaigns/lapaliiya/house-rules.md`
   - `campaigns/lapaliiya/log-recent.md`
   - `dm-core/modules/drama-deck.md` (because this campaign uses the Drama Deck)
5. **Optional:** connect the D&D Beyond MCP and link `campaigns/lapaliiya/` in the desktop app.
6. **Start a chat** with:
   ```
   Handoff: <your name> is session lead
   ```
   Claude briefs you: where the party is, active threats, open threads, any secrets about to matter. Then start play, or ask for a "Previously…" recap for the table.

**Quick check before your first live session:** ask Claude `OOC: list the knowledge files you can see and where the party is now`. If it can't name the files and the current situation, the upload didn't take.

## Start a new campaign

1. **Copy the template:** `cp -r campaigns/_template campaigns/<new-name>`.
2. **Create a new Claude project** for it, paste `dm-core/instructions.md` into its instructions, and upload the template's five files (plus `dm-core/modules/drama-deck.md` if you'll use it).
3. **Run session zero:** start a chat with
   ```
   OOC: run session zero for a new campaign
   ```
   Claude walks you through `campaign.md` (premise, ruleset, leveling, initiative, lethality, tone, lines and veils, table, tools), takes the party list, and drafts the opening `bible.md`, `roster.md` and `log-recent.md`.
4. **Save the drafts** into `campaigns/<new-name>/`, re-upload them to the project, and commit.

## During and after a session

- **Input:** the session lead types for the table, e.g. `Hurintha: attacks, 17 to hit, 14 damage.` Lines starting `DM:` or `OOC:` are instructions to Claude. Discord posts go in with `DISCORD:`.
- **`end session`:** Claude writes the session file and the full text of every changed working-set file, plus hooks for next time.
- **Save and commit:**
  1. Put the new session file in `campaigns/<name>/sessions/`.
  2. Replace the changed working-set files.
  3. Commit: `git commit -m "<campaign>: Session YYYY-MM-DD: <short title>"` and push.
  4. **Re-upload the changed files to the Claude project** so its knowledge matches the repo.
- **Not using git?** Send your end-of-session output to Elmer to commit.

## Changing settings

- **One campaign's settings** (lethality, initiative method, tone, lines and veils): edit that campaign's `campaign.md`. Nothing else changes.
- **How Claude runs every campaign:** edit `dm-core/instructions.md`, update the date at the top, and tell the other co-DMs to re-paste it into their projects.
- **A new initiative or rules module:** add it to `dm-core/modules/`, and name it in the campaign's `campaign.md`.

## One source of truth

The repo holds the canonical copy of everything. Whoever runs a session commits their updates; everyone else pulls and re-uploads before their next session. If a Claude project and the repo disagree, the repo wins, unless something happened at the table that hasn't been committed yet.
