# Autonomous System Observer

A security-focused AI daemon that continuously monitors your server's codebase, detects suspicious changes, and lets you query your system's history through a local LLM — all without sending data to the cloud.

[![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square&logo=python)](https://python.org)
[![LangChain](https://img.shields.io/badge/LangChain-LangGraph-121212?style=flat-square)](https://langchain.com)
[![FAISS](https://img.shields.io/badge/FAISS-Vector_Store-0066CC?style=flat-square)](https://faiss.ai)
[![Local LLM](https://img.shields.io/badge/LLM-Llama_3.2_Local-8B00FF?style=flat-square)](https://github.com/ggerganov/llama.cpp)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)

> **Variant Notice:** This is a security-specialized fork of [AI-CodeCompass](https://github.com/w8explorer/AI-CodeCompass), optimized for autonomous server monitoring on Ubuntu.

---

## What It Does

Autonomous System Observer is a background AI security agent that runs on your server 24/7. It:

- **Watches your filesystem** every 15 minutes using cron and `find`
- **Understands code structure** via AST analysis — knows which *function* changed, not just which *line*
- **Builds semantic memory** of your entire codebase using FAISS vector embeddings
- **Detects anomalies** — new files, modified scripts, permission changes — and flags risks
- **Writes a Security Diary** (`PROJECT_DIGEST.md`) — a structured log of what changed and why it matters
- **Answers questions** about system history, security risks, and code logic via interactive CLI

---

## Architecture

```
Cron / Background Watcher  (runs every 15 min)
  observer_scanner.sh
        |
        | filesystem events
        v
  observer.py  (Main Daemon)
        |
   +-----------+    +------------+
   | ParserAgent|    | RAG Engine |
   | AST +      |    | LangGraph  |
   | Tree-sitter|    |            |
   +-----------+    +------------+
        |                |
        +-------+--------+
                |
        FAISS Vector Store  (Semantic memory of your system)
                |
                v
  Llama 3.2 1B via llama.cpp  (Runs locally, no internet needed)
```

---

## Key Features

### AST-Level "Deep Eyes" Analysis

The `ParserAgent` uses Python's `ast` module and `tree-sitter` to parse code structure:

- **Function-level awareness** — knows which functions were added, modified, or removed
- **Complexity tracking** — identifies changes that may introduce technical debt
- **Import graph analysis** — understands how one piece of code affects another

### Adaptive RAG Intelligence

When you ask a question, the system reasons in multiple steps:

1. **Query Rewriting** — technically refines your question for better retrieval
2. **Document Grading** — scores and filters retrieved code chunks by relevance
3. **Self-Reflection** — validates the answer before returning it (prevents hallucinations)

### Automated Security Digest

After each scan, the observer writes `PROJECT_DIGEST.md` containing:

- What changed since the last scan
- Which files were modified and by how much
- Any anomalies or security concerns detected

### Fully Offline — Air-Gap Ready

All processing runs locally. No API keys, no external network calls. Designed for servers where data privacy is non-negotiable.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Orchestration | LangChain, LangGraph |
| Reasoning | Adaptive RAG (Query Rewrite, Grade, Reflect) |
| Vector Store | FAISS |
| Code Parsing | Tree-sitter, Python `ast` module |
| LLM | Llama 3.2 1B Instruct (via llama.cpp) |
| Background Runner | Cron + Bash |
| Language | Python 3.10+ |

---

## Getting Started

### Prerequisites

- Ubuntu server (tested on Ubuntu 22.04)
- Python 3.10+
- `llama.cpp` with Llama 3.2 1B model
- Cron (standard on all Linux systems)

### Install

```bash
git clone https://github.com/w8explorer/Autonomous-System-Observer.git
cd Autonomous-System-Observer

pip install -r requirements.txt

cp .env.example .env
# Set MODEL_PATH and WATCH_DIR in .env
```

### Run

```bash
# Interactive mode — ask questions about your system
python observer.py --interactive

# One-shot security query
python observer.py --query "Are there any hardcoded credentials in the scripts directory?"

# Trigger a manual scan
python observer.py --scan
```

### Set Up Background Monitoring

```bash
# Add to crontab — scans every 15 minutes automatically
crontab -e

# Paste this line:
*/15 * * * * /bin/bash /path/to/Autonomous-System-Observer/scripts/observer_scanner.sh
```

---

## Project Structure

```
Autonomous-System-Observer/
├── api/                # FastAPI interface (optional, for remote queries)
├── agents/             # LangGraph nodes: parser, retriever, grader, reflector
├── core/               # RAG pipeline, FAISS store, embeddings
├── libs/               # Shared utilities
├── ui/                 # Optional Streamlit dashboard
├── scripts/            # Cron shell scripts
├── observer.py         # Main daemon — interactive + scan modes
├── observer_chat.py    # CLI chat interface
├── engine_adapter.py   # LLM engine abstraction layer
└── launcher.py         # Combined FastAPI + UI startup
```

---

## Example Interactions

```
You: What changed in the last scan?

Observer: In the last scan (14 mins ago), 3 files were modified:
  - scripts/start_observer_brain.sh -- added nohup flags
  - core/rag_pipeline.py -- grader threshold adjusted from 0.6 to 0.8
  - README.md -- documentation update
  No anomalies detected.

You: Are there any security vulnerabilities in the scan script?

Observer: Yes. observer_scan.sh passes output from the `find` command
  directly into the Python parser without sanitization. A file with a
  specially crafted name could trigger command injection. Recommend
  wrapping filenames in quotes and validating input before parsing.
```

---

## Related Projects

- [AI-CodeCompass](https://github.com/w8explorer/AI-CodeCompass) — The parent project: a full developer assistant with Streamlit UI and FastAPI backend

---

## License

MIT License — see [LICENSE](LICENSE) for details.
