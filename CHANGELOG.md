# Changelog

Bump `version` in `.claude-plugin/plugin.json` for every change users should receive;
Claude Code only updates a plugin when its version changes.

## 0.3.0

- `rules/session-shape.md` replaced with Brett Ragozzine's original "Session shape -
  architect + crew" text from his `CLAUDE.global.md`, shared 2026-09-23. This is his real
  wording, not the team's earlier reconstruction from his summary artifact.
- Kept the `reviewer` agent and its two rule additions from 0.2.0, marked inline as team
  additions so it stays clear which parts are Brett's and which aren't.
- Everyone should update: the previous rules text was a guess at thresholds and wording;
  this version has Brett's actual dispatch triggers, the concurrent-dispatch guidance, and
  the four return-handling rules.

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
