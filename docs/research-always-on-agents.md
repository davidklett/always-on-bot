# Research: how always-on agents work

Source: Stanford CS146S lecture, "How always-on agents work", Lee Robinson (formerly at Cursor, now at SpaceX),
45 min, published 2026-10-11. https://youtu.be/L1OBRtfPS34. It covers "Grok Bot", SpaceX's always-on agent.
This file is our own summary, not a transcript.

## What makes an agent "always-on"
- It runs on a server rather than your laptop, so it survives a closed lid, restarts and crashes.
- It has its own Linux computer (desktop, files, browser).
- It decides when to stay quiet and when to report back, like a colleague.
- Events wake it up (Slack, email, meetings, routines, finished background jobs), not only your prompts.
- It can work for hours or days, and in the future weeks or months.

## Architecture
1. **Triggers → router.** Events are de-duplicated and merged into the conversation. A rule decides
   whether to notify the user.
2. **Agent server: a thin client and a heavy server.** A priority queue feeds the agent loop (a model calling
   tools). The user's own messages interrupt current work; a burst of messages is handled in one turn.
   All state is saved to a database so the server and the VM can sleep.
3. **Durable workflows** (they use Temporal): after a crash, work resumes at the failed step instead of
   replaying from the start. Each sub-agent is its own durable workflow.
4. **The agent's computer:** a Firecracker VM that can be snapshotted and resumed, and runs only when needed.
   The agent loop stays on the server so the VM can be rebuilt (for example to patch it) without downtime.
   For browsing it tries the accessibility tree first, then the page HTML, then screenshots, then clicking
   on pixels.
5. **One tool between client and server:** `send_to_user`. Each client (mobile app, Slack, email) decides
   how to display it: sign-in forms, payment, approving a draft, an emoji reaction.

## Harness and context techniques
- **Tool search:** only tool names go into the prompt; full schemas load when a tool is needed.
- **Try the cheapest option first:** what's already in context → a plugin/MCP server/API → web search →
  the browser → the full desktop → only then ask the user.
- **Sub-agents ("helpers"):** expensive work runs in a fresh context and only the result comes back,
  which makes context feel unlimited.
- **Prompt caching:** keep tools and the system prompt fixed and in a fixed order (hash each section).
  Treat a cache miss as a bug. Warm the cache when the user starts typing.
- **Compact early:** summarize while the cache is still warm, so the next session starts from a cheap summary.
- **Write long outputs to files**; avoid adding or removing tools mid-turn; write error messages agents
  can act on.
- **Memory layers:** core user facts in every prompt, dated logs, short-lived scratch notes, and everything
  else in searchable files. Skills are markdown files; routines are prompts on a schedule.

## Safety
- A second model reviews commands before they run ("auto review").
- Emails and webhooks are untrusted input, never treated as instructions.
- Irreversible actions (send email/Slack) need the user's approval; passwords stay out of the model's context.

## The speaker's predictions
Agents that run for weeks or months, better recall, voice interfaces, agents learning skills on the job,
and companies writing down their processes so agents can follow them. Open problems: evaluating agents that
run for a month, memory, security, and knowing when the agent should speak up.
