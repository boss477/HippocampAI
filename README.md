# HippocampAI — Agentic Memory Engine

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/pypi/pyversions/hippocampai.svg)](https://pypi.org/project/hippocampai/)
[![Documentation](https://img.shields.io/badge/docs-comprehensive-brightgreen.svg)](docs/)

**🏆 1st Runner-Up — SanDisk Hackathon, Vellore (₹50,000 prize)**

Top 50 out of **1000+ teams** across VIT Vellore, VIT Chennai, VIT AP, and VIT Mauritius.

---

## About This Project

**HippocampAI** is an agentic memory system that gives AI assistants persistent, human-like recall across sessions — inspired by how the brain's hippocampus stores and retrieves experience.

We built and extended this platform for the **SanDisk Hackathon in Vellore**, designing an **agentic AI memory layer** with a **portable BrainVault** — encrypted memory that lives on USB storage, survives device changes, and restores into any running HippocampAI instance.

> **Research foundation:** Our hackathon solution was shaped by [*The AI Hippocampus: How Far are We From Human Memory?*](https://arxiv.org/abs/2601.09113) (Jia et al., 2026) — a survey on implicit, explicit, and **agentic memory** paradigms in LLMs and multimodal systems.

This repo is our customized implementation and demo codebase — the [HippocampAI](https://github.com/boss477/HippocampAI) agentic memory engine we built for the SanDisk Hackathon.

---

## Hackathon Highlights

| | |
|---|---|
| **Event** | SanDisk Hackathon — Vellore |
| **Result** | 🥈 1st Runner-Up (₹50k) |
| **Scale** | 1000+ teams → Top 50 → Final placement |
| **Theme** | Agentic AI + portable memory on external storage |
| **Approach** | Research paper → architecture design → full-stack demo under time pressure |

### Team

| Name |
|---|
| **Fawaz** |
| Vishvadharman Saminathan |
| Gokul R |
| Nadia Naureen |

**Thanks to:** Sandisk for hosting the event, and mentors/judges Venkatesh Duraisamy, Shreyas Budgur, Ramanathan Muthiah, Dinesh Khanna, and Lokesh Babu for their guidance.

---

## Our Custom Features (SanDisk / BrainVault)

These are the hackathon-specific modules we built on top of HippocampAI — the core differentiators for portable, agentic memory.

### 🧠 BrainVault — Encrypted USB Memory Vault

| Feature | Description |
|---------|-------------|
| **USB memory export** | Pull all user/session memories from the HippocampAI API and write them to a SanDisk USB drive as timestamped `.enc` files |
| **Fernet encryption** | Password-derived SHA-256 key encryption — memories are never stored in plain text on the drive |
| **USB memory restore** | Auto-detect the latest export on the drive, decrypt, and re-ingest memories via `memories:remember` |
| **Standalone decrypt tool** | Inspect or verify encrypted exports offline without running the full stack |
| **BrainVault folder layout** | Organized `exports/`, `backups/`, and `uploads/` paths mounted into the API container |

```
HippocampAI API  ──export──▶  Fernet encrypt  ──write──▶  USB (BrainVault/exports/*.enc)
USB (BrainVault) ──decrypt──▶  JSON memories  ──restore──▶  HippocampAI API
```

### 🤖 Agentic Chat Pipeline (`brain_flow`)

| Feature | Description |
|---------|-------------|
| **Dual-layer memory** | Redis for short-term chat context (last 20 turns) + HippocampAI for long-term vector memory |
| **Smart remember gate** | Agent decides what to store — skips questions, stores statements with project/AI/USB keywords |
| **Relevance-filtered recall** | Only injects memories above a similarity threshold; filters out question-shaped noise |
| **Context assembly** | Combines recent chat + recalled long-term memories into a structured LLM prompt |
| **Local LLM support** | Runs against LM Studio (`qwen3.5-9b`) for fully offline inference |
| **Importance scoring** | Long-term facts stored with importance=7, 365-day TTL, tagged `chat` + `memory` |

### 💬 Brain Chat UI (`chat_ui`)

| Feature | Description |
|---------|-------------|
| **Gradio web interface** | Live chat with memory panel showing which long-term memories were used |
| **One-click USB export** | Export encrypted memory vault directly from the chat UI |
| **USB health dashboard** | Live drive stats + write-speed graph inside the same interface |
| **Session persistence** | Redis-backed conversation history across turns |

### 📊 USB SmartMetrics (`usb_health` / `performance`)

| Feature | Description |
|---------|-------------|
| **Real-time write speed** | Windows PDH counters for live MB/s throughput during memory export |
| **Thermal throttling detection** | Flags when peak speed drops >60% — possible drive overheating |
| **SLC cache exhaustion alert** | Detects sustained write slowdown after burst speeds >80 MB/s |
| **Performance drop warnings** | Abnormal speed drops during active writes |
| **Drive capacity monitor** | Free/total GB and usage % for the USB volume |
| **Live matplotlib graph** | Write speed, average, and peak plotted over time in the Gradio UI |
| **CLI monitor mode** | Standalone terminal dashboard for debugging export performance |

### 📚 Knowledge Import (`import_books_to_hippocamp`)

| Feature | Description |
|---------|-------------|
| **Bulk book ingestion** | Pre-load structured book summaries (Think and Grow Rich, Rich Dad Poor Dad, etc.) as tagged long-term memories |
| **Rich tagging** | Per-book, per-chapter tags for precise recall filtering |
| **High importance + long TTL** | Book facts stored at importance=8 with 10-year retention |

---

## Platform Features (HippocampAI Engine)

### Core Memory Operations

| Feature | Description |
|---------|-------------|
| **remember()** | Store facts, preferences, goals, habits, events with tags, TTL, and importance |
| **recall()** | Hybrid semantic search — vector + BM25 + cross-encoder reranking |
| **get_memories()** | List/filter memories by type, tags, importance, date range |
| **update_memory()** | In-place text, tag, metadata, and importance updates |
| **delete_memory()** | Single or batch delete with user verification |
| **Batch add/delete** | High-throughput bulk operations for migration |
| **Memory TTL** | Auto-expire memories after configurable days |
| **Access tracking** | Track how often each memory is recalled and injected |

### Hybrid Retrieval & Search

| Feature | Description |
|---------|-------------|
| **Vector search** | Qdrant HNSW index with BGE-small embeddings (384-dim) |
| **BM25 keyword search** | Lexical matching for exact term recall |
| **Cross-encoder reranking** | ms-marco-MiniLM re-scores top candidates |
| **Reciprocal Rank Fusion (RRF)** | Combines vector + BM25 + graph scores |
| **Graph-aware retrieval** | 3-way fusion adds knowledge-graph entity matches |
| **Redis query cache** | 50–100× faster repeat queries (1–2 ms cached) |
| **Temporal queries** | Time-range filtering and recency-weighted scoring |
| **Advanced filters** | Metadata, custom fields, access count, importance thresholds |

### Knowledge Graph

| Feature | Description |
|---------|-------------|
| **Real-time extraction** | Entity/relationship extraction on every `remember()` call |
| **In-memory graph + JSON persistence** | Fast traversal with disk backup |
| **Relationship mapping** | Link memories by shared entities (people, places, projects) |
| **Graph traversal retrieval** | Multi-hop recall via related entities (depth 1–2) |
| **Memory clusters** | Detect densely connected memory groups |
| **Graph view UI** | Interactive visualization in the React dashboard |

### Intelligence & NLP

| Feature | Description |
|---------|-------------|
| **Fact extraction** | Pull structured facts from unstructured conversation text |
| **Entity recognition** | Identify people, orgs, locations, dates from memory text |
| **Semantic clustering** | Group related memories into topic clusters |
| **Conflict resolution** | Detect and merge contradictory memories |
| **Auto-summarization** | Generate session/conversation summaries |
| **Importance decay** | Memories fade over time unless re-accessed |
| **Smart updater** | Automatically refine memories as new info arrives |
| **Agentic classifier** | LLM-powered memory type and relevance classification |
| **Predictive analytics** | Forecast memory usage patterns and user behavior |
| **Cross-session insights** | Detect habits, behavior changes, and recurring themes |

### Agentic & Multi-Agent Memory

| Feature | Description |
|---------|-------------|
| **Procedural memory** | Self-optimizing prompt rules learned from user behavior |
| **Memory triggers** | Event-driven webhooks, WebSocket pushes, and log actions |
| **Multi-agent collaboration** | Shared, private, and public memory visibility per agent |
| **Memory namespaces** | Hierarchical organization with per-namespace permissions |
| **Context assembly** | Token-budgeted context packs for LLM injection |
| **Relevance feedback loop** | User rates recall quality; scores decay exponentially over 90 days |
| **Offline mode** | Queue remember/recall ops when backend is unreachable |
| **Plugin system** | Custom processors, scorers, retrievers, and filters |

### Memory Lifecycle & Quality

| Feature | Description |
|---------|-------------|
| **Sleep phase consolidation** | Nightly compaction — merge duplicates, prune low-value memories |
| **Auto-deduplication** | Scheduled duplicate detection and merging |
| **Auto-healing** | Detect and repair corrupted or orphaned memory records |
| **Memory health monitoring** | Quality scores, staleness alerts, coverage metrics |
| **Version control** | Full memory edit history with rollback |
| **Audit trail** | Who changed what, when — compliance-ready logging |
| **Bi-temporal facts** | Valid-from / valid-to time travel queries |
| **Custom schemas** | Define entity types and validate without code changes |
| **Retention policies** | Configurable per-type TTL and archival rules |
| **Tiered storage** | Hot / warm / cold tiers for cost-efficient scaling |

### Portability & Backup

| Feature | Description |
|---------|-------------|
| **JSON / JSONL export** | Standard portable backup formats |
| **Parquet / CSV export** | Analytics-friendly bulk export |
| **Import pipeline** | Restore from any supported export format |
| **Gzip compression** | Optional compressed archives |
| **Embedding migration** | Safe vector model upgrades via Celery background jobs |

### LLM & Framework Integrations

| Provider / Framework | Support |
|---------------------|---------|
| **Groq** | llama-3.1-8b-instant (default cloud) |
| **Ollama** | Local models (qwen2.5, etc.) |
| **OpenAI** | GPT-4 / GPT-3.5 |
| **Anthropic** | Claude |
| **LM Studio** | Local OpenAI-compatible API (our hackathon setup) |
| **LangChain** | Memory adapter |
| **LlamaIndex** | Memory adapter |

---

## React Dashboard

Full-featured web UI with 20+ pages:

| Page | What it does |
|------|-------------|
| **Dashboard** | Memory stats, recent activity, system health overview |
| **Memories** | Browse, search, filter, edit, and delete memories |
| **Graph View** | Interactive knowledge graph visualization |
| **Timeline** | Chronological memory history |
| **Bi-Temporal** | Time-travel fact queries with validity periods |
| **Cluster** | Semantic memory clusters |
| **Analytics** | Usage patterns, growth charts, heatmaps |
| **Heatmap** | Memory activity density over time |
| **Health** | Memory quality scores and issue detection |
| **Hygiene** | Duplicate detection and cleanup tools |
| **Sleep Phase** | Consolidation job control and dry-run preview |
| **Triggers** | Configure event-driven memory actions |
| **Procedural Memory** | View and edit learned behavioral rules |
| **Feedback** | Recall relevance ratings and score trends |
|
---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        User Interfaces                          │
│   Gradio Brain Chat  │  React Dashboard  │  REST / WebSocket API │
└──────────────┬──────────────────┬───────────────────────────────┘
               │                  │
               ▼                  ▼
┌──────────────────────┐  ┌──────────────────────────────────────┐
│  Agentic Pipeline    │  │         HippocampAI Engine           │
│  brain_flow          │  │                                      │
│  ├─ Redis (short)    │  │  FastAPI ──▶ Hybrid Retriever        │
│  ├─ Recall filter    │  │     │         ├─ Qdrant (vectors)   │
│  ├─ Remember gate    │  │     │         ├─ BM25 (keywords)     │
│  └─ LM Studio LLM    │  │     │         ├─ Reranker            │
└──────────┬───────────┘  │     │         └─ Knowledge Graph     │
           │              │     ├──▶ Celery (async tasks)         │
           │              │     ├──▶ Redis (cache)                │
           │              │     └──▶ PostgreSQL (auth)           │
           │              └──────────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────────────────────────┐
│                    BrainVault (SanDisk USB)                     │
│   export_to_usb ──▶ Fernet .enc ──▶ restore_from_usb           │
│   usb_health ──▶ write speed / thermal / capacity monitor      │
└─────────────────────────────────────────────────────────────────┘
```

Inspired by the **agentic memory** paradigm from the [AI Hippocampus survey](https://arxiv.org/abs/2601.09113): persistent, temporally extended memory structures that enable long-term planning, self-consistency, and personalized inference across devices.

[Full architecture docs →](docs/ARCHITECTURE.md)

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
docker run -d -p 6333:6333 qdrant/qdrant
cp .env.example .env
pip install -e ".[saas]"
uvicorn hippocampai.api.app:socket_app --host 0.0.0.0 --port 8000
```

### React dashboard

```bash
cd frontend && npm install && npm run dev   # http://localhost:5173
```

### Brain Chat UI (Gradio — local tooling)

```bash
pip install gradio redis cryptography matplotlib psutil pywin32
python chat_ui.py   # launches Brain Chat with USB export + health panel
```

### Export memory to USB

```bash
python export_to_usb.py      # encrypts and writes to BrainVault/exports/
python restore_from_usb.py   # decrypts latest .enc and restores to API
python decrypt_usb_memory.py # inspect an encrypted export offline
python usb_health.py         # check USB write speed and drive health
```

---

## Tech Stack

| Layer | Technologies |
|-------|-------------|
| **Backend** | Python 3.11, FastAPI, Celery, Pydantic, Uvicorn |
| **Memory store** | Qdrant (vectors), Redis (cache + chat), PostgreSQL (auth) |
| **Frontend** | React 18, TypeScript, Tailwind CSS, Vite |
| **Chat UI** | Gradio, LM Studio (local LLM) |
| **Encryption** | Fernet (cryptography), SHA-256 password-derived keys |
| **USB monitoring** | psutil, win32pdh, matplotlib |
| **LLM providers** | Groq, Ollama, OpenAI, Anthropic, LM Studio |
| **Embeddings** | BGE-small-en-v1.5, cross-encoder reranking |
| **Monitoring** | Prometheus, Grafana, Flower |

---

## Documentation

| Goal | Link |
|------|------|
| Get started in 5 min | [Getting Started](docs/GETTING_STARTED.md) |
| All 102+ API methods | [API Reference](docs/API_REFERENCE.md) |
| Full feature deep-dive | [Features Guide](docs/FEATURES.md) |
| Configuration options | [Configuration](docs/CONFIGURATION.md) |
| SaaS deployment | [SaaS Guide](docs/SAAS_GUIDE.md) |
| Full doc index (26 docs) | [Documentation Hub](docs/README.md) |

---

## Examples

25+ working demos in [`examples/`](examples/):

```bash
python examples/01_basic_usage.py
python examples/11_intelligence_features_demo.py
python examples/12_multiagent_demo.py
python examples/20_collaboration_demo.py
python examples/consolidation_demo.py
```

---

## Research Reference

> **Jia, Z., et al.** (2026). *The AI Hippocampus: How Far are We From Human Memory?*
> arXiv:2601.09113 — [https://arxiv.org/abs/2601.09113](https://arxiv.org/abs/2601.09113)

This paper surveys three memory frameworks in modern AI:

1. **Implicit memory** — knowledge embedded in model weights
2. **Explicit memory** — external vector/graph stores (what HippocampAI implements)
3. **Agentic memory** — persistent, temporally extended structures for autonomous agents

Our hackathon solution focused on bridging **explicit** and **agentic** memory — giving agents durable, encrypted, portable recall that survives across sessions, devices, and USB drives.

---

## Use Cases

- **Personal AI assistants** — remember preferences and context across conversations and devices
- **Portable edge AI** — export encrypted memory to USB, plug into any machine, restore instantly
- **Offline-first agents** — LM Studio + local HippocampAI + Redis with no cloud dependency
- **Multi-agent systems** — shared memory spaces for coordinated agent teams
- **Knowledge bases** — bulk-import structured content (books, docs) as searchable long-term memory
- **Customer support bots** — retain interaction history and user-specific details

---

## License

**Apache 2.0** — built on the open-source HippocampAI project. Free for commercial and personal use.

---

**Built at the SanDisk Hackathon, Vellore 🚀**

*Sometimes you're just destined to win in a different place.*
