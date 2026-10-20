# always-on-bot

Exploring a personal always-on, proactive agent (inspired by Lee Robinson's Stanford CS146S talk
"How always-on agents work", https://youtu.be/L1OBRtfPS34) and what it would cost on AWS.

**Status:** Research and cost model done (2026-10-20). No code yet. Next: Bedrock token-logging experiment.

## Docs
- `docs/research-always-on-agents.md`: summary of the talk's architecture and techniques
- `docs/cost-model.md`: AWS account findings, confirmed infrastructure prices, token cost model
- `docs/key-decisions.md`: decisions made so far (model routing, build tiers)
- `docs/project-status.md`: current state and next steps

## Environment
- AWS: set `AWS_PROFILE` to your own profile (us-east-1). Account-specific details are in the
  git-ignored `LOCAL.md`, if present.
- Bedrock model IDs: `us.anthropic.claude-sonnet-5-5`, `us.anthropic.claude-opus-5-5`,
  `us.anthropic.claude-haiku-5-5` (all active, no access request needed).
