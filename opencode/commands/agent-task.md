---
description: Work a Linear issue end to end and open a PR
agent: build
---

Task: Linear issue $ARGUMENTS.

Read it before anything else:
  everything-cli linear issue get $ARGUMENTS --format toon
  everything-cli linear issue comments $ARGUMENTS --format toon

This is a non-interactive session. Nobody will answer questions.
If the issue is too underspecified for sound assumptions, do NOT implement.
Post your open questions instead:
  everything-cli linear issue comment create $ARGUMENTS --body "..."
and stop without opening a PR.
