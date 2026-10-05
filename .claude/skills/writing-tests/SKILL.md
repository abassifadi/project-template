---
name: writing-tests
description: Designs and writes automated tests (unit, integration, contract, end-to-end) using the test pyramid, test-first development, and deterministic fixtures. Use when adding tests, raising coverage, doing TDD, fixing flaky tests, or when the user asks "how should I test this".
metadata:
  role: senior-developer
  version: "1.0"
---

# Writing tests

Tests exist to let people change code with confidence. Optimize for: fails when behavior breaks, passes when only implementation changes, runs fast, never flakes.

## Choose the level

| Level | Tests | Speed | Use for |
|-------|-------|-------|---------|
| Unit | One function/class, no I/O | ms | Business rules, edge cases, pure logic |
| Integration | Real DB/queue/filesystem in a container | s | Queries, transactions, serialization, adapters |
| Contract | Consumer/provider API agreement | s | Service boundaries (e.g. Pact, OpenAPI schema checks) |
| End-to-end | Whole system via UI/API | min | A few critical user journeys only |

Default shape: many unit tests, fewer integration tests, a handful of E2E. Test behavior through public interfaces; do not test private methods.

## Workflow (test-first)

1. **Detect the project's conventions.** Find the test runner, folder layout, naming, fixtures, and mocking library already in use. Match them. Do not introduce a new framework.
2. **List behaviors** as one-line specs before writing code:
   `returns 0 for empty cart`, `rejects negative quantity`, `applies discount once per order`.
3. **Red:** write one failing test for the next behavior. Run it; confirm it fails for the right reason.
4. **Green:** write the minimum code to pass.
5. **Refactor** with the test green. Repeat.
6. **Run the whole suite** before finishing. Report the exact command and result.

## Test structure

Use Arrange–Act–Assert, one behavior per test, name says the behavior:

```python
def test_rejects_order_when_quantity_is_negative():
    cart = Cart()                                   # Arrange
    with pytest.raises(ValueError, match="quantity"):
        cart.add(item="sku-1", quantity=-1)          # Act + Assert
```

## Edge-case checklist

For each function under test, consider: empty / null / missing, boundary values (0, 1, max, max+1), invalid types and formats, duplicates, ordering, unicode and locale, time zones and DST, concurrency, partial failure and retries, large inputs.

## Determinism rules

- Inject clocks, random seeds, and UUID generators; never call `now()` directly in logic under test.
- No `sleep` to wait for async work; await the condition or use fake timers.
- No real network calls in unit tests. Use real dependencies (Testcontainers) for integration tests instead of mocking the database.
- Each test sets up and cleans up its own data; tests must pass in any order and in parallel.
- Mock only at system boundaries you own the interface of. Over-mocking tests the mock, not the code.

## Fixing a flaky test

1. Reproduce: run it in a loop (`pytest -x --count=200`, `go test -count=200`, `jest --runInBand` repeated).
2. Classify the cause: timing, shared state, ordering, external dependency, resource leak.
3. Fix the cause; never just add retries or increase timeouts.

## Coverage

Coverage is a smell detector, not a goal. Check that changed lines and risky branches are covered; do not write assertion-free tests to raise the number. Consider mutation testing (Stryker, PIT, mutmut) for critical modules.
