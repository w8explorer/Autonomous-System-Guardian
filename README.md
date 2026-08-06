# ðŸ›¡ï¸ Autonomous System Observer

> A security-focused AI daemon that continuously monitors your server's codebase, detects suspicious changes, and lets you query your system's history through a local LLM â€” all without sending data to the cloud.

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
- **Understands code structure** via AST analysis (knows *what function* changed, not just *what line*)
- **Builds semantic memory** of your entire codebase using FAISS vector embeddings
- **Detects anomalies** â€” new files, modified scripts, permission changes â€” and flags risks
- **Writes a Security Diary** (`PROJECT_DIGEST.md`) â€” a human-readable log of what changed and why it matters
- **Answers your questions** about the system's history, security risks, and code logic via interactive CLI

---

## Architecture

```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚   Cron / Background Watcher         â”‚  â† Runs every 15 min via crontab
â”‚   observer_scanner.sh               â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
               â”‚ filesystem events
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚   observer.py (Main Daemon)         â”‚
â”‚                                     â”‚
â”‚   â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”  â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”  â”‚
â”‚   â”‚ ParserAgent â”‚  â”‚  RAG Engine â”‚  â”‚
â”‚   â”‚ (AST + Tree-â”‚  â”‚ (LangGraph) â”‚  â”‚
â”‚   â”‚  sitter)    â”‚  â”‚             â”‚  â”‚
â”‚   â””â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”˜  â””â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”˜  â”‚
â”‚          â”‚                â”‚         â”‚
â”‚   â”Œâ”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â” â”‚
â”‚   â”‚       FAISS Vector Store      â”‚ â”‚  â† Semantic memory of your system
â”‚   â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€-â”˜ â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
               â”‚
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â–¼â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚   Llama 3.2 1B (via llama.cpp)      â”‚  â† Runs locally, no internet needed
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```

---

## Key Features

### ðŸ” AST-Level "Deep Eyes" Analysis
The `ParserAgent` uses Python's `ast` module and `tree-sitter` to parse code structure. It understands:
- Which **functions** were added, modified, or removed (not just line diffs)
- **Complexity changes** that may introduce technical debt
- **Import graph changes** that affect dependencies

### ðŸ§  Adaptive RAG Intelligence
When you ask a question, the system doesn't just search â€” it reasons:
1. **Query Rewriting** â€” technically refines your question for better retrieval
2. **Document Grading** â€” scores and filters retrieved code chunks by relevance
3. **Self-Reflection** â€” validates the answer before returning it to prevent hallucinations

### ðŸ“‹ Automated Security Digest
After each scan, the observer writes `PROJECT_DIGEST.md` â€” a structured report of:
- What changed since the last scan
- Which files were modified and by how much
- Any anomalies or security concerns flagged

### ðŸ”’ Fully Offline â€” Air-Gap Ready
All processing runs locally. No API keys, no data sent to external servers. Designed for servers where data privacy is non-negotiable.

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Orchestration** | LangChain, LangGraph |
| **Reasoning** | Adaptive RAG (Query Rewrite â†’ Grade â†’ Reflect) |
| **Vector Store** | FAISS |
| **Code Parsing** | Tree-sitter, Python `ast` module |
| **LLM** | Llama 3.2 1B Instruct (via llama.cpp) |
| **Background Runner** | Cron + Bash shell scripts |
| **Language** | Python 3.10+ |

---

## Getting Started

### Prerequisites
- Ubuntu server (tested on Ubuntu 22.04)
- Python 3.10+
- `llama.cpp` with Llama 3.2 1B model
- Cron available (standard on all Linux systems)

### Install

```bash
git clone https://github.com/w8explorer/Autonomous-System-Observer.git
cd Autonomous-System-Observer

pip install -r requirements.txt

cp .env.example .env
# Set MODEL_PATH, WATCH_DIR in .env
```

### Run

```bash
# Interactive mode â€” ask questions about your system
python observer.py --interactive

# One-shot security query
python observer.py --query "Are there any hardcoded credentials in the scripts directory?"

# Trigger a manual scan
python observer.py --scan
```

### Set Up Background Monitoring (Cron)

```bash
# Add to crontab â€” scans every 15 minutes automatically
crontab -e

# Add this line:
*/15 * * * * /bin/bash /path/to/Autonomous-System-Observer/scripts/observer_scanner.sh
```

---

## Project Structure

```
Autonomous-System-Observer/
â”œâ”€â”€ api/                # FastAPI interface (optional, for remote queries)
â”œâ”€â”€ agents/             # LangGraph nodes: parser, retriever, grader, reflector
â”œâ”€â”€ core/               # RAG pipeline, FAISS store, embeddings
â”œâ”€â”€ libs/               # Shared utilities
â”œâ”€â”€ ui/                 # Optional Streamlit dashboard
â”œâ”€â”€ scripts/            # Cron shell scripts (observer_scanner.sh)
â”œâ”€â”€ observer.py         # Main daemon â€” interactive + scan modes
â”œâ”€â”€ observer_chat.py    # CLI chat interface
â”œâ”€â”€ engine_adapter.py   # LLM engine abstraction layer
â””â”€â”€ launcher.py         # Combined FastAPI + UI startup
```

---

## Example Interactions

```
You: What changed in the last scan?
Observer: In the last scan (14 mins ago), 3 files were modified:
  - scripts/start_observer_brain.sh â€” added nohup flags
  - core/rag_pipeline.py â€” grader threshold adjusted from 0.6 to 0.8
  - README.md â€” documentation update
  No anomalies detected.

You: Are there any security vulnerabilities in the scan script?
Observer: Yes. The observer_scan.sh passes output from `find` directly
  into the Python parser without sanitization. A file with a specially
  crafted name could cause command injection. Recommend wrapping the
  find output in quotes and validating filenames before passing to Python.
```

---

## Related Projects

- [AI-CodeCompass](https://github.com/w8explorer/AI-CodeCompass) â€” The parent project: a full developer assistant with Streamlit UI and FastAPI backend

---

## License

MIT License â€” see [LICENSE](LICENSE) for details.
