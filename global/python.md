# Python Coding Rules

A practical style guide for keeping this codebase clean and consistent.
Based on PEP 8 / Google Python Style Guide, adapted for day-to-day use.

## 1. Formatting

- Use **4 spaces** per indentation level. Never tabs.
- Max line length: **88 characters** (Black's default) or 79 if you prefer strict PEP 8.
- Use a formatter (Black or Ruff format) and run it before every commit — don't format by hand.
- One blank line between methods, two blank lines between top-level functions/classes.
- No trailing whitespace, no trailing commas debates — let the formatter decide.

## 2. Naming

| Type | Convention | Example |
|---|---|---|
| Variables / functions | `snake_case` | `user_count`, `get_data()` |
| Classes | `PascalCase` | `UserProfile` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Private/internal | leading underscore | `_internal_helper()` |
| Modules/packages | short, lowercase | `utils.py`, `network/` |

- Names should say **what**, not **how**: `filtered_users`, not `list2`.
- Avoid single-letter names except in short loops (`i`, `j`) or math contexts.
- Boolean names read like yes/no questions: `is_valid`, `has_permission`.

## 3. Imports

- Order: standard library → third-party → local application, each group separated by a blank line.
- No wildcard imports (`from module import *`).
- One import per line; avoid unused imports (let Ruff/flake8 catch these).
- Prefer absolute imports over relative ones unless within a tightly-coupled package.

## 4. Functions

- **One function, one responsibility.** If you need "and" to describe it, split it.
- Keep functions short — a good target is **under ~40 lines**; if it's longer, look for a natural split.
- Limit parameters (~4-5 max); beyond that, use a dataclass/config object.
- Always use type hints for parameters and return values:
  ```python
  def get_user(user_id: int) -> User | None:
      ...
  ```
- Avoid mutable default arguments (`def f(items=[])` is a bug waiting to happen — use `None` and initialize inside).
- Early-return / guard clauses over deep nesting:
  ```python
  # Good
  def process(item):
      if not item:
          return None
      ...

  # Avoid
  def process(item):
      if item:
          ...
          if item.valid:
              ...
  ```

## 5. Classes

- Keep classes focused (Single Responsibility Principle). If a class does networking, parsing, and UI updates, split it.
- Use `@dataclass` for simple data containers instead of hand-written `__init__` boilerplate.
- Prefer composition over inheritance unless there's a genuine "is-a" relationship.
- Keep public API surface small — mark internal methods with a leading underscore.

## 6. Error Handling

- Catch specific exceptions, never bare `except:`.
  ```python
  try:
      value = data["key"]
  except KeyError:
      value = default
  ```
- Don't swallow exceptions silently — log or re-raise.
- Fail fast: validate inputs early rather than letting bad data propagate.
- Use custom exception classes for domain-specific errors instead of generic `Exception`.

## 7. Comments & Docstrings

- Comments explain **why**, not **what** (the code already shows what).
- Every public function/class/module gets a docstring (PEP 257 style):
  ```python
  def fetch_users(active_only: bool = False) -> list[User]:
      """Fetch users from the database.

      Args:
          active_only: If True, only return active users.

      Returns:
          A list of User objects.
      """
  ```
- Delete commented-out code — that's what git history is for.
- No TODO comments without an owner or ticket reference: `# TODO(roman): handle timeout case`.

## 8. Structure & Organization

- **Avoid huge files.** One concern per module/file:
  - **~300 lines** — fine, keep an eye on it.
  - **~500 lines** — warning sign, look for a natural split.
  - **800+ lines** — refactor now; the file is almost certainly doing more than one job.
- If you can't summarize a file's purpose in one sentence, it's doing too much — split it.
- A common split pattern: separate **data/models**, **business logic**, and **I/O (network, UI, DB)** into different files instead of mixing them in one.
- Group related functionality into packages with clear `__init__.py` exports.
- Keep configuration (constants, env vars) separate from logic.
- Avoid circular imports — a sign that module boundaries are wrong.

## 9. Testing

- Every non-trivial function should have at least one test.
- Test names describe behavior: `test_returns_none_when_user_not_found`.
- Arrange-Act-Assert structure in each test.
- Don't test implementation details — test observable behavior.

## 10. General Refactoring Signals

Watch for these signs it's time to refactor:
- A function does more than one thing (hard to name it simply).
- Duplicate logic in 2+ places → extract a shared function.
- Deeply nested `if`/`for` blocks (3+ levels) → flatten with early returns or helper functions.
- Long parameter lists → group into a dataclass.
- Comments explaining confusing code → rewrite the code so it doesn't need the comment.
- Large files mixing unrelated concerns → split into modules.

## 11. Tooling (recommended)

- **Ruff** — linting + import sorting + formatting in one tool.
- **mypy** or **pyright** — static type checking.
- **pytest** — testing framework.
- Set these up in `pyproject.toml` and run them in CI / pre-commit hooks so style is enforced automatically, not manually.

---
*Keep this file updated as the team's conventions evolve — a rules doc that's out of date is worse than none.*
