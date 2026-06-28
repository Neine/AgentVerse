SalesAgent.ipynb — ReadMe
=========================

OVERVIEW
--------
SalesAgent.ipynb is a hands-on Jupyter notebook that builds a multi-agent cold
email sales workflow for ComplAI, a fictional SaaS company focused on SOC 2
compliance and audit preparation. It uses the OpenAI Agents SDK to generate,
compare, select, format, and send sales emails.


WHAT YOU WILL LEARN
-------------------
The notebook walks through several core agent patterns:

  1. Single-agent email generation (with streaming output)
  2. Parallel agent execution (three writers run at once)
  3. A "picker" agent that chooses the best draft from multiple options
  4. Function tools (SendGrid email sending)
  5. Agents exposed as tools (sales writers wrapped with .as_tool())
  6. A Sales Manager agent that orchestrates draft → select → send
  7. Handoffs (Sales Manager delegates formatting/sending to Email Manager)
  8. Tracing via the OpenAI platform (trace() context manager)


PREREQUISITES
-------------
  - Python 3.10+ recommended
  - A Jupyter environment (Jupyter Notebook, JupyterLab, or VS Code)
  - An OpenAI API key with access to gpt-4o-mini
  - A SendGrid account and API key (for sending test emails)
  - A verified sender email address in SendGrid


REQUIRED PACKAGES
-----------------
Install dependencies before running the notebook:

  pip install openai openai-agents python-dotenv sendgrid

Note: The notebook imports from the `agents` package (OpenAI Agents SDK).


ENVIRONMENT SETUP
-----------------
Create a `.env` file in the repository root (one level above this notebook,
which lives in SalesAgent/) with the following variables:

  OPENAI_API_KEY=your_openai_api_key_here
  SENDGRID_API_KEY=your_sendgrid_api_key_here

The notebook loads these with python-dotenv. Do not commit `.env` to version
control (it is listed in .gitignore).


SENDGRID CONFIGURATION
----------------------
Before running email-sending cells, update the sender and recipient addresses
in these functions to match your verified SendGrid sender:

  - send_test_email()
  - send_email()
  - send_html_email()

Look for the Email(...) and To(...) lines in those cells. Both addresses must
be valid for your SendGrid setup. A successful test send returns HTTP 202.


NOTEBOOK SECTIONS
-----------------

  Set Up
    Load environment variables, verify the OpenAI key, and test SendGrid.

  Agent Workflow
    Define three sales agents with different tones:
      - Professional Sales Agent — formal, serious cold emails
      - Engaging Sales Agent — witty, humorous cold emails
      - Busy Sales Agent — short, concise cold emails

    Run them individually (with streaming), in parallel, and with a picker agent
    that selects the most compelling draft.

  Use of Tools
    Re-create the three sales agents and prepare them for tool-based workflows.

  Tools & Agent Interactions
    Define send_email as a @function_tool that sends plain-text mail via SendGrid.

  Turning Agent into Tool
    Wrap each sales agent with .as_tool() so other agents can call them.

  Sales Manager — Planning Agent
    A manager agent that:
      1. Calls all three sales agent tools to generate drafts
      2. Picks the best email
      3. Sends it with send_email

  Handoff — Agent delegating to another agent
    Add specialized agents for subject lines and HTML conversion, plus
    send_html_email. The Email Manager agent formats and sends the final mail.
    The Sales Manager hands off the winning draft instead of sending directly.

  Automated SDR (final workflow)
    Sales Manager → three writer tools → pick best → handoff to Email Manager
    → subject + HTML + send.


HOW TO RUN
----------
  1. Open SalesAgent.ipynb in your Jupyter environment.
  2. Run the setup cells first to confirm API keys are loaded.
  3. Run send_test_email() once to verify SendGrid works.
  4. Work through the notebook top to bottom; later cells depend on earlier
     definitions (agents, tools, handoffs).
  5. Cells that use async (await, asyncio.gather) require a Jupyter kernel
     that supports top-level await (recent Jupyter / IPython versions do).
  6. After the final Sales Manager run, review traces at:
       https://platform.openai.com/traces
     and check your inbox for the sent email.


AGENTS SUMMARY
--------------

  Agent                    Role
  ---------------------    ------------------------------------------------
  Professional Sales Agent Writes formal ComplAI cold emails
  Engaging Sales Agent     Writes humorous, engaging cold emails
  Busy Sales Agent         Writes brief, direct cold emails
  sales_picker             Chooses the best draft (customer perspective)
  Sales Manager            Orchestrates draft → select → send or handoff
  Email subject writer     Generates an email subject line
  HTML email body converter Converts plain/markdown body to HTML
  Email Manager            Subject + HTML conversion + send (via handoff)


TROUBLESHOOTING
---------------
  OpenAI API key not set
    Ensure OPENAI_API_KEY is in .env and load_dotenv(override=True) ran.

  SendGrid errors (4xx)
    Verify SENDGRID_API_KEY, sender verification, and from/to addresses.

  ImportError: agents
    Install the OpenAI Agents SDK: pip install openai-agents

  Async errors in Jupyter
    Restart the kernel and run cells in order, or use nest_asyncio if needed.

  Empty or duplicate agent state
    Re-run cells that define agents/tools after editing instructions.


RELATED FILES
-------------
  SalesAgent/SalesAgent.ipynb        Main notebook
  SalesAgent/SalesAgent-ReadMe.txt   This file
  .env                                Local API keys at repo root (not in repo)
  .gitignore                          Excludes secrets and virtual environments
