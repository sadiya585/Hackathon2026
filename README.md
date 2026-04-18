# TV2Z Research Copilot

An internal multi-agent AI system for the TV2Z product team. Ask a feature or market question in chat — the Supervisor routes it to specialist agents, searches the web in real time, and returns a structured Feature Intelligence Report grounded in TV2Z's actual stack, devices, competitors, and KPN infrastructure.

---

## Architecture

```
PM Chat Input
      │
      ▼
Supervisor Agent (GPT-4.1)
      │
      ├── 🔍 Competitor Tool        — per-competitor research, gaps, UX benchmarks
      ├── 📱 Device Tool            — 10-device implementation guide
      ├── 🔧 Tech Stack Tool        — build vs buy, libraries, architecture
      ├── 📡 IP TV Tool             — KPN dependency check, HLS/catch-up impact
      └── 📊 Market Intelligence    — market sizing, pricing, regulatory landscape
                │
                ▼
      Feature Intelligence Report
```

Each agent has its own **GPT-4.1** model, **SerpAPI** (live web search), and **memory buffer**. The Supervisor has TV2Z's full product context embedded — 5 products, 20 competitors, 10 devices, full tech stack, KPN infrastructure model.

---

## Prerequisites

| Tool | Purpose |
|---|---|
| [TV2Z Market Researcher](https://tv2z-chatbot-n8n-server.thethi.ng/webhook/1606b2f3-7b01-4929-a052-41b56942bd24/chat) (self-hosted or cloud) | Workflow orchestration |
| OpenAI API key | GPT-4.1 for all 6 agents |
| SerpAPI key | Live web search for all 5 specialist tools |

---

## Setup

1. Import `market_researcher.json` into n8n via **Workflows → Import from file**
2. Add credentials in n8n:
   - **OpenAI** — used by all 6 `lmChatOpenAi` nodes
   - **SerpAPI** — used by all 5 `toolSerpApi` nodes
3. Activate the workflow
4. Open the chat via the **When chat message received** trigger URL

No other configuration needed — all TV2Z context is embedded in the agent system prompts.

---

## What It Produces

For full feature research queries, the output follows this structure:

- **Executive Summary** — what matters, what TV2Z should do
- **Per-Competitor Findings** — individually searched, evidence-labelled (🔍 search / 🧠 prior knowledge)
- **Device Impact Matrix** — Web, SDMC box, Tizen, WebOS, iOS, Android, Roku
- **Tech Stack & Implementation** — named libraries, build vs buy, architecture sketch
- **Live IPTV Considerations** — KPN dependency check ⚠️, HLS/catch-up impact
- **Market Intelligence** — sizing, operator demand, pricing, EU regulatory landscape
- **Recommended Approach** — phased plan with LOE estimate

For narrower questions, the Supervisor returns a concise direct answer without the full template.

---

## File Structure

```
market_researcher.json   ← n8n workflow (import this)
README.md
```

---

## Agents — Quick Reference

| Agent | Trigger it when asking about |
|---|---|
| Competitor Tool | Who has this feature, how is it built, what can TV2Z learn |
| Device Tool | Cross-platform implementation, device-specific SDK/player/UX |
| Tech Stack Tool | Which library, build vs buy, quick wins with existing integrations |
| IP TV Tool | Live TV, catch-up, recording, KPN feed changes, HLS/DASH |
| Market Intelligence | Market size, operator demand, competitor pricing, EU regulations |

---

## Notes

- **Internal use only** — no customer-facing surface, no external auth required
- The Competitor Tool is configured to search each competitor individually before drawing conclusions — it will not group competitors or generalise without evidence
- KPN dependency is flagged explicitly on every IPTV-related response
- All agents use `gpt-4.1` — do not downgrade to mini for the Supervisor as it loses multi-tool reasoning quality
