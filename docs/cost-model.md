# Cost model (checked 2026-10-20)

## Bedrock availability (us-east-1)
- Every current Claude model is ACTIVE through `us.anthropic.*` cross-region profiles
  (Opus 5.5, Sonnet 5.5, Haiku 5.5, Fable 5.1, plus older models).
- The test account had no VPC in us-east-1, so one would need creating; use public subnets to avoid a NAT Gateway.

## Infrastructure prices (AWS Pricing API, us-east-1, on-demand)
| Item | Unit price | Monthly |
|---|---|---|
| Fargate, 0.5 vCPU / 1 GB, 24/7 (agent server) | $0.04048/vCPU-hr + $0.004445/GB-hr | $18.02 |
| t3.medium, 4 hrs/day (agent computer) | $0.0416/hr | $4.99 |
| t3.medium, 24/7 | $0.0416/hr | $30.37 |
| gp3, 30 GB | $0.08/GB-month | $2.40 |
| NAT Gateway (avoid: use public subnets) | $0.045/GB plus hourly | ~$32+ |

DynamoDB, S3, EventBridge, API Gateway and Secrets Manager add under $10.
Infrastructure total: **about $25–35/month**, or about $50 with the VM running 24/7.
Avoid self-hosted Temporal or Temporal Cloud ($50–100+) and Firecracker, which needs bare-metal EC2;
use Step Functions or SQS + DynamoDB instead.

## Token costs (Anthropic list prices; Bedrock per-token prices not yet confirmed)
- Sonnet 5.5: $2 input / $10 output per million tokens, cache read $0.20, 5-minute cache write 1.25×.
- Opus 5.5: $4 / $20, cache read $0.20.
- Haiku 5.5: $0.10 / $0.50.
- The Pricing API returned no Bedrock entries for 5.5. **Check https://aws.amazon.com/bedrock/pricing/.**

Assumptions per turn: about 5 model calls, about 40K tokens of mostly cached context per call,
about 15K new input tokens and about 4K output tokens. That works out to about $0.12/turn on Sonnet
and about $0.20/turn on Opus.

| Usage | All Sonnet 5.5 | Sonnet + Opus coding sub-agent | All Opus 5.5 |
|---|---|---|---|
| Light (~20 turns/day) | ~$70 | — | ~$120 |
| Medium (~60 turns/day) | ~$215 | ~$270–300 (~5 coding tasks/day at ~3× tokens) | ~$350 |
| Heavy (150+ turns/day) | $500+ | — | $900+ |

If prompt caching breaks, these numbers roughly double.

## Alternatives
- **Managed Agents** (Anthropic API, beta; also on Claude Platform on AWS, not on Bedrock): tokens plus
  $0.08 per hour while a session runs, so about $10/month at 4 active hours a day, with no infrastructure.
- **Claude Code subscription** (scheduled cloud agents via `/schedule`, Claude in Slack): no extra
  cost within usage limits, but fewer event triggers and less control over memory. A 24/7 server bot
  should use API or Bedrock billing, not subscription credentials.

**Bottom line:** about $100–250/month for one personal bot, and 75–90% of that is model usage.
