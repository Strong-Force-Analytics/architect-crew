# Changelog

Bump `version` in `.claude-plugin/plugin.json` for every change users should receive;
Claude Code only updates a plugin when its version changes.

## 0.2.0

- Added `reviewer` (Haiku): reviews a diff, branch, or file for defects and returns
  severity-tagged findings only — no fixes applied, no scope creep. Contributed from a
  teammate's independent architect + crew setup, folded in after the two were compared.
- `rules/session-shape.md`: added the reviewer row, its dispatch trigger, and a note that
  reviewer findings are input to the architect's judgment, not an automatic action list.
- `plugin.json` description now lists all four agents.

## 0.1.0

- First team release.
- Agents: `scout` (Haiku), `runner` (Haiku), `builder` (Sonnet), from Brett Ragozzine.
- SessionStart hook injects the architect + crew delegation rules.
- Rules are a draft written from Brett's summary; replace with his original text.
