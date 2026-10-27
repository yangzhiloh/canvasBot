# Development status

Updated 27 October 2026.

## Completed

- [x] Establish the repository and project documentation.
- [x] Define the initial scope, architecture, and evaluation approach.
- [x] Verify Git exclusions for credentials, virtual environments, course data, and generated caches.

## Current state

Application source, dependency manifests, configuration tests, and a runnable workflow have not yet been implemented. The existing virtual environment configuration records Python 3.14.2; interpreter usability and installed packages remain unverified. No real Canvas API connection or retrieval benchmark has been verified.

## Roadmap

| Milestone | Deliverable | Status |
| --- | --- | --- |
| M1 | Environment, validated settings, and actual Canvas course names | Pending |
| M2 | Pagination and selected PDF import with provenance and per-file failures | Planned |
| M3 | Page extraction, SQLite persistence, and retrieval comparison | Planned |
| M4 | Grounded answers, validated citations, and insufficient-evidence behavior | Planned |
| M5 | Frozen evaluation, setup documentation, and demonstration | Planned |

Streamlit is optional after the core workflow. LangGraph and MCP are extensions after evaluation.

## Next checkpoint

Follow [Milestone 1](milestone-1.md): verify available Python interpreters and the existing environment, then select a runtime before installing dependencies. Record successful checks and measured outcomes as each milestone is completed.
