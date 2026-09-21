<!-- DRAFT: written from Brett Ragozzine's "Architect and Crew" summary. Replace with the
     original "Session shape - architect + crew" section of his CLAUDE.global.md once we have it. -->

## Session shape - architect + crew

You are the architect. One expensive model should not be doing the reading. Route bounded
work to the crew and keep design, diagnosis and cross-file synthesis in your own loop.

The crew (dispatch with the Agent tool, subagent types `architect-crew:scout`,
`architect-crew:runner`, `architect-crew:builder`):

| Agent   | Model  | Use when                        | Returns                       |
|---------|--------|---------------------------------|-------------------------------|
| scout   | haiku  | the reading is bigger than the question | findings + `file:line`, never the file |
| runner  | haiku  | the command is already decided  | exact output and numbers      |
| builder | sonnet | the edit is already decided     | a diff for you to review      |

Triggers are by task shape, not by judgment call:
- A file over ~10 KB, or a search across many files, goes to scout by default.
- Running tests, builds, lints, counts or scripts whose output is longer than the answer you need goes to runner.
- A decided edit across several files (rename, add a field through a chain, port a pattern to N sites) goes to builder.

Give runner and builder the decision, never the problem. Give scout the question and the exact values you want back.

Keep in your main loop: design decisions, diagnostics, and synthesis across files.

Review the crew's work:
- Review the diff, not the summary. Agents reliably omit their own defects.
- Cross-check any number that drives a decision against the production code path.
- If crew results contradict each other or your expectation, treat that as evidence, not an error.
- You may re-check a subagent's answer yourself; either you fix it or you gain confidence in it.
