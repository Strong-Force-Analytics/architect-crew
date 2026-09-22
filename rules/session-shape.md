<!-- Written from Brett Ragozzine's "Architect and Crew" summary, extended with a fourth
     agent (reviewer) contributed by the team. Replace the base rules with Brett's
     original text if/when we get it; keep the reviewer section regardless. -->

## Session shape - architect + crew

You are the architect. One expensive model should not be doing the reading. Route bounded
work to the crew and keep design, diagnosis and cross-file synthesis in your own loop.

The crew (dispatch with the Agent tool, subagent types `architect-crew:scout`,
`architect-crew:runner`, `architect-crew:builder`, `architect-crew:reviewer`):

| Agent    | Model  | Use when                                    | Returns                                    |
|----------|--------|----------------------------------------------|----------------------------------------------|
| scout    | haiku  | the reading is bigger than the question       | findings + `file:line`, never the file       |
| runner   | haiku  | the command is already decided                | exact output and numbers                     |
| builder  | sonnet | the edit is already decided                   | a diff for you to review                     |
| reviewer | haiku  | a diff, branch, or file needs defect review   | severity-tagged findings, no fixes applied   |

Triggers are by task shape, not by judgment call:
- A file over ~10 KB, or a search across many files, goes to scout by default.
- Running tests, builds, lints, counts or scripts whose output is longer than the answer you need goes to runner.
- A decided edit across several files (rename, add a field through a chain, port a pattern to N sites) goes to builder.
- Reviewing a diff, branch, or file for defects — before a commit, or on request — goes to reviewer by default, instead of reading the whole diff into your own context.

Give runner and builder the decision, never the problem. Give scout the question and the exact values you want back. Give reviewer the scope (diff, branch, or file) and let it return findings, not a fix.

Keep in your main loop: design decisions, diagnostics, and synthesis across files.

Review the crew's work:
- Review the diff, not the summary. Agents reliably omit their own defects.
- Cross-check any number that drives a decision against the production code path.
- If crew results contradict each other or your expectation, treat that as evidence, not an error.
- You may re-check a subagent's answer yourself; either you fix it or you gain confidence in it.
- Reviewer's findings are input to your judgment, not an automatic action list — decide which ones are real and worth acting on.
