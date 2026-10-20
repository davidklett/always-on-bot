# Project status

**As of 2026-10-20:** research, AWS check and cost model are complete. No code yet.

## Done
- Transcribed and summarized the talk (`research-always-on-agents.md`).
- Read-only check of an AWS dev account: Bedrock Claude model availability (`cost-model.md`).
- Confirmed infrastructure prices through the AWS Pricing API.
- Decided on Sonnet 5.5 for general use with an Opus 5.5 coding sub-agent (`key-decisions.md`).

## Open questions
- Bedrock per-token prices for the Claude 5.5 models (the Pricing API returned nothing).
- What the bot should actually do (Slack/email triage, daily reports, watching deploys), which drives
  the token estimate.
- Whether Managed Agents on Claude Platform on AWS can be billed through the AWS account.

## Next step: Bedrock token-logging experiment (about $2)
Write a small Claude Agent SDK script that runs locally against Bedrock (`AWS_PROFILE=<your-profile>`,
`CLAUDE_CODE_USE_BEDROCK=1`): a Sonnet 5.5 main agent with an Opus 5.5 coding sub-agent. Log per-turn
usage (input, cache read, cache write, output tokens, model) to a JSONL file. Use it for a few days,
then replace the assumptions in `cost-model.md` with measured numbers.
Check the Agent SDK docs (code.claude.com/docs/en/agent-sdk) for the exact sub-agent and Bedrock options
before writing code.
