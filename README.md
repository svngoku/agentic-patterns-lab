# Agentic Patterns Lab

This repository provides a **production‑ready monorepo** for building, testing and scaling agentic systems.  It follows the principle that the agent’s context – its prompts, memory, tools and business data – should be treated as a single managed asset rather than scattered across helper scripts.  Recent thinking on **context engineering** emphasises that an AI agent needs more than a prompt; it requires a carefully curated information environment to reason reliably【583963753334775†L36-L47】.  This monorepo brings those ideas together with the **Model Context Protocol (MCP)**, **Agentic Retrieval‑Augmented Generation (RAG)**, and domain‑specific finance agents.

The layout is package‑first, allowing Python, TypeScript/Node and infrastructure code to coexist while sharing a single CI pipeline.  Each package exposes a typed interface so that agents can call each other over MCP without leaking implementation details.

## Repository layout

```
agentic-patterns-lab/
├── docs/                    # MkDocs content, diagrams, architectural decision records
├── examples/                # End‑to‑end demos demonstrating how packages work together
├── packages/                # Python/TS modules organised by domain
│   ├── context_engineering/ # Context engineering helpers and memory stores
│   ├── mcp_patterns/        # Reusable MCP servers, clients and orchestrator patterns
│   ├── agentic_rag/         # Ingest, Retrieve, Reason, Evaluate pipelines
│   ├── finance/             # Domain‑specific finance agents and backtests
│   ├── ui/                  # React/Agno front‑ends (placeholder)
│   └── tooling/             # CodeAct executors, Deep‑Agent planners and other shared tools
├── infra/                   # Dockerfiles, dev‑containers and infrastructure‑as‑code templates
├── tests/                   # Pytest and contract tests
├── scripts/                 # One‑shot setup & data‑seeding helpers
├── pyproject.toml           # Python project metadata and dependencies
└── package.json             # Node workspaces for TypeScript packages
```

### Context engineering layer

The `packages/context_engineering` module implements the 12‑Factor Agent checklist described by Kubiya’s **context engineering** guide【583963753334775†L36-L47】.  It exposes adapters for normalising inputs (e.g. Slack messages or REST requests) into a canonical JSON schema, **guards** that enforce required fields before any LLM call, and simple in‑memory or vector‑store backends for long‑term knowledge.  A prompt library sits under `prompts/` to store ReAct‑style templates【46423354257222†L49-L67】.

### Model Context Protocol (MCP) patterns

The `packages/mcp_patterns` package contains reusable servers, clients and orchestrators based on the Model Context Protocol.  MCP turns context assets into plug‑and‑play services: servers expose **resources** (read‑only data) and **tools** (functions that produce side‑effects) using a standard schema【358028013772867†L60-L70】.  Clients can discover and invoke tools over standard transports, ensuring that new capabilities can be added without rewriting agent logic.  This package also includes pattern implementations such as **Task Decomposition**, **Swarm** and **Supervisor/Worker** to orchestrate multi‑agent workflows【837657169318762†L152-L170】.

### Agentic RAG module

Traditional RAG pipelines retrieve documents once and feed them to the model.  **Agentic RAG** embeds planning and reflection into the loop: agents iteratively query their knowledge store, think, act and re‑query until the goal is achieved【943668547875319†L64-L79】.  The `packages/agentic_rag` package contains submodules for ingestion (loaders, chunkers and embeddings), planning (wrappers around Deep‑Agent or Semantic‑Kernel), retrieval (vector and graph search), reasoning (ReAct‑style loops【46423354257222†L49-L67】) and evaluation (factuality and source‑coverage checks).  A sample playbook under `playbooks/finance_rag.yml` shows how to wire a pipeline to live market data.

### Finance agents

The `packages/finance` module demonstrates how to apply agentic patterns in a highly regulated, latency‑sensitive domain.  Standalone LLMs struggle with real‑time finance problems because of temporal latency and numerical processing constraints; researchers therefore combine language models with tool invocation and deterministic code to process dynamic market data【530182088570403†L1863-L1868】.  Following guidance from the Cambridge Centre for Alternative Finance【704584711066887†L449-L470】, this module includes:

- **Portfolio Copilot:** Monitors prices, retrieves filings and drafts rebalance suggestions.
- **Compliance Checker:** Scans generated trade rationales against policy rules before execution.
- **Backtester:** Runs notebooks inside a sandboxed executor (see `packages/tooling/code_exec.py`) and feeds results back into the agent’s reflection loop.

### Shared tooling

Reusable tooling lives under `packages/tooling`.  These utilities wrap open‑source projects like Deep‑Agent for hierarchical planning, CodeAct for safe code execution, Agno for minimal UIs, and the MCP SDK for declaring tool schemas.  By centralising these wrappers, agents remain SOLID and testable.  Additional helper modules (e.g. `deep_agent_wrapper.py`) make it easy to swap planners without touching business logic.

### DevOps and quality gates

The monorepo is designed with CI/CD in mind.  A typical pipeline runs linting, unit tests, contract (JSON schema) tests and end‑to‑end simulations.  Contributors must adhere to performance budgets (e.g. <2 s latency for hot paths and <8 K tokens per context window) and security requirements such as a tool‑execution sandbox.  The finance module also includes a risk assessment document under `docs/finance/risk.md`, summarising governance concerns from academic research【704584711066887†L468-L553】.

## Getting started

1. Ensure you have Python 3.10+ and Node.js 18+ installed.
2. Clone the repository and install Python dependencies:

   ```bash
   pip install -e .[dev]
   ```

3. Install Node dependencies and build any TypeScript packages:

   ```bash
   cd packages/ui
   npm install
   npm run build
   ```

4. See the `examples/` folder for an end‑to‑end demo combining context engineering, MCP and Agentic RAG.  Run tests with `pytest` and use `ruff`/`mypy` for static analysis.

5. Contribute using the provided templates in `CONTRIBUTING.md` to stay aligned with the architecture.
