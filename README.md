# canvasBot

A local study tool designed to connect Canvas course materials with searchable evidence and answers that cite specific documents and pages.

## Current status

Early development. The project specification, repository, and documentation are in place. Application implementation and Canvas connectivity are pending. Retrieval and answer quality have not yet been evaluated.

## Planned capabilities

- A real Canvas API connection with explicit manual import of 5 to 10 selected PDFs from one course.
- Page-preserving extraction, deterministic chunks, and SQLite persistence.
- Keyword search with TF-IDF, semantic search with local sentence embeddings, and hybrid search with reciprocal rank fusion.
- Grounded answers with document and page citations, supporting passages, and insufficient-evidence behavior.
- A small frozen evaluation comparing retrieval methods and checking answer support.

The initial interface will be a command-line workflow. A minimal Streamlit interface may follow after the core evaluation.

## Architecture

Canvas API → selected PDFs → page extraction → chunks and SQLite storage → keyword and semantic retrieval → evidence-grounded answers.

The planned stack uses Python, HTTPX, Pydantic Settings, pypdf, SQLite, scikit-learn, and Sentence Transformers. The answer-generation provider will be selected during implementation.

## Documentation

- [Architecture and scope](docs/architecture.md)
- [Milestone 1: Canvas connection](docs/milestone-1.md)
- [Development status](docs/progress.md)

## Development

The next milestone is environment verification, validated configuration, and an authenticated Canvas course listing. Setup instructions will be added as the first runnable workflow is implemented.

Credentials, virtual environments, and downloaded course materials are excluded from version control. Public demonstrations will use permitted public or synthetic documents.
