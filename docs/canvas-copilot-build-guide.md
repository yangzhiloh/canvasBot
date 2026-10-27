# Canvas Study Assistant Build Guide

Revised 27 October 2026. This guide supersedes the previous PostgreSQL, pgvector and Docling design. The user has chosen a Canvas API integration as a required part of a small personal RAG project.

## Project overview

Build a local study assistant that connects to Canvas, imports accessible text-based PDFs from one selected course, retrieves relevant passages, and answers academic questions with document and page citations. Its answers are grounded in the indexed materials. It does not promise to answer every academic question or correctly interpret every equation or diagram.

The developer is a Computer Science undergraduate who mainly knows Python scripts and wants to learn AI engineering through personally building the project with coaching. The available time is 5 to 10 hours a week for about one month. The project should demonstrate real retrieval and evaluation, with honest limitations, for AI and ML internship applications.

## Verified starting state

On 26 October 2026 the project root contained `docs/` and `.venv/`, including this guide, `canvasbot-guided-handoff.md`, `progress.md`, and `canvasbot-first-milestone-plan.md`.

On 27 October 2026 the user explicitly chose to reuse and rename the existing OpportunityLens repository to `canvasBot`, replacing its current files while retaining Git history. The existing Git metadata was moved into the CanvasBot folder, and its linked checkouts were repaired. The old working files and a copy of the Git metadata were saved in a local recovery directory outside this repository. A README and credential/data exclusion rules were added for the replacement. Older commits and branches remain historical records rather than the current architecture.

There is no application source, dependency manifest, test suite, or environment template at the root. `.venv/pyvenv.cfg` identifies Python 3.14.2. Attempts to execute that interpreter from the originating and receiving chats were denied by their execution environments. That does not establish that the interpreter is broken. Runtime compatibility, installed packages and Canvas access remain unverified.

## Definition of a finished core

1. Authenticate to the user's Canvas instance and list the user's courses.
2. Select one course and explicitly import a small batch of accessible PDFs from its file listing.
3. Save original files locally and retain source URL, Canvas file ID, capture time and content hash.
4. Extract readable text with real PDF page numbers and create deterministic chunks.
5. Search the same corpus with TF-IDF, sentence embeddings, and reciprocal rank fusion.
6. Generate answers using retrieved passages and validate references to supplied citation IDs.
7. Display the supporting passage, document name, page number and Canvas source link.
8. Handle insufficient evidence and document extraction failures visibly.
9. Run a small frozen evaluation and document measured results and failures.
10. Provide reproducible setup and a short demonstration, with a command-line interface sufficient for the core.

The file listing is not a promise of complete course coverage. Report unavailable files or denied listing access. If the selected course cannot be imported, resolve that access issue or choose another accessible course before expanding discovery.

## Required scope

- Personal use, one user and one selected course.
- Canvas API integration with explicit manual import.
- Initially 5 to 10 text-based PDFs, selected for clear extraction and manageable size.
- Local files, SQLite metadata and chunk storage, and local embedding search.
- A small explicit candidate set of retrieved passages per answer.
- Keyword, semantic and hybrid retrieval on the same indexed content.
- Cited question answering, abstention tests, evaluation and learning notes.
- A minimal command-line flow first; Streamlit when the core works and time remains.

## Deferred scope

Scheduled synchronization, multi-course search, Workday or other job providers, PostgreSQL, pgvector, SQLAlchemy, migrations, FastAPI, custom web frontend, background workers, Docker services, advanced telemetry, OCR, Docling, raw video, transcription, automatic assignment solving, and multi-user deployment are outside the first release.

LangGraph and MCP are extensions after the core passes evaluation. LLM fine-tuning is separate study. Using pretrained embeddings or calling an LLM is not model-training experience.

## Architecture

Canvas API -> local PDFs -> page text -> chunks -> keyword and embedding search -> selected evidence -> answer with citations.

Ordinary Python functions coordinate this flow. Keep application services usable from a script so adding Streamlit does not require a backend rewrite. SQLite stores materials, chunks and import status. Embedding arrays can be cached locally alongside an ordered chunk-ID manifest; no vector database is required for this small corpus.

Use a few boundary models only when their features need them: settings, material, chunk, search hit and answer. Do not implement a general evidence framework, immutable event system or large domain-contract layer before importing one actual file.

## Technology choices

- Python: prefer an available Python 3.12 interpreter for the local embedding stack. Inspect available interpreters and the existing environment before choosing. Preserve the existing environment until a replacement works.
- HTTPX: Canvas requests, timeouts and controlled retries.
- Pydantic Settings: environment configuration and validation.
- pypdf: first text extraction path for text-based PDFs.
- sqlite3: parameterized SQL and persistence without a database service.
- scikit-learn: TF-IDF baseline; NumPy for vector calculations when directly used.
- Sentence Transformers: local embeddings, with an exact chosen model and revision recorded when adopted.
- One answer-generation provider: choose an available hosted or local provider with the user at the generation milestone, then follow that provider's current official API. Document which passages leave the machine when using a hosted provider.
- Pytest and mocked HTTP responses: focused behavior checks. Ruff for formatting and linting.
- Streamlit: optional small interface after the core flow works.

Install dependencies incrementally. The Canvas milestone needs only HTTP, configuration and testing tools. Add document, retrieval and model dependencies at the milestone that uses them. Do not install the old guide's complete stack.

## Canvas integration

The first real checkpoint is one authenticated `GET /api/v1/courses` call that displays course names without revealing credentials. A connection check proves only the response it inspected; complete listing requires pagination.

