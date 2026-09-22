Task: the spec in .agent/task.md — a frozen snapshot of GitHub issue
#__ISSUE_NUMBER__ taken when this run was triggered. Do NOT re-fetch the issue
via gh or any API: later edits by the issue author are deliberately excluded;
the snapshot is the approved spec.

If the snapshot references a Linear identifier (e.g. ENG-123), read that card
as supplementary context:
  everything-cli linear issue get <ID> --format toon
  everything-cli linear issue comments <ID> --format toon
The frozen snapshot remains the spec of record.

This is a non-interactive session. Nobody will answer questions, and you will
be handed off to a different model for implementation — so the plan has to
stand on its own.

If the spec is too underspecified for sound assumptions, do NOT plan. Post
your open questions — on the Linear card if one is linked, otherwise on the
GitHub issue:
  everything-cli linear issue comment create <ID> --body "..."
  gh issue comment __ISSUE_NUMBER__ --body "..."
then write the single line NEEDS-CLARIFICATION to .agent/plan.md and stop.

Otherwise use the gbuild-plan skill and write .agent/plan.md containing:
- the change, file by file
- every assumption you are making, stated explicitly
- how it will be verified (which tests, which command)

Plan only. Do not edit any file other than .agent/plan.md. Work will happen on
branch __BRANCH__.
