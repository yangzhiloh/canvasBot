# CanvasBot Guided Handoff

Updated 27 October 2026. Continue the user's personal Canvas Study Assistant project in `C:/Users/Loh Yang Zhi/Documents/Projects/CanvasBot`.

## User request and decisions

The user requested a new CanvasBot chat using GPT-6.1 Sol with high reasoning. They want a smaller project that teaches core AI engineering and RAG concepts within about a month, with 5 to 10 available hours per week. Canvas API integration is required in the core. The project should support internship applications through actual functionality and measured evaluation.

The user wants to build personally with coaching. Give one small exercise at a time, explain the concept and expected result, review their output, and update verified progress. Do not generate the whole application or silently take over implementation. If the user asks you to implement a particular step, that authorization takes precedence.

## Read in order

1. `docs/canvas-copilot-build-guide.md`: current scope, architecture and learning objectives.
2. `docs/progress.md`: verified state and first outstanding checkpoint.
3. `docs/canvasbot-first-milestone-plan.md`: bounded implementation and checks for environment, configuration and the first Canvas connection.

These local revised files supersede older guides in other chats or historical output folders. Do not restore the old PostgreSQL, pgvector, Docling or 45-to-70-hour design. On 27 October 2026 the user superseded the earlier separate-project decision: reuse and rename the OpportunityLens repository to `canvasBot`, replacing its current files while preserving Git history. Earlier OpportunityLens commits and branches remain historical records.

## Verified state at handoff

- Root contains the existing `.venv/`, the four Canvas planning documents in `docs/`, a README, `.gitignore`, and the reused Git metadata.
- The GitHub repository is `https://github.com/yangzhiloh/canvasBot`, with `main` as its default branch. The local `origin` uses that renamed repository.
- The existing three planning files have been revised; the first-milestone plan is newly added.
- There is no source code, test suite, dependency manifest, `.env`, or `.env.example` at the root.
- `.venv/pyvenv.cfg` identifies Python 3.14.2.
- An attempt to run the existing environment from the originating chat was denied by that execution environment. It was not established to be broken. Installed dependencies and interpreter usability remain unverified.
- No authenticated Canvas request or model-provider request has been verified.
- No product implementation, dependency installation or credential changes were performed as part of this handoff. Existing Git metadata was moved from OpportunityLens, and its linked checkouts were repaired. Old working files and a Git metadata copy were saved outside this repository at `C:/Users/Loh Yang Zhi/Documents/Projects/CanvasBot-Recovery-20261027`.

Inspect current state before acting because the user may have made changes after this record.

## First release

Connect to Canvas -> select one course -> manually import 5 to 10 accessible text-based PDFs -> extract pages -> chunk and persist -> keyword, semantic and hybrid search -> answer with supporting page citations -> evaluate a small frozen question set.

Use Python, HTTPX, Pydantic Settings, pypdf, SQLite, scikit-learn, NumPy as needed, Sentence Transformers, and one selected generation provider. Add dependencies only when needed. A command-line flow is sufficient for the core. Streamlit follows if time permits.

Keep Canvas import manual. Keep data local except passages deliberately sent to a selected hosted generation provider. Explain that boundary when choosing a provider. Do not invent a model name, endpoint, credential or course identifier.

## Budget and scope control

The current estimate is 30 to 38 hours including about 4 hours of buffer. This requires availability near the upper end of 5 to 10 hours a week. At around 20 total hours, a Canvas import and retrieval demonstration is the safer milestone; full cited generation may require more time.

Defer scheduled sync, multi-course discovery, OCR, videos, assessments automation, PostgreSQL, Docker, API backend, workers and custom frontend. LangGraph and MCP are extensions after the core evaluation. Fine-tuning is separate study. Do not claim these skills merely because a package is installed.

## Coaching and verification

1. Explain the immediate concept in two to four sentences.
2. Name the exact file and offer one small exercise or change.
3. State the check and what success would establish.
4. Let the user write or run the step; request output without secrets.
5. Review the observed result before proceeding.
6. Update `docs/progress.md` using actual evidence.

Use meaningful tests for configuration, HTTP behavior, page provenance, retrieval and citations. A citation ID passing validation does not prove the answer is supported. Review sample answers manually. Evaluation reports must show actual counts and limitations.

No subagents are needed for this guided learning task. Use the existing CanvasBot checkout and preserve its historical branches and linked checkouts. The user authorized the repository replacement and rename; later unrelated pushes, publishing, or messages require their own authorization. Preserve existing user work.

## First response in the receiving chat

Briefly acknowledge the one-course, Canvas-API-first RAG scope and guided build. Read the local files, inspect the root and available interpreter state, and begin the first outstanding checkpoint. Explain the environment choice and give one small action toward listing real Canvas courses. Do not repeat a long project overview, ask the user to reconfirm the already chosen scope, install the entire stack, or fabricate successful access.

The user needs to configure their Canvas origin and token locally when that step is reached. Do not ask for a token in chat. The first real integration pass condition is seeing their own course names through an authenticated request; the complete course and file listings subsequently require pagination.
