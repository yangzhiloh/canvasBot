# Architecture and scope

## Objective

Build a local study tool that imports accessible text-based PDFs from one Canvas course, retrieves supporting passages, and generates answers with document and page citations.

## Initial scope

- One account and one selected course.
- Explicit manual import of 5 to 10 accessible text-based PDFs.
- Local document storage, page-preserving extraction, deterministic chunks, and SQLite persistence.
- Independent keyword, semantic, and hybrid retrieval modes over the same corpus.
- Grounded answers with supporting passages, source links, and insufficient-evidence behavior.
- A frozen evaluation set and a reproducible command-line demonstration.

## Data flow

1. Authenticate to Canvas and list courses with bounded pagination.
2. Select one course and discover accessible PDFs through its file listing.
3. Select a small batch for download and record source metadata.
4. Extract text with original PDF page numbers and inspect sample pages.
5. Create deterministic chunks and persist their text and provenance.
6. Search with TF-IDF, sentence embeddings, or reciprocal rank fusion.
7. Supply a bounded set of evidence passages to the answer model.
8. Validate citation references and display the answer alongside its evidence.

Ordinary Python functions coordinate the workflow. SQLite stores materials, import status, and chunks. Local embedding arrays are paired with an ordered chunk-ID manifest. A command-line interface is sufficient for the initial release.

## Planned components

| Component | Responsibility |
| --- | --- |
| `canvasbot/config.py` | Validated settings and credential handling |
| `canvasbot/canvas.py` | Canvas requests, pagination, and selected downloads |
| `canvasbot/documents.py` | Page extraction and deterministic chunking |
| `canvasbot/storage.py` | SQLite schema and parameterized operations |
| `canvasbot/retrieval.py` | Keyword search, embeddings, and rank fusion |
| `canvasbot/answers.py` | Provider integration, evidence prompts, and citation validation |
| `canvasbot/models.py` | Shared records as required by implemented features |
| `scripts/` | Connection, import, question-answering, and evaluation entry points |
| `tests/` | Configuration, HTTP, provenance, retrieval, and answer checks |
| `evaluation/` | Checked questions, corpus configuration, and measured results |

These components are planned. Application source and tests have not yet been implemented.

## Technology choices

- Python 3.12 is the preferred target; interpreter usability and dependency compatibility require verification.
- HTTPX handles HTTP requests, timeouts, and controlled retries.
- Pydantic Settings validates configuration at the application boundary.
- pypdf provides the initial text extraction path.
- The standard-library `sqlite3` module provides local persistence.
- scikit-learn supplies the TF-IDF baseline; NumPy supports vector operations where needed.
- Sentence Transformers supplies local sentence embeddings. Record the selected model and revision when adopted.
- Pytest and mocked HTTP responses cover behavior; Ruff provides formatting and linting.
- One answer-generation provider will be selected at the generation milestone. Any passages sent to a hosted provider must be documented.

Dependencies will be introduced at the milestone that needs them.

## Canvas integration requirements

The first connection check uses `GET /api/v1/courses`. Inspecting its first response proves connectivity for that page; complete enumeration requires pagination. The importer uses `GET /api/v1/courses/:course_id/files` and reports unavailable files or denied access.

Attach the token only to the configured Canvas origin. External signed download URLs use a credential-free request. Bound timeouts, retries, page counts, file counts, and download sizes. Handle 401, 403, 429, and transient server failures explicitly.

Use generated local filenames. Retain the Canvas file ID, source URL, capture time, and content hash. Record per-file failures, and preserve any previous successful import if a later download fails.

## Retrieval and answer requirements

Chunks retain material ID, page references, ordinal, and text. Record chunk size, overlap, and processing configuration, keeping inputs within the selected embedding model's limit. Defer documents that require OCR or complex diagram interpretation.

Run keyword, semantic, and hybrid retrieval over identical indexed content. Preserve chunk IDs across rankings and record reciprocal rank fusion settings. Similarity scores are ranking signals rather than calibrated confidence values.

Treat retrieved text as data. Supply numbered evidence passages, require grounded claims, and reject citation IDs outside that evidence set. Citation validity and semantic support must be checked separately. Cover unsupported questions, invented citations, and malicious instructions embedded in documents.

## Evaluation

Prepare 16 to 20 manually checked questions with answerable and unanswerable cases. Separate development and holdout questions before tuning, avoid near-duplicate questions across splits, and record the corpus snapshot and settings.

Compare retrieval modes using hit rate at 5: the proportion of answerable questions with a labelled supporting passage in the first five results. Report the numerator and denominator. Separately inspect citation support, unsupported claims, appropriate abstention, and response latency.

Report measured results, sample size, and failures. This small benchmark evaluates the project corpus and does not establish general academic accuracy.

## Deferred work

A minimal Streamlit interface is optional after the core works. LangGraph orchestration and an MCP search tool are extensions after core evaluation.

Automatic synchronization, multi-course search, OCR, video, assessment automation, PostgreSQL, vector database services, Docker services, a separate API backend, background workers, and multi-user deployment are outside the initial release. Model fine-tuning is separate work.

## References

- [Canvas courses API](https://developerdocs.instructure.com/services/canvas/resources/courses)
- [Canvas files API](https://developerdocs.instructure.com/services/canvas/resources/files)
- [Sentence Transformers semantic search](https://www.sbert.net/examples/sentence_transformer/applications/semantic-search/README.html)

Check current official API documentation when implementing each integration.
