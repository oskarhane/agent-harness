# agent-harness

Central definition of the Linear -> opencode -> PR agent. Target repos hold a
caller workflow and nothing else.

| Path | What it is |
|---|---|
| `.github/workflows/agent.yml` | reusable: label on a pointer issue -> PR |
| `.github/workflows/agent-ci-fix.yml` | reusable: red CI on an `agent/*` branch -> fix commit |
| `opencode/opencode.jsonc` | policy: plugin pin, permissions, subagent depth |
| `opencode/agents/gbuild-reviewer.md` | read-only reviewer (plugins cannot register agents; config can) |
| `opencode/commands/agent-task.md` | the flow as an opencode command |
| `prompts/01-plan.md` … `05-pr.md` | one prompt per phase |
| `prompts/ci-fix.md` | the deliberately narrow CI-fix prompt |
| `agent-onboard.sh` | run from a target repo clone to wire that repo up to this harness |

## Onboarding a target repo

The onboarding script ships here, so this repo is all you need to enable a new
target:

    gh repo clone <you>/agent-harness    # or curl the raw script at tag v1
    cd /path/to/target-repo
    /path/to/agent-harness/agent-onboard.sh --harness <you>/agent-harness --owner <you> \
      --app-id <app-id> --app-key-file ~/keys/agent.pem --ci-workflow "CI" --dry-run

Inspect the dry run, then re-run without `--dry-run`. It creates the `agent`
label, three secrets, two variables, the gated `agent` environment, and commits
the two caller workflows on a branch with a PR. The script operates on whatever
repo you run it from — nothing of it stays behind beyond the two callers.

## Phases and models

Each phase is its own `opencode run` against one session, so each can carry
its own model. Empty inputs fall back to `model`.

| Phase | Prompt | Model input | Gate after it |
|---|---|---|---|
| 1 plan | 01-plan.md | `plan-model` | `.agent/plan.md` exists; NEEDS-CLARIFICATION stops the run |
| 2 implement | 02-implement.md | `implement-model` | commits or working-tree changes exist |
| 3,5 review | 03-review.md | `review-model` | `.agent/review-N.json` exists and parses as an array |
| 4,6 fix | 04-fix.md | `implement-model` | next review cycle |
| 7 pr | 05-pr.md | `pr-model` | a PR exists on the branch |

All phases attach to one `opencode serve` so plugin and MCP cold boot is paid
once rather than seven times. The session id is created up front via
`opencode api v2.session.create` and threaded with `--session`; if that call
fails the run falls back to `--continue`, which is correct here but would be
ambiguous if anything else ran in the same workspace.

Callers pin `@v1`. To roll a change out everywhere: commit, then move the tag.

    git tag -f v1 && git push -f origin v1

Current defaults: model `fireworks-ai/kimi-latest`, opencode `2.0.1`,
opencode-gbuild `0.4.0`, everything-cli ref `main`.
A caller can override any of them per repo via the `with:` block.

## Fireworks notes

Provider id is `fireworks-ai` (not `fireworks`), env var
`FIREWORKS_API_KEY`, OpenAI-compatible at
`https://api.fireworks.ai/inference/v1`. Model ids are provider-qualified:
`fireworks-ai/<slug>` or `fireworks-ai/accounts/fireworks/models/<name>`.

Two things to watch, both specific to open-weight models behind an
OpenAI-compatible endpoint:

- **Multiple leading system messages.** Some open-model chat templates reject
  more than one, and this flow stacks a system prompt plus AGENTS.md plus skill
  instructions. Other harnesses coalesce consecutive system messages
  specifically for Fireworks-style hosts. If the first call 400s, that is why.
- **Tool-calling quality is the binding constraint.** The gbuild flow is
  tool-call heavy and dispatches subagents; a model that is strong at prose but
  loose with tool schemas will fail in ways that look like prompt bugs. Test
  the plan -> review -> pr chain on one throwaway card before switching models.

FireConnect (`fireconnect opencode on`) is the vendor's setup CLI, but it
rewrites `~/.config/opencode/opencode.json` and bakes the key in plaintext —
wrong shape for CI. This harness declares the provider itself instead.
