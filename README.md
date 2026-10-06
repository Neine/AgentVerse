<div align="center">

# 🌌 AgentVerse

**Hands-on notebooks for learning and building agentic AI systems.**

`Python 3.10+` · `OpenAI Agents SDK` · `Jupyter` · `3 projects`

</div>

AgentVerse is a growing collection of practical, runnable Jupyter notebooks built on the [OpenAI Agents SDK](https://github.com/openai/openai-agents-python). Each project takes a real-world use case and walks through the core agent patterns behind it: tools, multi-agent orchestration, handoffs, parallel execution and tracing.

---

## 🧩 Projects

| Project | What it builds | Key patterns |
|---|---|---|
| 📧 [**SalesAgent**](OpenAISDK/SalesAgent.ipynb) | An automated SDR that writes, picks and sends cold emails for a fictional SOC 2 compliance SaaS (ComplAI) | Parallel agents · Picker agent · Agents-as-tools · Handoffs · Function tools · Tracing |
| 💬 [**AskMyCV**](OpenAISDK/Askmycv.ipynb) | A personal "ask my CV" chatbot that answers visitor questions about a career, using a LinkedIn PDF + summary as its knowledge base | Persona prompting · Function tools · Push notifications · Gradio chat UI |
| 🔎 [**DeepResearch**](OpenAISDK/DeepResearch.ipynb) | A research agent that plans web searches, runs them in parallel, writes a long-form report and emails it | Code orchestration · Structured outputs · Hosted WebSearchTool · `asyncio.gather` · Tracing |

Each notebook has a detailed companion guide: [SalesAgent-ReadMe](OpenAISDK/SalesAgent-ReadMe.txt) · [Askmycv-ReadMe](OpenAISDK/Askmycv-ReadMe.txt)

---

### 📧 SalesAgent: Automated SDR workflow

```
Sales Manager
   ├── Professional Sales Agent ─┐
   ├── Engaging Sales Agent ─────┼──► pick best draft
   ├── Busy Sales Agent ─────────┘
   └── handoff ──► Email Manager
                      ├── Subject writer
                      ├── HTML converter
                      └── send_html_email (SendGrid)
```

- Three writer agents with different tones (formal, witty, concise) run in parallel
- A Sales Manager orchestrates draft → select → hand off
- An Email Manager adds a subject line, converts to HTML and sends via SendGrid
- Every run is traceable on the OpenAI platform

### 💬 AskMyCV: Career chatbot

- Loads a knowledge base from `me/linkedin.pdf` and `me/summary.txt`
- Agent answers in the first person, as the profile owner
- `record_user_details` captures visitor contact info; `record_unknown_question` logs anything it can't answer
- Both trigger instant **Pushover** notifications
- Served through a **Gradio** chat interface

### 🔎 DeepResearch: Plan → Search → Write → Email

```
query
  └── Planner Agent ──► WebSearchPlan (5 searches)
          └── Search Agent × 5  (parallel, asyncio.gather)
                  └── Writer Agent ──► ReportData
                          └── Email Agent ──► send_email_tool
```

- Orchestrated **in code**: each step is a separate `Runner.run()` call, so the flow is predictable and debuggable
- **Structured outputs** with Pydantic: `WebSearchPlan` for the plan and `ReportData` for the report (summary, markdown report, follow-up questions)
- Search Agent uses OpenAI's hosted `WebSearchTool` with `tool_choice="required"`
- Writer produces a 1,000+ word markdown report
- Email Agent converts the report to HTML and sends it, or falls back to a push notification when `USE_EMAIL = False`
- The whole run is wrapped in a single `trace()` for end-to-end visibility

> 💸 `WebSearchTool` is billed per call (about 1¢). Tune `HOW_MANY_SEARCHES` to control cost.

---

## 🗂️ Repository structure

```
AgentVerse/
├── OpenAISDK/
│   ├── SalesAgent.ipynb          # Multi-agent cold email workflow
│   ├── SalesAgent-ReadMe.txt
│   ├── Askmycv.ipynb             # CV chatbot with Gradio UI
│   ├── Askmycv-ReadMe.txt
│   ├── DeepResearch.ipynb        # Plan → search → report → email
│   └── messenger.py              # send_email / push helpers
├── me/
│   ├── linkedin.pdf              # Knowledge base for AskMyCV
│   └── summary.txt
└── .gitignore
```

---

## 🚀 Quick start

**1. Clone and set up an environment**

```bash
git clone https://github.com/Neine/AgentVerse.git
cd AgentVerse
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
```

**2. Install dependencies**

```bash
pip install openai openai-agents python-dotenv sendgrid requests pypdf gradio jupyter
```

**3. Add a `.env` file in the repo root**

```env
OPENAI_API_KEY=your_openai_api_key

# SalesAgent
SENDGRID_API_KEY=your_sendgrid_api_key

# AskMyCV
PUSHOVER_USER=your_pushover_user_key
PUSHOVER_TOKEN=your_pushover_api_token
```

**4. Open a notebook and run top to bottom**

```bash
jupyter lab
```

> **Note:** the notebooks use top-level `await`, so run them in Jupyter, JupyterLab or VS Code.

---

## ✅ Prerequisites

- Python 3.10+
- OpenAI API key (`gpt-4o-mini`; DeepResearch uses `gpt-5.4-mini`)
- SendGrid account with a verified sender (SalesAgent, DeepResearch)
- Pushover account (AskMyCV, DeepResearch fallback)

---

## 🛠️ Tech stack

`Python` · `OpenAI Agents SDK` · `Pydantic` · `asyncio` · `Jupyter` · `SendGrid` · `Gradio` · `Pushover` · `pypdf` · `python-dotenv`

---

## 🗺️ Roadmap

More agent projects are on the way. Watch or ⭐ the repo to follow along.

---

## 👤 Author

**Neine Arora**

[GitHub](https://github.com/Neine)

> Built while learning agentic AI, one agent at a time.
