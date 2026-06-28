Askmycv.ipynb — ReadMe
======================

📋 OVERVIEW
-----------
Askmycv.ipynb builds a personal "ask my CV" chatbot. It uses the OpenAI
Agents SDK to create an agent that impersonates a specific person, answering
visitor questions about their career, background, skills, and experience.
The agent's knowledge comes from a LinkedIn PDF export and a short written
summary. A Gradio chat UI serves as the front end, and Pushover push
notifications alert the owner whenever a visitor leaves contact details or
asks something the agent can't answer.


🎓 WHAT YOU WILL LEARN
----------------------
The notebook walks through these pieces:

  1. Loading a knowledge base from a PDF (pypdf) and a plain text summary
  2. Building a system prompt that injects a persona + knowledge base into
     agent instructions
  3. Function tools (record_user_details, record_unknown_question) for
     capturing visitor info and unanswered questions
  4. Sending push notifications via the Pushover API
  5. Running an agent with the OpenAI Agents SDK Runner
  6. Serving the agent as a chatbot with Gradio's ChatInterface


✅ PREREQUISITES
----------------
  - Python 3.10+ recommended
  - A Jupyter environment that supports top-level await (Jupyter Notebook,
    JupyterLab, or VS Code)
  - An OpenAI API key with access to gpt-4o-mini
  - A Pushover account (user key + API token) for notifications
  - A LinkedIn PDF export and a short text summary describing yourself


📦 REQUIRED PACKAGES
--------------------
Install dependencies before running the notebook:

  pip install python-dotenv requests pypdf gradio openai-agents

Note: The notebook imports from the `agents` package (OpenAI Agents SDK).


🔐 ENVIRONMENT SETUP
--------------------
Create a `.env` file in the repository root (one level above this notebook,
which lives in Askmycv/) with the following variables:

  OPENAI_API_KEY=your_openai_api_key_here
  PUSHOVER_USER=your_pushover_user_key_here
  PUSHOVER_TOKEN=your_pushover_api_token_here

The notebook loads these with python-dotenv. Do not commit `.env` to version
control (it is listed in .gitignore).


🧠 KNOWLEDGE BASE SETUP
------------------------
The notebook lives in Askmycv/ and loads its knowledge base from
me/ (one level up), using relative paths "../me/linkedin.pdf" and
"../me/summary.txt". Place these files there:

  me/linkedin.pdf    Your exported LinkedIn profile (PDF)
  me/summary.txt     A short plain-text bio/summary about yourself

Also update the `name` variable in the "Load knowledge base" cell (currently
set to "Neine Arora") so it matches the profile you're loading — it's used
throughout the agent's instructions.


📓 NOTEBOOK SECTIONS
---------------------

  Imports
    Load dotenv, requests, pypdf, gradio, pathlib, and disable Agents SDK
    tracing before importing `agents`.

  Set up
    Load environment variables, set the model (gpt-4o-mini), and check that
    Pushover credentials were found.

  Tool Functions
    push(message) — posts a notification to the Pushover API.
    record_user_details (@function_tool) — records a visitor's email, name,
    and notes when they want to be contacted.
    record_unknown_question (@function_tool) — records any question the
    agent couldn't answer.

  Load knowledge base
    Extract text from me/linkedin.pdf with pypdf and read me/summary.txt.

  Creating Agent
    Build career_instructions (persona + summary + LinkedIn text) and
    create the "Career Assistant" agent with both function tools attached.

  Chat function using agent Runner
    async chat(message, history) converts Gradio chat history into the
    message format the Agents SDK expects and runs the agent via
    Runner.run(), returning result.final_output.

  Launch Gradio
    gr.ChatInterface(chat, type="messages").launch() starts a local chat UI
    (default: http://127.0.0.1:7860).


🚀 HOW TO RUN
-------------
  1. Add me/linkedin.pdf and me/summary.txt, and set
     `name` to match.
  2. Open Askmycv.ipynb in your Jupyter environment.
  3. Run the setup cells first to confirm OPENAI_API_KEY and Pushover
     credentials are loaded.
  4. Run the cells top to bottom; later cells depend on earlier definitions
     (tools, knowledge base, agent).
  5. The async chat() function requires a kernel that supports top-level
     await (recent Jupyter/IPython versions do).
  6. Run the final cell to launch Gradio, then open the local URL printed
     in the output to chat with the agent.
  7. Check your Pushover app for notifications when the agent records a
     visitor's contact info or an unanswered question.


🛠️ TOOLS SUMMARY
------------------

  Tool                      Triggered when
  ------------------------  ---------------------------------------------
  record_user_details       A visitor shares an email (and optionally a
                             name/notes) to be contacted later
  record_unknown_question   The agent can't answer a visitor's question


🐛 TROUBLESHOOTING
-------------------
  OpenAI API key not set
    Ensure OPENAI_API_KEY is in .env and load_dotenv(override=True) ran.

  Pushover user/token not found
    Check PUSHOVER_USER and PUSHOVER_TOKEN in .env; both must be set for
    push() to succeed (it will still run but notifications will fail).

  FileNotFoundError on linkedin.pdf or summary.txt
    Confirm both files exist under me/ (one level up from the
    notebook, which lives in Askmycv/).

  ImportError: agents
    Install the OpenAI Agents SDK: pip install openai-agents

  Async errors in Jupyter
    Restart the kernel and run cells in order, or use nest_asyncio if your
    Jupyter/IPython version lacks top-level await support.

  Gradio port already in use
    Another launch() is still running; restart the kernel or pass a
    different `server_port` to launch().


🔗 RELATED FILES
-----------------
  Askmycv/Askmycv.ipynb        Main notebook
  Askmycv/Askmycv-ReadMe.txt   This file
  me/linkedin.pdf              LinkedIn PDF knowledge source (not in repo)
  me/summary.txt               Bio/summary knowledge source (not in repo)
  .env                         Local API keys at repo root (not in repo)
  .gitignore                   Excludes secrets and virtual environments
