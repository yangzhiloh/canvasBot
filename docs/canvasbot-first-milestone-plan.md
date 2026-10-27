# Canvas Connection Implementation Plan

> **Execution:** Use the executing-plans skill for learner-led inline work. The user writes source and tests with coaching. Complete one small checkbox at a time. No subagents or worktrees are required.

**Goal:** Verify a usable Python environment, validate Canvas configuration, and display real Canvas course names through one authenticated request.

**Architecture:** Pydantic Settings validates local environment configuration. A small HTTPX script makes the first request and prints only course names or a bounded error. This connection check is not the complete paginated client; that is the next milestone.

**Tech Stack:** Python 3.12 preferred, HTTPX, Pydantic Settings, Pytest, Ruff.

**Spec:** `docs/canvas-copilot-build-guide.md`

## Global constraints

- Canvas API integration is required in the core.
- Work in the existing CanvasBot project and inspect for user changes before each edit.
- Existing `.venv` identifies Python 3.14.2; inspect usable interpreters before selecting a runtime and preserve existing work.
- Explain each step and let the user write or run it unless implementation is explicitly requested.
- Keep the Canvas origin and token local; do not request or print credentials in chat or logs.
- First dependencies are limited to HTTPX, Pydantic Settings, Pytest and Ruff.
- No database, embeddings, LLM, Streamlit, container or background-worker work in this milestone.
- Do not claim complete Canvas enumeration from the first response page.

## Task 1: Usable environment and validated settings

**Files:** Create `.gitignore`, `.env.example`, `requirements.txt`, `requirements-dev.txt`, `copilot/__init__.py`, `copilot/config.py`, and `tests/test_config.py` as needed. Local `.env` is created by the user.

**Interface:** `CanvasSettings(_env_file=None, base_url=..., token=...)` produces normalized `base_url: str` and masked `token: SecretStr`. The same class reads `CANVAS_BASE_URL` and `CANVAS_TOKEN` from `.env` when no explicit fields are passed.

- [ ] Explain virtual environments and inspect available interpreters with `py -0p`, then inspect `.venv/pyvenv.cfg`. Attempt `.venv/Scripts/python.exe --version` only through the receiving chat's permitted execution path. An access denial is an execution restriction, not proof of a broken environment.
- [ ] Select an available Python 3.12 runtime if practical. If another runtime is used, verify dependency compatibility and record the actual version. Do not silently delete the existing environment. Use the selected environment's interpreter directly for all commands.
- [x] Verify the repository's `.gitignore` excludes `.env`, `.venv/`, `.venv-*/`, `data/`, `__pycache__/`, `.pytest_cache/`, `.ruff_cache/` and `*.pyc` before configuring credentials. These rules were added and checked with `git check-ignore` during the repository replacement on 27 October 2026. Git history is already present because the user requested reuse of the existing repository; do not initialize a separate repository.
- [ ] Create `requirements.txt` containing `httpx` and `pydantic-settings`, and `requirements-dev.txt` containing `-r requirements.txt`, `pytest` and `ruff`. Install with the selected interpreter's `-m pip install -r requirements-dev.txt`. Record actual successful versions once verified.
- [ ] Explain boundary validation. Ask the user to predict why an HTTP origin or blank token should fail before making a request.
- [ ] Write the failing configuration tests below, then run `python -m pytest tests/test_config.py -q` with the selected interpreter. Confirm the failure is the missing module or implementation, not an unrelated setup failure.

```python
import pytest
from pydantic import ValidationError

from copilot.config import CanvasSettings


def test_normalizes_canvas_origin():
    settings = CanvasSettings(
        _env_file=None, base_url="https://canvas.example.edu/", token="test-token"
    )
    assert settings.base_url == "https://canvas.example.edu"
    assert "test-token" not in repr(settings)


@pytest.mark.parametrize(
    "origin",
    ["http://canvas.example.edu", "https://canvas.example.edu/path", "https://u:p@canvas.example.edu"],
)
def test_rejects_non_origin_values(origin):
    with pytest.raises(ValidationError):
        CanvasSettings(_env_file=None, base_url=origin, token="test-token")


def test_rejects_blank_token():
    with pytest.raises(ValidationError):
        CanvasSettings(_env_file=None, base_url="https://canvas.example.edu", token=" ")
```

- [ ] Add an empty `copilot/__init__.py` and the minimum settings implementation below. Explain each validator before the user writes it.

