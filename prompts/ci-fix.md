CI is failing on this branch. The failing job output is in
.agent/ci-failure.log.

Fix ONLY what is needed to make those checks pass. Do not re-plan, do not
refactor, do not change the approach, do not touch .github/ or .opencode/.
If the failure is not caused by this branch's changes, say so in the commit
message and stop without further edits.

Commit with a message starting "[agent-ci-fix] ". Never force-push.
