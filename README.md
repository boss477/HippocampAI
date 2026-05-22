# HippocampAI — Agentic Memory Engine

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/pypi/pyversions/hippocampai.svg)](https://pypi.org/project/hippocampai/)
[![Documentation](https://img.shields.io/badge/docs-comprehensive-brightgreen.svg)](docs/)

**🏆 1st Runner-Up — SanDisk Hackathon, Vellore (₹50,000 prize)**

Top 50 out of **1000+ teams** across VIT Vellore, VIT Chennai, VIT AP, and VIT Mauritius.

---

## About This Project

**HippocampAI** is an agentic memory system that gives AI assistants persistent, human-like recall across sessions — inspired by how the brain's hippocampus stores and retrieves experience.

We built and extended this platform for the **SanDisk Hackathon in Vellore**, designing an **agentic AI memory layer** that can store, retrieve, and reason over long-term user context — with a focus on portable, durable memory workflows suited to edge and offline use cases.

> **Research foundation:** Our hackathon solution was shaped by [*The AI Hippocampus: How Far are We From Human Memory?*](https://arxiv.org/abs/2601.09113) (Jia et al., 2026) — a survey on implicit, explicit, and **agentic memory** paradigms in LLMs and multimodal systems.

This repo is our customized implementation and demo codebase, built on top of the open-source [HippocampAI](https://pypi.org/project/hippocampai/) memory engine.

---

## Hackathon Highlights

| | |
|---|---|
| **Event** | SanDisk Hackathon — Vellore |
| **Result** | 🥈 1st Runner-Up (₹50k) |
| **Scale** | 1000+ teams → Top 50 → Final placement |
| **Theme** | Agentic AI + persistent memory for intelligent systems |
| **Approach** | Research-driven design → rapid prototype → working demo under time pressure |

### What we built

- **Long-term agent memory** — facts, preferences, and session context stored in a vector database with hybrid retrieval
- **Agentic recall pipeline** — the agent decides what to remember, when to recall, and how to inject context into LLM responses
- **Portable memory workflows** — export, backup, and restore user memory (aligned with SanDisk's storage-first vision)
- **Full-stack demo** — FastAPI backend, React dashboard, Redis caching, and Qdrant vector search

### Team

| Name | |
|---|---|
| **Fawaz** | *(you — repo owner)* |
| Vishvadharman Saminathan | |
| Gokul R | |
| Nadia Naureen | |

**Thanks to:** Sandisk for hosting the event, and mentors/judges Venkatesh Duraisamy, Shreyas Budgur, Ramanathan Muthiah, Dinesh Khanna, and Lokesh Babu for their guidance.

---

## Quick Start

### Install

```bash
pip install "hippocampai[saas]"
```

### Store & recall a memory (30 seconds)

```python
from hippocampai import MemoryClient

client = MemoryClient()

client.remember(
    "I prefer working remotely on Tuesdays and drink oat milk coffee",
    user_id="alice",
    type="preference",
)

results = client.recall("work preferences", user_id="alice")
print(results[0].memory.text)
```

### Run locally

```bash
# Start Qdrant
docker run -d -p 6333:6333 qdrant/qdrant

# Copy env template and configure your LLM provider
cp .env.example .env

# Install and run API
pip install -e ".[saas]"
uvicorn hippocampai.api.app:socket_app --host 0.0.0.0 --port 8000
```

### React dashboard

```bash
cd frontend
npm install
npm run dev   # http://localhost:5173
```

---

## Key Features

| Feature | Description |
|---------|-------------|
| **Hybrid retrieval** | Vector + BM25 + reranking for accurate recall |
| **Knowledge graph** | Real-time entity/relationship extraction on every `remember()` |
| **Graph-aware search** | 3-way RRF fusion: vector + BM25 + graph |
| **Session management** | Conversation tracking, summaries, hierarchical sessions |
| **Procedural memory** | Self-optimizing prompts via learned behavioral rules |
| **Memory triggers** | Event-driven webhooks and websocket actions |
| **Multi-agent collaboration** | Shared memory spaces for agent coordination |
| **Bi-temporal facts** | Time-travel queries with validity periods |
| **React dashboard** | Analytics, memory browser, graph view, and more |

[Full feature list →](docs/FEATURES.md)

---

## Architecture

```
User / Agent
     │
     ▼
┌─────────────┐     ┌──────────┐     ┌─────────┐
│  FastAPI    │────▶│  Redis   │     │ Qdrant  │
│  Memory API │     │  Cache   │     │ Vectors │
└─────────────┘     └──────────┘     └─────────┘
     │
     ▼
┌─────────────┐     ┌──────────────┐
│ LLM Provider│     │ Knowledge    │
│ Groq/Ollama │     │ Graph        │
└─────────────┘     └──────────────┘
```

Inspired by the **agentic memory** paradigm from the [AI Hippocampus survey](https://arxiv.org/abs/2601.09113): persistent, temporally extended memory structures that enable long-term planning, self-consistency, and personalized inference.

[Architecture docs →](docs/ARCHITECTURE.md)

---

## Tech Stack

| Layer | Technologies |
|-------|-------------|
| **Backend** | Python, FastAPI, Celery, Pydantic |
| **Memory store** | Qdrant (vectors), Redis (cache), PostgreSQL (auth) |
| **Frontend** | React, TypeScript, Tailwind CSS, Vite |
| **LLM providers** | Groq, Ollama, OpenAI, Anthropic |
| **Embeddings** | BGE-small, cross-encoder reranking |

---

## Documentation

| Goal | Link |
|------|------|
| Get started in 5 min | [Getting Started](docs/GETTING_STARTED.md) |
| All API methods | [API Reference](docs/API_REFERENCE.md) |
| Feature deep-dive | [Features Guide](docs/FEATURES.md) |
| Configuration | [Configuration](docs/CONFIGURATION.md) |
| Full doc index | [Documentation Hub](docs/README.md) |

---

## Examples

25+ working demos in [`examples/`](examples/):

```bash
python examples/01_basic_usage.py
python examples/11_intelligence_features_demo.py
python examples/12_multiagent_demo.py
python examples/20_collaboration_demo.py
```

---

## Research Reference

> **Jia, Z., et al.** (2026). *The AI Hippocampus: How Far are We From Human Memory?*
> arXiv:2601.09113 — [https://arxiv.org/abs/2601.09113](https://arxiv.org/abs/2601.09113)

This paper surveys three memory frameworks in modern AI:

1. **Implicit memory** — knowledge embedded in model weights
2. **Explicit memory** — external vector/graph stores (what HippocampAI implements)
3. **Agentic memory** — persistent, temporally extended structures for autonomous agents

Our hackathon solution focused on bridging **explicit** and **agentic** memory — giving agents durable, queryable recall that survives across sessions and devices.

---

## Use Cases

- **Personal AI assistants** — remember preferences, habits, and context across conversations
- **Customer support bots** — retain interaction history and user-specific details
- **Multi-agent systems** — shared memory spaces for coordinated agent teams
- **Portable / edge AI** — export and restore memory for offline or device-local workflows

---

## License

**Apache 2.0** — built on the open-source HippocampAI project. Free for commercial and personal use.

---

**Built at the SanDisk Hackathon, Vellore 🚀**

*Sometimes you're just destined to win in a different place.*
