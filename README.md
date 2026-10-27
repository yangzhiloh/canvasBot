# canvasBot

A personal Canvas Study Assistant learning project: connect to Canvas, select one course, import a small set of accessible text-based PDFs, and build retrieval and grounded answers with document and page citations.

## Current status

Planning and repository setup only. Application code, dependency installation, usable Python execution, and a real Canvas connection have yet to be verified. No retrieval or answer-quality results have been measured.

The repository is being reused from OpportunityLens at the owner's request. Earlier commits and branches retain that project's history; the current project is canvasBot.

## Planned core

- A real Canvas API connection with explicit manual import of 5 to 10 selected PDFs from one course.
- Page-preserving extraction, deterministic chunks, and SQLite persistence.
- Keyword search with TF-IDF, semantic search with local sentence embeddings, and hybrid search with reciprocal rank fusion.
- Grounded answers with document and page citations, supporting passages, and insufficient-evidence behavior.
- A small frozen evaluation comparing retrieval methods and checking answer support.

Start with a command-line flow. A minimal Streamlit interface is optional after the core works. PostgreSQL, vector database services, Docker, a separate backend, automatic sync, OCR, video, and multi-course search are deferred. LangGraph and MCP are extensions after the core evaluation.

## Building and learning

This is a learner-led project. Work proceeds one small exercise at a time, with observed output reviewed before progress is recorded.

Start with the [build guide](docs/canvas-copilot-build-guide.md), check [verified progress](docs/progress.md), then follow the [first milestone plan](docs/canvasbot-first-milestone-plan.md). The [guided handoff](docs/canvasbot-guided-handoff.md) records the coaching workflow.

The next checkpoint is to verify available Python interpreters and the existing virtual environment locally. Python 3.12 is preferred in the plan, but the selected runtime and package compatibility still require verification. There is no runnable application or dependency manifest yet.

Keep Canvas credentials local. Credentials, virtual environments, and downloaded course materials are excluded from version control. Public demonstrations should use permitted public or synthetic documents.

The planning estimate is 30 to 38 focused hours including buffer. At about 20 hours, Canvas import and retrieval are the safer milestone.