```python
from urllib.parse import urlsplit

from pydantic import SecretStr, field_validator
from pydantic_settings import BaseSettings, SettingsConfigDict


class CanvasSettings(BaseSettings):
    model_config = SettingsConfigDict(
        env_file=".env", env_prefix="CANVAS_", extra="ignore"
    )

    base_url: str
    token: SecretStr

    @field_validator("base_url")
    @classmethod
    def require_https_origin(cls, value: str) -> str:
        value = value.strip().rstrip("/")
        parsed = urlsplit(value)
        if (
            parsed.scheme != "https"
            or not parsed.hostname
            or parsed.username is not None
            or parsed.password is not None
            or parsed.path
            or parsed.query
            or parsed.fragment
        ):
            raise ValueError("Canvas URL must be an HTTPS origin")
        return value

    @field_validator("token")
    @classmethod
    def require_token(cls, value: SecretStr) -> SecretStr:
        if not value.get_secret_value().strip():
            raise ValueError("Canvas token must be configured locally")
        return value
```

- [ ] Re-run the configuration tests and Ruff on the new files. Observe passing output before recording the checkpoint.
- [ ] Create `.env.example` with `CANVAS_BASE_URL=` and `CANVAS_TOKEN=`. The user copies it to `.env` and fills values locally. Verify how their institution provides permitted API access using current official Canvas guidance; do not assume token creation is enabled.

## Task 2: One real authenticated connection

**Files:** Create `scripts/__init__.py`, `scripts/check_canvas.py`, and `tests/test_connection.py`.

**Interface:** `fetch_course_names(settings: CanvasSettings, client: httpx.Client) -> list[str]` returns names from the first response page. The script prints those names without the token, request headers or raw response body.

- [ ] Explain HTTP status codes and why the first response is not proof of complete pagination. Write this behavior test and verify the expected missing-module failure.

```python
import httpx

from copilot.config import CanvasSettings
from scripts.check_canvas import fetch_course_names


def test_fetches_names_with_canvas_authentication():
    settings = CanvasSettings(
        _env_file=None, base_url="https://canvas.example.edu", token="test-token"
    )

    def respond(request):
        assert request.url.path == "/api/v1/courses"
        assert request.headers["Authorization"] == "Bearer test-token"
        return httpx.Response(200, json=[{"id": 1, "name": "Algorithms"}])

    with httpx.Client(transport=httpx.MockTransport(respond)) as client:
        assert fetch_course_names(settings, client) == ["Algorithms"]
```

- [ ] Add an empty `scripts/__init__.py` and implement the smallest checked request below.

```python
import httpx
from pydantic import ValidationError

from copilot.config import CanvasSettings


def fetch_course_names(settings: CanvasSettings, client: httpx.Client) -> list[str]:
    response = client.get(
        f"{settings.base_url}/api/v1/courses",
        headers={"Authorization": f"Bearer {settings.token.get_secret_value()}"},
        params={"per_page": 20},
        timeout=15.0,
        follow_redirects=False,
    )
    response.raise_for_status()
    payload = response.json()
    if not isinstance(payload, list):
        raise ValueError("Expected a Canvas course list")
    return [
        course["name"]
        for course in payload
        if isinstance(course, dict) and isinstance(course.get("name"), str)
    ]


def main() -> None:
    try:
        settings = CanvasSettings()
    except ValidationError:
        print("Configure a valid Canvas origin and token in the local .env file.")
        raise SystemExit(1) from None
    try:
        with httpx.Client() as client:
            names = fetch_course_names(settings, client)
    except httpx.HTTPStatusError as error:
        print(f"Canvas returned HTTP {error.response.status_code}.")
        raise SystemExit(1) from None
    except (httpx.RequestError, ValueError):
        print("Canvas connection or response check failed.")
        raise SystemExit(1) from None
    print("Course names from the first response page:")
    for name in names:
        print(name)
    if not names:
        print("No named courses were returned on this page.")


if __name__ == "__main__":
    main()
```

- [ ] Run the connection test, all current tests, and Ruff. Use a synthetic 401 response in a follow-up test to confirm the checked request raises an HTTP status error. Use a synthetic non-list response to confirm the documented ValueError.
- [ ] Have the user run `python -m scripts.check_canvas` with their locally configured settings. Inspect only safe status output and course names. Confirm success from the actual response. If denied, diagnose status and institution access before proceeding.
- [ ] Update `docs/progress.md` with the actual interpreter, package check, tests and real connection result. Never record a predicted test count as an observed result.

## Completion and next milestone

M1 completes only when settings checks pass and the user's real course names have been displayed. An empty first-page response requires further inspection and does not establish the target course is accessible.

Next write a bounded M2 plan for same-origin pagination, selected course-file discovery and safe downloads, based on the actual Canvas response shape. M2 must test multiple pages, denied access, retry bounds, foreign-origin links, oversized downloads and successful file provenance. Do not implement later retrieval or generation code during M1.

## Plan self review

The plan covers environment verification, incremental dependencies, credential exclusion, validated settings and a real first-page connection. Public names and signatures are consistent across tests and examples. The core build guide remains the scope authority; this milestone deliberately excludes complete discovery, documents, storage and models. Exact model and provider choices are made at their own milestones rather than assumed here.
