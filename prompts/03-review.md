Review the work on this branch against .agent/plan.md using the gbuild-review
skill, dispatched to the gbuild-reviewer agent.

Write findings to __REVIEW_FILE__ as a JSON array, one object per finding:
{"severity":"blocking"|"medium"|"low","file":"...","summary":"...","resolved":false}

An empty array means clean. Write nothing else to that file, and fix nothing in
this phase — reviewing and fixing are separate models on purpose.
