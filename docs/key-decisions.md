# Key decisions

## 2026-10-20: Use Sonnet 5.5 for general use and route coding to Opus 5.5
- The main agent (chat, triage, Slack/email, scheduling, research) uses `claude-sonnet-5-5`.
- Coding work goes to a sub-agent on `claude-opus-5-5` (a Claude Agent SDK sub-agent with its model set
  to Opus and a description like "use for writing, reviewing or debugging code"). Only the result
  (a summary or PR link) comes back to the main agent.
- Optional: the API's advisor tool, with Sonnet 5.5 consulting an Opus 5.5 advisor, for hard non-coding
  questions.
- Why: Opus quality for code for about 30% more than all-Sonnet, versus about 60% more for all-Opus.
  Sub-agents start with a fresh context, so switching models costs no prompt caching.
- Cheaper alternative still open: the bot hands coding off to the user's Claude Code subscription
  (via a ticket or ping) instead of coding itself.

## 2026-10-20: Choosing how much to build
1. Claude Code routines and Claude in Slack: free, try these first.
2. Managed Agents: the closest match to the talk's architecture without running infrastructure.
3. Build it ourselves on AWS: Lambda/API Gateway and EventBridge for triggers, Step Functions or Fargate
   for the agent server, DynamoDB and S3 for state and memory files, EC2 stop/start as the agent's
   computer, and Bedrock for the models. Only if we need control over the VM, memory or data.
- Avoid Temporal and Firecracker at this scale.
