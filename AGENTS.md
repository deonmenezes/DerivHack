# DerivHack — AI Company Multi-Agent System

A local multi-agent AI company built with Google Agent Development Kit (ADK) and Hugging Face models (free tier). Provides 6 specialist AI agents accessible via a FastAPI server or interactive CLI, with local vector memory via ChromaDB.

## Tech Stack

- **Language:** Python 3.9+
- **Agent Framework:** Google ADK (`google-adk`)
- **LLM Provider:** Hugging Face Hub (free tier)
- **LLM Routing:** LiteLLM
- **API Server:** FastAPI + Uvicorn
- **Vector Memory:** ChromaDB
- **Data Processing:** Pandas, NumPy
- **Visualization:** Matplotlib, Seaborn, Plotly
- **Testing:** Pytest + pytest-asyncio

## Setup

```bash
# Run the automated setup script
bash setup.sh

# Or manually:
python3 -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.template .env      # then edit .env and add HUGGINGFACE_API_KEY
```

Required environment variable (in `.env`):
```
HUGGINGFACE_API_KEY=hf_your_token_here
```

Get a free token at: https://huggingface.co/settings/tokens

Create required directories:
```bash
mkdir -p data/memory agent_workspace/pending_approval agent_workspace/approved
```

## Build / Run / Test

```bash
# Start FastAPI API server
python api_server.py

# Interactive CLI test mode
python test_local.py

# Run tests
pytest

# Lint
flake8
black .
```

## Project Structure

```
DerivHack/
├── api_server.py          # FastAPI server exposing agent endpoints
├── test_local.py          # Interactive CLI for local testing
├── requirements.txt       # Python dependencies
├── setup.sh               # Automated setup script
├── src/
│   ├── agents.py          # Agent definitions (HR, AI Eng, Data, PMO, Security, DevOps)
│   ├── llm_config.py      # LLM provider configuration (HuggingFace via LiteLLM)
│   ├── memory_manager.py  # ChromaDB vector memory management
│   ├── mcp_tools.py       # MCP tool integrations
│   ├── emergency.py       # Emergency/fallback handling
│   └── adk/               # Google ADK session/runtime helpers
├── data/
│   └── memory/            # ChromaDB persistent storage (auto-created)
└── agent_workspace/
    ├── pending_approval/   # AI-generated code awaiting human approval
    └── approved/           # Approved code artifacts
```

## Architecture & Key Files

- `src/agents.py` — defines all 6 specialist agents; entry point for adding/modifying agent capabilities
- `src/llm_config.py` — configures the Hugging Face model provider via LiteLLM; change models here
- `src/memory_manager.py` — ChromaDB vector store for conversation history and context retrieval
- `api_server.py` — FastAPI routes; each agent has its own endpoint
- Code generation uses an approval workflow: generated code lands in `agent_workspace/pending_approval/` and must be reviewed before moving to `approved/`

## Conventions & Notes for Agents

- **Approval workflow:** Never skip the `pending_approval` → `approved` step for AI-generated code — it is a core safety feature.
- **100% free:** All LLM calls go through Hugging Face free tier; do not introduce paid API dependencies without flagging it.
- **Async:** FastAPI routes and ADK sessions use async/await throughout; keep new code async-compatible.
- **Environment secrets** live in `.env` (gitignored); never hardcode API keys.
- `aiosqlite` is used for ADK session persistence; do not swap to a sync DB driver.
- No frontend — this is a pure Python backend / API project.
