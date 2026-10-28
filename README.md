# Cloudflare Peer Point Vancouver: System One Hackathon

Build an AI agent that thinks fast. In about two hours you'll take a working, stateful agent and teach it to
make decisions with Cloudflare's new **System One** models, then demo what you built.

## The idea in one minute

Most AI apps use a large language model for everything. That's slow and expensive, and the LLM is the
wrong tool for a lot of the job. [System One models](https://blog.cloudflare.com/clef-decision-models/)
answer in a single forward pass, in milliseconds, with typed, calibrated outputs:

| Model                                                                                                                                                                                          | It answers                         | Example                                      |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------- | -------------------------------------------- |
| [**Clef**](https://developers.cloudflare.com/workers-ai/models/clef/)                                                                                                                          | _Which option? How likely?_        | "Is this a jailbreak?" → `0.97`              |
| [**Clef-flash**](https://developers.cloudflare.com/workers-ai/models/clef-flash/)                                                                                                              | the same, faster and cheaper       | screen every message in about 50 ms          |
| [**Clef-omni**](https://developers.cloudflare.com/workers-ai/models/clef-omni/)                                                                                                                | the same, from audio and video too | "Does the fan sound broken?"                 |
| [**Clairic**](https://developers.cloudflare.com/workers-ai/models/clairic/)                                                                                                                    | _What's the value?_                | invoice total, due date, every email address |
| An LLM ([GLM-5.3 Flash](https://developers.cloudflare.com/workers-ai/models/glm-5.3-flash/), [DeepSeek V4 Flash](https://developers.cloudflare.com/workers-ai/models/deepseek-v4-flash-0731/)) | _What should I say?_               | the reply, the summary, the plan             |

**The pattern: Clairic extracts, Clef decides, the LLM writes.** Read more in
[Introducing Clef](https://blog.cloudflare.com/clef-decision-models/),
[Clef-omni](https://blog.cloudflare.com/clef-faster-cheaper-multimodal/) and
[Clairic](https://blog.cloudflare.com/clairic-extraction-model/).

## Pick a challenge

Every starter is a complete, deployable chat agent. **Checkpoint 0 already works.** Your job is to take it
further.

| Challenge                                                                                | What it does                                                                       | You'll use                              |
| ---------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- | --------------------------------------- |
| [**Agent Guardrails**](https://github.com/vnikhilbuddhavarapu/ppug-yvr-agent-guardrails) | Screens every prompt, tool call and reply before the agent acts                    | Clef-flash, Think hooks, tool approvals |
| [**Threat Hunter**](https://github.com/vnikhilbuddhavarapu/ppug-yvr-threat-hunter)       | Opens any URL in a real browser, judges it, and pulls out indicators of compromise | Browser Run, Clef (vision), Clairic, R2 |
| [**DLP Redactor**](https://github.com/vnikhilbuddhavarapu/ppug-yvr-dlp-redactor)         | Finds and redacts sensitive data before the LLM ever sees it                       | Clef-flash, Clairic spans, R2           |

Each repo's README walks you through the build:

- **Checkpoint 0** already works: run it, deploy it, read it.
- **Checkpoints 1 and 2** are scaffolded features, about 30-40 minutes each.
- **Checkpoint 3 is the boss level**, a bigger build that fills the rest of the time and makes the demo.

The same list is in [`cards.json`](cards.json).

## Bring your own use case

Got a better idea? Go for it. Pick any starter as your base (the agent, UI, Decision Trace and AI Gateway
wiring are all reusable) and build your own thing. Two ground rules:

1. **Use at least one System One model** (Clef, Clef-flash, Clef-omni or Clairic) for a real decision or
   extraction, not just decoration.
2. **Demo it running.** A deployed `workers.dev` URL is best.

Need inspiration?

- **Support triage:** Clairic pulls the order id, amount and dates out of a ticket, Clef picks the team,
  urgency and refund-or-repair, and the LLM drafts the reply.
- **Receipt and invoice inbox:** Clairic reads totals, due dates and line items from text or photos;
  Clef approves, flags or rejects.
- **Content moderation:** Clef-omni judges images, audio clips or short videos against your policy.
- **Tool router:** let Clef pick which tool or sub-agent should handle a request before the LLM runs.
- **Lead scoring:** Clairic `estimate` fields give a value with an 80% interval; rank the leads in code.

## Before you start

- **Accept your Lab invite.** Check your inbox: it gives you a Cloudflare account with everything enabled,
  and you won't pay for anything.
- **[Node.js](https://nodejs.org/) 24+** and **[Git](https://git-scm.com/)**.
- **OpenCode (optional, recommended).** Install it from [ppug-opencode.cf.prompt2prod.dev](https://ppug-opencode.cf.prompt2prod.dev).
  Each starter ships an `AGENTS.md` so your coding agent knows the codebase.

## Get going

```bash
git clone https://github.com/vnikhilbuddhavarapu/<challenge-repo>
cd <challenge-repo>
npm install
npx wrangler login        # sign in with your event account
npm run dev               # http://localhost:5173
npm run deploy            # ship it to <name>.<you>.workers.dev
```

Click the sample prompts, then watch the **Decision Trace** panel. Every Clef and Clairic call shows up
there with its probabilities, latency and cost.

## How the day runs

1. **Kickoff (30 min):** System One in a nutshell, a refresher on the agent you start with, and a live demo.
2. **Build (2 hours):** set up and deploy Checkpoint 0, then Checkpoints 1, 2 and the Checkpoint 3 boss level,
   or bring your own use case.
3. **Demos (30 min):** 3 minutes each; show it running, ideally on your live `workers.dev` URL.
4. **Winners:** the top 3 for **creativity** take home swag.

Going off-script is encouraged. The best demos usually start from a checkpoint and end somewhere nobody expected.

## What's under the hood

Every starter uses the same building blocks:

- **[Workers](https://developers.cloudflare.com/workers/)** run your code on Cloudflare's network.
- **[Agents SDK](https://developers.cloudflare.com/agents/)** and the
  **[Think harness](https://developers.cloudflare.com/agents/harnesses/think/)** give you a stateful chat agent
  with streaming, memory, tools and [lifecycle hooks](https://developers.cloudflare.com/agents/harnesses/think/lifecycle-hooks/).
- **[Durable Objects](https://developers.cloudflare.com/durable-objects/)**: one per chat session, holding its
  history, memory and SQLite data.
- **[Workers AI](https://developers.cloudflare.com/workers-ai/)** serves the System One models and the LLMs.
- **[AI Gateway](https://developers.cloudflare.com/ai-gateway/)**: every model call is logged, tagged and
  measurable. Open it in the dashboard while you build.
- **[Browser Run](https://developers.cloudflare.com/browser-run/)** (Threat Hunter) is headless Chrome you can drive from a Worker.
- **[R2](https://developers.cloudflare.com/r2/)** (Threat Hunter, DLP) stores screenshots and uploads.
- **[Wrangler](https://developers.cloudflare.com/workers/wrangler/)** is the CLI behind `npm run dev` and `npm run deploy`.

## Tips

- Start with the **Decision Trace**. If a decision looks wrong, reword the question; it's plain English.
- Clef takes up to 64 questions in one call, and they all cost the same single pass. Ask more questions, not
  longer ones.
- System One output is well-formed, not always right. Use the probabilities and confidence to decide
  what to automate and what to send to a human.
- Stuck for more than 5 minutes? Wave at a Cloudflare person. That's why we're here.
- Never commit API keys or tokens. You won't need any: the bindings handle auth.

## Learn more

- [Clef decision models](https://blog.cloudflare.com/clef-decision-models/) ·
  [Clef-omni](https://blog.cloudflare.com/clef-faster-cheaper-multimodal/) ·
  [Clairic extraction model](https://blog.cloudflare.com/clairic-extraction-model/)
- [Extraction models explained](https://developers.cloudflare.com/workers-ai/concepts/extraction-models/)
- [Think getting started](https://developers.cloudflare.com/agents/harnesses/think/getting-started/)
- [Calling models with `createAI`](https://developers.cloudflare.com/agents/models/ai-sdk/)
- [Cloudflare docs for LLMs](https://developers.cloudflare.com/llms.txt): point your coding agent here
