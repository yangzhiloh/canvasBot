# Milestone 1: Canvas connection

## Goal

Verify a usable Python environment, validate local Canvas configuration, and display actual course names through one authenticated API request.

## Scope

This milestone covers environment setup, configuration, and a first-page connection check. Complete pagination and PDF import follow in Milestone 2.

## Environment and dependencies

- [ ] Inspect available interpreters and verify the existing environment before selecting a runtime.
- [ ] Prefer Python 3.12 where practical; record the selected version and verify package compatibility.
- [x] Verify exclusions for credentials, virtual environments, downloaded data, and generated caches.
- [ ] Add HTTPX and Pydantic Settings as runtime dependencies, with Pytest and Ruff as development dependencies.
- [ ] Verify dependency installation using the selected environment's interpreter.

Preserve the existing environment until a replacement works. Install only dependencies required by this milestone.

## Configuration contract

Planned interface: `canvasbot.config.CanvasSettings`.

- Normalize the Canvas base URL to an HTTPS origin.
- Reject HTTP URLs, embedded credentials, paths, query strings, and fragments.
- Reject missing or blank tokens and mask tokens in object representations.
- Read `CANVAS_BASE_URL` and `CANVAS_TOKEN` from a local `.env` file.
- Provide a credential-free `.env.example`.
- Support explicit test inputs without loading a local environment file.

Write focused tests for normalization, invalid origins, token validation, and secret masking. Confirm expected failures before implementing the settings class, then rerun tests and lint checks.

## Connection contract

Planned entry point: `python -m scripts.check_canvas`.

Planned function: `fetch_course_names(settings, client) -> list[str]`.

- Request `GET /api/v1/courses` on the configured origin.
- Include bearer authentication, a bounded timeout, and an explicit page size.
- Disable automatic redirects for the connection check.
- Require a successful HTTP status and a list response.
- Return names only from valid course objects on that response page.
- Print course names or bounded error messages without exposing credentials, request headers, or raw response bodies.
- Label the output as the first response page and report an empty result explicitly.

Use a mocked HTTP transport to verify request path, authentication, successful name extraction, HTTP error propagation, and rejection of non-list responses.

## Acceptance criteria

- [ ] Configuration tests pass.
- [ ] Connection behavior tests pass.
- [ ] All implemented tests and lint checks pass.
- [ ] A real authenticated request displays actual Canvas course names.
- [ ] Record the interpreter, installed package versions, observed checks, and connection outcome in the development status document.

An empty response requires further inspection before confirming course access. Keep credentials local and check institutional API access requirements before configuring a token.

## Next milestone

Implement bounded same-origin pagination, selected course-file discovery, and safe PDF downloads. Cover multiple pages, denied access, retry limits, foreign-origin links, oversized downloads, per-file failures, and file provenance.
