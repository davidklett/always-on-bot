# always-on-bot

Research, cost model and design decisions for a **personal always-on AI agent** running on AWS:
an agent that lives on a server, wakes up on events (Slack, email, schedules), uses its own cloud
computer, and keeps working while you're away.

Inspired by Lee Robinson's Stanford CS146S lecture
[How always-on agents work](https://youtu.be/L1OBRtfPS34).

## Current state

**Research phase. No code yet.** (Last updated 2026-10-20.)

| Area | Status |
|---|---|
| Summary of the talk's architecture and techniques | Done |
| Claude availability on Amazon Bedrock (us-east-1) | Confirmed: Opus 5.5, Sonnet 5.5 and Haiku 5.5 are active |
| AWS infrastructure prices | Confirmed through the AWS Pricing API |
| Token cost model | Estimated from assumptions; not yet measured |
| Model routing | Decided: Sonnet 5.5 for general use, an Opus 5.5 sub-agent for coding |
| Bedrock token-logging experiment | Next step |

### Headline numbers (one personal bot)

- **Infrastructure:** about $25–35/month (a Fargate agent server, plus an EC2 computer that runs
  only when needed).
- **Model usage:** about $70–300/month, depending on how much you use it. This is 75–90% of the total.
- **Total:** about **$100–250/month** at moderate use.

See [docs/cost-model.md](docs/cost-model.md) for the breakdown and assumptions.

## Repository layout

| Path | Contents |
|---|---|
| [docs/research-always-on-agents.md](docs/research-always-on-agents.md) | How always-on agents work: triggers, agent server, durable workflows, the agent's computer, context engineering, memory, safety |
| [docs/cost-model.md](docs/cost-model.md) | AWS and token prices, cost scenarios, alternatives (Managed Agents, Claude Code routines) |
| [docs/key-decisions.md](docs/key-decisions.md) | Decisions and the reasoning behind them |
| [docs/project-status.md](docs/project-status.md) | What's done, open questions, next steps |
| `CLAUDE.md` | Context for Claude Code sessions working in this repo |

## Usage

### Read the research
Start with `docs/research-always-on-agents.md`, then read `docs/cost-model.md` to see what it would
cost to build.

### Check the prices yourself
Requires the AWS CLI and credentials for any account (the Pricing API is read-only and free).

```bash
export AWS_PROFILE=<your-profile>

# Which Claude models Bedrock offers in your region
aws bedrock list-foundation-models --by-provider anthropic \
  --query 'modelSummaries[].[modelId,modelLifecycle.status]' --output text

# EC2 t3.medium on-demand price (us-east-1)
aws pricing get-products --region us-east-1 --service-code AmazonEC2 \
  --filters "Type=TERM_MATCH,Field=location,Value=US East (N. Virginia)" \
            Type=TERM_MATCH,Field=instanceType,Value=t3.medium \
            Type=TERM_MATCH,Field=operatingSystem,Value=Linux \
            Type=TERM_MATCH,Field=tenancy,Value=Shared \
            Type=TERM_MATCH,Field=preInstalledSw,Value=NA \
            Type=TERM_MATCH,Field=capacitystatus,Value=Used \
  --query 'PriceList[0]' --output text
```

In zsh, pass the profile through `AWS_PROFILE` as shown above. Putting `--profile <name>` in a shell
variable fails because zsh doesn't split variables into separate arguments.

Bedrock's per-token prices for Claude aren't returned by the Pricing API; check the
[Bedrock pricing page](https://aws.amazon.com/bedrock/pricing/).

### Continue with Claude Code
Open this directory in Claude Code. `CLAUDE.md` and `docs/project-status.md` give a new session the
full context. Keep account-specific notes in a `LOCAL.md` file, which `.gitignore` already excludes.

## Roadmap

1. **Token-logging experiment** (about $2 of Bedrock usage): a local Claude Agent SDK script with a
   Sonnet 5.5 main agent and an Opus 5.5 coding sub-agent, logging per-turn token usage to JSONL.
2. Replace the estimated token costs in `docs/cost-model.md` with measured numbers.
3. Choose how much to build: Claude Code routines, Anthropic Managed Agents, or a self-built stack on
   AWS (Lambda and EventBridge triggers, Step Functions or Fargate, DynamoDB and S3, and EC2).