The reusable client then follows Canvas pagination links, limits page count and total downloads, uses bounded timeouts, handles 401/403 clearly, and bounds retries for 429 and transient server errors. The initial importer uses `GET /api/v1/courses/:course_id/files`, filters accessible PDFs, and lets the user choose a small batch before download.

Attach the Canvas token only to the configured Canvas origin. Use a credential-free request for external signed download URLs. Enforce a maximum download size, use generated local filenames, and record per-file failures. A failed download must not replace a previously successful local import.

If discovery through modules becomes necessary, add that as a separately bounded follow-up after proving the selected course's file-list access. Do not promise automatic discovery of all linked external content.

## Retrieval and answer behavior

Preserve the actual page number during extraction. Inspect a sample of extracted pages before indexing. Defer PDFs whose usable text depends on OCR or complex diagram interpretation.

Chunks retain material ID, page references, ordinal and text. Size chunks within the adopted embedding model's input limit and record overlap and processing configuration. Keep keyword, semantic and hybrid modes independently runnable.

Fuse keyword and semantic ranked lists with reciprocal rank fusion, recording the chosen settings. Preserve chunk IDs throughout retrieval. Similarity scores are ranking signals, not calibrated answer confidence.

Give the answer model numbered evidence passages and ask for claims grounded in those passages. Treat retrieved text as data, not instructions. Validate every returned citation ID against the supplied evidence. Valid IDs do not establish semantic support, so manually inspect a sample of answers. Include tests for an unsupported question, an invented citation and a malicious instruction in a document.

## Four week delivery plan

| Week | Deliverable | Estimated focused hours |
| --- | --- | ---: |
| 1 | Validated settings, Canvas connection, paginated listing and selected PDF import | 7 to 9 |
| 2 | Page extraction, SQLite persistence, TF-IDF, embeddings and hybrid retrieval | 6 to 8 |
| 3 | Cited generation, insufficient-evidence behavior and a usable question-answering script | 7 to 9 |
| 4 | Frozen evaluation, failure fixes, setup documentation and demonstration; Streamlit if time permits | 6 to 8 |

Reserve about 4 additional hours for environment and debugging problems. This is a planning range of 30 to 38 hours, not a delivery guarantee. It requires time near the upper end of the user's weekly availability. At about 20 total hours, prioritize a working Canvas import and retrieval flow; the full cited-answer core may need more time.

Review progress after every week. Reduce file count and interface work before removing retrieval comparisons, citations or evaluation. Do not increase the number of weeks or silently drop Canvas API integration.

## Evaluation

Create a target of 16 to 20 manually checked questions, including answerable and unanswerable cases. Separate development and holdout questions before tuning. Avoid near-duplicate questions across the split and record the corpus snapshot and settings.

Compare keyword, semantic and hybrid retrieval using hit rate at 5: the proportion of answerable questions with at least one labelled supporting passage in the first five results. Record numerator and denominator. Separately inspect citation support, unsupported claims, appropriate abstention and response latency.

This is a small development portfolio benchmark, not evidence of general academic accuracy. Report failures and sample size. Do not invent target achievement percentages or convert valid citation IDs into an answer-correctness score.

## Learning protocol

The user writes the source and tests with coaching, unless they explicitly ask the assistant to implement a step. For each small checkpoint: explain the concept, ask what the check should prove, let the user write or run it, inspect the result, and record only verified progress.

The user should be able to explain authentication and pagination; parameterized SQL and persistence; PDF extraction and chunking; embeddings and cosine similarity; keyword versus semantic retrieval; rank fusion; retrieval versus generation; citation validity versus support; and evaluation leakage and abstention.

## Planned files

Create files as each milestone needs them, not all at setup:

- `copilot/config.py`: validated Canvas and later model configuration.
- `copilot/canvas.py`: Canvas connection, pagination and selected PDF downloads.
- `copilot/documents.py`: page extraction and deterministic chunking.
- `copilot/storage.py`: SQLite schema and parameterized material/chunk operations.
- `copilot/retrieval.py`: TF-IDF, embeddings, fusion and ranked evidence.
- `copilot/answers.py`: provider adapter, evidence prompt and citation validation.
- `copilot/models.py`: shared records only when required by working features.
- `scripts/check_canvas.py`: first authenticated connection checkpoint.
- `scripts/import_course.py`: explicit one-course import.
- `scripts/ask.py`: complete command-line question-answer flow.
- `scripts/evaluate.py`: frozen retrieval and answer evaluation.
- `tests/`: focused unit and integration checks with permitted or synthetic fixtures.
- `evaluation/`: shareable checked questions, configuration and measured results.
- `app.py`: optional minimal Streamlit interface.
- `README.md`, dependency files, `.gitignore`, `.env.example`: setup and reproducibility.

Keep credentials and downloaded course material out of Git. Use permitted public or synthetic documents for a public demonstration. Never commit the user's private course PDFs.

## Official references

- Canvas courses: https://developerdocs.instructure.com/services/canvas/resources/courses
- Canvas files: https://developerdocs.instructure.com/services/canvas/resources/files
- Sentence Transformers semantic search: https://www.sbert.net/examples/sentence_transformer/applications/semantic-search/README.html
- LangGraph, extension only: https://docs.langchain.com/oss/python/langgraph/overview
- MCP, extension only: https://modelcontextprotocol.io/docs/develop/build-server

Before implementing an integration, verify its exact current API against the relevant official documentation.
