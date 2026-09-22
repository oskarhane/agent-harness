Task: Linear issue __LINEAR_ID__.

Read it before anything else:
  everything-cli linear issue get __LINEAR_ID__ --format toon
  everything-cli linear issue comments __LINEAR_ID__ --format toon

This is a non-interactive session. Nobody will answer questions, and you will
be handed off to a different model for implementation — so the plan has to
stand on its own.

If the issue is too underspecified for sound assumptions, do NOT plan. Post
your open questions instead:
  everything-cli linear issue comment create __LINEAR_ID__ --body "..."
then write the single line NEEDS-CLARIFICATION to .agent/plan.md and stop.

Otherwise use the gbuild-plan skill and write .agent/plan.md containing:
- the change, file by file
- every assumption you are making, stated explicitly
- how it will be verified (which tests, which command)

Plan only. Do not edit any file other than .agent/plan.md. Work will happen on
branch __BRANCH__.
