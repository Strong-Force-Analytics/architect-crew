<!-- Base text is Brett Ragozzine's original CLAUDE.global.md "Session shape - architect +
     crew" section, shared in Slack 2026-09-23, copied verbatim. Passages marked
     TEAM ADDITION are the team's, not Brett's - keep them if this file is ever refreshed
     from a newer version of his original. -->

## Session shape - architect + crew

Run a session as an architect + crew, not one model doing everything. The main loop holds
the plan, makes the design and diagnostic calls, reviews diffs, and writes the commit.
Bounded work goes to the crew, so the expensive model never pays to hold a file it doesn't
need.

Four agents exist; use them by name (dispatch with the Agent tool, subagent types
`architect-crew:scout`, `architect-crew:runner`, `architect-crew:builder`,
`architect-crew:reviewer`):

- **scout** - reading is bigger than the question. A large file you need three values
  from, "where is X defined and who calls it", tracing a value across files. Reading means
  every way you look at a file, rather than the Read tool alone - a run of greps, `sed -n`
  slices, or inline scripts converging on one question is the same dispatch wearing
  different clothes, and is the shape that actually shows up when a session is told to
  work through the shell. Returns findings and `file:line`, never contents.
- **runner** - the command is already decided. Suite, build, script, counts, regenerating
  an artifact. Returns numbers.
- **builder** - the edit is already decided. Returns a diff for you to review.
- **reviewer** *(TEAM ADDITION - not in Brett's original)* - a diff, branch, or file needs
  defect review. Returns severity-tagged findings only; no fixes applied, no scope creep.

Fire on the shape of the next action, never on whether a plan exists. This is what
separates a practice that holds from one that decays: coupling delegation to planned work
means it lapses the moment the plan queue empties - maintenance, a small bounded task,
between projects. Open-ended investigation is a dispatch. Anything the project's CLAUDE.md
names as large, or over ~10 KB, is a scout by default. A session that opens by writing new
code is the one most likely to skip delegation entirely, because there is no natural first
dispatch to set the habit - the measuring and the suite runs around the writing are still
dispatches. *(TEAM ADDITION: reviewing a diff, branch, or file for defects - before a
commit, or on request - is a reviewer dispatch by default, instead of reading the whole
diff into your own context.)*

Both levers, or you only get half of it. Cheaper models are the token lever; concurrent
dispatch is the wall-clock lever. Send independent dispatches as several tool calls in one
message rather than one at a time. A cheap model dispatched serially wastes the second
lever entirely.

The case to watch for is a sweep. Several long-running commands that vary a parameter - a
stress probe at four factors, a benchmark across three configs, the same script over N
inputs - have no dependency between them, however naturally they get typed one at a time.
That is N runner dispatches in one message, and the saving is the difference between the
slowest run and their sum. A measured case: four probes at a 900-second timeout each, run
in sequence, held a session for roughly 45 minutes of the possible 15.

Judgment and already-scoped small reads stay with you. The design call, the commit, the
diff review, cross-file synthesis, and a few targeted reads to confirm a fix.
Over-delegating makes the workflow feel slow and gets it abandoned for the opposite reason.

Four rules for handling what comes back, each from an observed failure:

1. **Review the diff, never the report.** A summary describes what the agent meant to do;
   the diff shows what it did. Agents reliably omit their own defects - they simply do not
   see them. One session's two worst defects appeared in no summary: a tie-break that
   would have committed an arbitrary grid corner, and an emit path that had never executed
   and ran only after a 20-minute search. Corollary: verify the code that runs last,
   first.
2. **Cross-check any decision-driving number against the production path**, and dispatch
   that cross-check rather than re-running it yourself. A delegated measurement can drift
   from production and fail silently in the optimistic direction. You compare the number;
   a runner produces it.
3. **A uniformly negative measurement is a premise smell - question the question.**
   "Every candidate at every setting violates this constraint" is evidence about the
   question, not only the answer. The same applies to an inherited explanation: a
   previous session's stated reason is an input to test, not a premise to build on,
   especially when it arrives already shaped into an action list, because the list makes
   the premise feel settled.
4. **An empty return is a resume to chase.** A dispatch that comes back with nothing is
   usually an agent that stopped early, so send it another message before concluding there
   was nothing to find. An empty return read as a silent success is the failure this rule
   exists to catch. Its other half needs no prose: no crew definition carries the Agent
   tool, so a dispatch already cannot spawn background children of its own.

*(TEAM ADDITION: reviewer's findings are input to your judgment, not an automatic action
list - decide which ones are real and worth acting on.)*

Window size is the last lever, not the first. A bigger window raises the ceiling before a
compaction but does not lower the per-read cost, and it erodes the discipline that
actually controls cost.
