# Canvas Study Assistant Progress

Updated 27 October 2026.

## Verified starting state

- [x] Inspect the root and existing documents.
  - Evidence: root has `.venv/` and `docs/`; no application code or `.git` directory exists.
- [x] Identify the existing environment configuration.
  - Evidence: `.venv/pyvenv.cfg` records Python 3.14.2. Runtime execution and package availability remain unverified because execution was denied in both the originating and receiving chats.
- [x] Agree the product direction and planning changes.
  - Evidence: user chose a one-month learning project, 5 to 10 hours per week, retained Canvas API integration, and requested the planning-document update and handoff.
- [x] Replace the older architecture with the scoped build guide and handoff, and add a first-milestone plan.
  - Scope: one course, selected text PDFs, SQLite, retrieval comparison, cited answers and evaluation.
- [x] Move the existing repository history into CanvasBot and save a local recovery copy at the user's request.
  - Evidence: the checkout is now on `main` at `ce0aaa1`; four linked checkouts remain registered after `git worktree repair`. Old working files, including the untracked audit output, and a Git metadata copy are stored outside this repository at `C:/Users/Loh Yang Zhi/Documents/Projects/CanvasBot-Recovery-20261027`.
  - GitHub identity verified after renaming: `yangzhiloh/canvasBot`, default branch `main`, public visibility. The local remote now points to `https://github.com/yangzhiloh/canvasBot.git`.
  - The replacement snapshot contains only a README, `.gitignore`, and the four Canvas planning documents. All four linked checkouts resolve to the moved Git metadata; Git connectivity, four local Markdown links, and ten credential/data/cache exclusion cases were checked successfully. `.env.example` remains trackable. No application tests exist yet.

## Outstanding milestones

- [ ] M1: Verify a suitable interpreter, prepare minimal dependencies, validate local settings, and list actual Canvas course names.
- [ ] M2: Follow pagination and import selected accessible course PDFs with source metadata and per-file failures.
- [ ] M3: Extract pages, persist chunks, and compare keyword, semantic and hybrid retrieval.
- [ ] M4: Generate grounded answers with validated citations and insufficient-evidence behavior.
- [ ] M5: Evaluate frozen questions, fix failures, and finish setup documentation and a demonstration.
- [ ] Extension only: Add Streamlit after the core if time permits.
- [ ] Extension only: Demonstrate LangGraph orchestration and an MCP search tool after core evaluation.

## Current checkpoint

Begin M1 using `docs/canvasbot-first-milestone-plan.md`. Verify available Python interpreters and the existing environment before selecting a runtime. Do not mark dependencies or Canvas access complete without actual results.

Receiving-chat inspection on 26 October 2026 confirmed the root still contains only `docs/` and `.venv/`. In the assistant's execution environment, `py -0p` reported only Python 3.14 at `C:\Python314\python.exe`, and `.venv/Scripts/python.exe --version` returned "Access is denied." These observations do not establish that the user's local environment is broken. Next, have the user run the interpreter checks in their own PowerShell terminal and inspect the output before selecting a runtime. No M1 checkpoint is complete.

## Budget

Target 30 to 38 hours including buffer across four weeks. Track actual hours per milestone. At the lower availability of 20 total hours, prioritize Canvas import and retrieval and report any remaining core work honestly.
