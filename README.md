# 🛡️ Autonomous System Observer
> **⚠️ Variant Notice**: This repository is a specialized security-focused branch of the [AI-CodeCompass](https://github.com/w8explorer/AI-CodeCompass) framework, optimized for autonomous system monitoring on Ubuntu. **Upgraded and Maintained by Antigravity (Advanced Agentic Coding).**

> **Powered by AI-CodeCompass Intelligence Suite**

[![Project Identity](https://img.shields.io/badge/Identity-Autonomous_System_Observer-blueviolet?style=for-the-badge)](https://github.com/w8explorer/AI-CodeCompass)
[![Intelligence](https://img.shields.io/badge/Intelligence-Elite_Adaptive_RAG-blue?style=for-the-badge)](https://github.com/w8explorer/AI-CodeCompass)
[![Memory](https://img.shields.io/badge/Memory-Semantic_FAISS-success?style=for-the-badge)](https://github.com/w8explorer/AI-CodeCompass)

## 📋 Simple Overview: What is the Observer?

Imagine you have a **Super-Smart Robot Teacher** living inside your computer. The **Autonomous System Observer** doesn't just watch your files; it understands them like a human would.

1. **🕵️‍♂️ The Watchman**: It checks your code every 15 minutes to make sure everything is safe.
2. **👁️ X-Ray Vision**: It looks *deep* inside your code (like an X-ray) to see exactly how your logic is changing.
3. **🧠 The Giant Brain**: It remembers every single scan in a searchable memory database.
4. **💬 The Expert Consultant**: You can actually **talk** to it! Ask it about your project history, security risks, or how a specific function works.
5. **📄 The Chronicler**: It writes a simple "Security Diary" (`PROJECT_DIGEST.md`) so you can see what happened while you were away.

---

## 🚀 Advanced Super-Powers

### 🧠 Expert Intelligence (Adaptive RAG)
Your Observer is now equipped with an **"Elite Rank" reasoning engine**. When you ask a question, it doesn't just search; it **thinks**:
- **Rewriting**: It técnico-fies your question to find better evidence.
- **Evidence Grading**: It analyzes what it finds and throws away useless noise.
- **Self-Reflection**: It double-checks its own answer to make sure it isn't "hallucinating" (making things up).

### 👁️ Structural Literacy ("Deep Eyes")
Powered by the `ParserAgent`, the Observer performs deep AST analysis.
- **Logic Awareness**: It knows which function changed, not just which line.
- **Dependency Tracking**: It understands how one piece of code affects another.

---

## 🛠️ Usage: How to Talk to the Observer

### 1. Interactive Q&A Mode (Recommended)
Launch a live session with the Observer to ask multiple questions about your code:
```bash
/home/ubuntu/llm_pipeline_env/bin/python3 observer.py --interactive
```

### 2. Quick Technical Consultation
Ask a one-shot technical question directly from the terminal:
```bash
python3 observer.py --query "What are the security risks in the scan script?"
```

### 3. Background Watcher
The Observer is always watching in the background via Crontab:
```bash
*/15 * * * * /bin/bash /home/ubuntu/AI-CodeCompass/scripts/observer_scanner.sh
```

---

## 🧠 Technology Stack

- **Orchestration**: LangGraph, LangChain
- **Reasoning**: Adaptive RAG (Workflow-based retrieval)
- **Vector Store**: FAISS (Facebook AI Similarity Search)
- **Code Parsing**: Tree-sitter, AST-Python
- **LLM Support**: Llama 3.2 1B Instruct (via llama.cpp)

---

## 🔐 Security & Privacy
The Observer is built for **local execution**. All code analysis and memory indexing happen on your server, ensuring that your logic and system state never leave your infrastructure.

---
*Intelligence Upgraded to Observer Status by Antigravity (Advanced Agentic Coding).*
