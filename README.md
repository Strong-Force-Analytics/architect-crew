# architect-crew

Make long Claude Code sessions cheaper by letting a **cheap model do the reading**.

Your main session (Opus) acts as the *architect*: it plans, decides and reviews. Bounded
work goes to a small *crew* of cheaper subagents instead:

| Agent   | Model  | Give it...                          | It returns...                               |
|---------|--------|-------------------------------------|---------------------------------------------|
| scout   | Haiku  | a big file or a wide search         | only the answer + `file:line`, never the file |
| runner  | Haiku  | a command you already decided       | the exact output and numbers                |
| builder | Sonnet | an edit you already decided         | a diff for the architect to review          |

Why it matters: reading large files and running noisy commands is what burns a session's
budget. The crew reads a lot and hands back a little.

> Pattern and agent definitions by **Brett Ragozzine**.
> His write-up (with measured results): <https://claude.ai/code/artifact/f76db9cd-72de-4a80-a8c9-85e6576b9422>

---

## Install (2 minutes)

You need: Claude Code, and read access to the two private repos in the
`Strong-Force-Analytics` GitHub org (see [Access](#access)).

In Claude Code, run:

```
/plugin marketplace add Strong-Force-Analytics/claude-plugins
/plugin install architect-crew@sfa-plugins
/reload-plugins
```

That's it. Nothing else to configure.

### Check it worked

1. Type `/context`. Under custom agents you should see `architect-crew:scout`,
   `architect-crew:runner` and `architect-crew:builder`.
2. Start a **new** session and ask: *"Do you have a 'Session shape - architect + crew'
   section in your instructions?"* It should say yes.

## Use it

You don't have to do anything special. Work as usual, with **Opus** as your main model
(`/model opus`). The rules injected at session start tell the architect when to delegate:

- a file over ~10 KB, or a search across many files → **scout**
- tests, builds, lints, counts whose output is long → **runner**
- a decided edit across several files (rename, add a field, port a pattern) → **builder**

You can also ask for it directly:

```
Use scout to find where the retry logic is defined and who calls it.
Have runner run the test suite and report the failing test names and the totals.
Have builder rename `getUser` to `fetchUser` everywhere, then show me the diff.
```

Rules of thumb:

- **Give builder the decision, not the problem.** "Rename X to Y in these files" works.
  "Fix the login bug" does not; that's a design task for the architect.
- **Ask scout a specific question**, and say which values you want back.
- **Review the diff, not the summary.** Agents can leave out their own mistakes.
- Design decisions, debugging and cross-file reasoning stay with the architect.

## Update

```
/plugin marketplace update
/reload-plugins
```

New versions are announced in [CHANGELOG.md](CHANGELOG.md). Updates only arrive when the
`version` in `.claude-plugin/plugin.json` is bumped.

## Turn off / remove

```
/plugin disable architect-crew@sfa-plugins
/plugin uninstall architect-crew@sfa-plugins
```

## Access

Both repos are private:

- <https://github.com/Strong-Force-Analytics/architect-crew>
- <https://github.com/Strong-Force-Analytics/claude-plugins> (the marketplace)

You need to be a member of the `Strong-Force-Analytics` org (or a collaborator on both
repos) **and** have git credentials on your machine:

```
gh auth login
```

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `/plugin marketplace add` fails with "repository not found" or an auth error | You lack access or aren't logged in. Run `gh auth login` and ask an org admin to add you to both repos. |
| Agents don't show in `/context` | Run `/reload-plugins`, or restart Claude Code. |
| Agents show but the architect never delegates | The rules load at session start. Start a **new** session, and make sure your main model is Opus. |
| Windows: rules don't load | The hook runs in Git Bash. Install [Git for Windows](https://git-scm.com/download/win) and restart. |
| You already have your own `scout`/`runner`/`builder` agents | Per the Claude Code docs, agents in your `.claude/agents/` override same-named plugin agents. Rename or remove yours to use the plugin versions. |

## What's in this repo

```
.claude-plugin/plugin.json   plugin manifest (name, version)
agents/                      scout.md, runner.md, builder.md (model set in frontmatter)
hooks/hooks.json             SessionStart hook: injects the delegation rules
rules/session-shape.md       the delegation rules the architect follows
CHANGELOG.md                 release notes
```

The plugin can't write to your `~/.claude/CLAUDE.md`, so the rules are delivered by a
SessionStart hook, which runs on every start, resume, `/clear` and compaction.

## Contributing

Change the agents or rules in a branch, bump `version` in `.claude-plugin/plugin.json`,
add a line to `CHANGELOG.md`, and open a pull request. CI runs
`claude plugin validate`. To try changes locally without installing:

```
claude --plugin-dir ./architect-crew
```

## Status

**v0.1.0, first team release.**

- The rules in `rules/session-shape.md` are written from Brett's summary, not his
  original `CLAUDE.global.md` text. They should be replaced with his version.
- Tested: plugin validates, and in a real session the rules load and all three agents
  are available. Not yet measured: how much budget this saves for *our* work. Try it and
  tell us.

Internal use. No open-source license has been chosen; that's Brett's decision if this
ever goes public.
