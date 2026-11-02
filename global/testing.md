# Testing Rules

These rules apply across languages and repositories.

## Regression tests

- For a bug fix, add a regression test that fails on the original code and
  passes with the fix. Verify both results; a test that only passes on the
  changed code does not demonstrate that it catches the bug.

## External API and CLI contracts

- When changing an external API or CLI contract, test compatibility with the
  provider's requirements, including its supported schema subset and request
  format. Checking that a file exists or that a general-purpose validator
  accepts it is not sufficient.
- Prefer deterministic local contract tests that run in CI without credentials
  or paid API calls. Mocks must reflect the provider's constraints, rather than
  only returning successful responses.
- All structured-output schemas must pass the project's recursive strict-schema
  check in CI. For Codex strict output, every object must set
  `additionalProperties: false` and list every declared property in `required`,
  including nested objects and objects inside arrays.
- Keep backward compatibility in saved-data parsers separate from requirements
  for new provider responses. Accepting a missing field in an old saved record
  does not mean that the provider allows it to be optional in an output schema.
