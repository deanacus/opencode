---
name: testing
description: Comprehensive testing methodology with coverage priorities and test design principles
---

# Testing Philosophy

## TL;DR

Write tests that catch bugs and document behavior. Prioritize critical paths over coverage metrics. Follow project conventions. One behavior per test.

## When to Use This Skill

- When writing tests for new features (TDD or verification)
- When using the `/test` command
- When asked to add test coverage
- Before reporting implementation completion (verify tests exist)

## The Testing Pyramid

### Unit Tests (Foundation - 70%)

- Test functions/methods in isolation
- Mock external dependencies
- Fast execution (<100ms per test)
- Single responsibility per test

### Integration Tests (Middle - 20%)

- Test components working together
- Validate data flow between modules
- Mock external services (DB, API)
- Verify contracts at boundaries

### E2E Tests (Top - 10%)

- Test critical user workflows only
- Keep minimal (expensive to maintain)
- Focus on happy paths
- Reserve for high-value scenarios

## Coverage Priorities

Test in this order:

| Priority | Category       | Examples                                 |
| -------- | -------------- | ---------------------------------------- |
| 1        | Critical paths | Core business logic, payment flows, auth |
| 2        | Error handlers | Failure modes, recovery, cleanup         |
| 3        | Edge cases     | Boundaries, empty values, limits         |
| 4        | Public APIs    | Exported interfaces, contracts           |
| 5        | Complex logic  | Algorithms, calculations, state machines |

**Don't chase 100% coverage.** Meaningful tests > metric optimization.

## Test Design Principles

### AAA Pattern

Every test follows Arrange-Act-Assert:

```
Arrange: Set up preconditions and inputs
Act:     Execute the code under test
Assert:  Verify expected outcomes
```

### One Behavior Per Test

- Each test verifies a single behavior
- Multiple assertions are OK if verifying one behavior
- If a test fails, you should know exactly what broke

### Descriptive Names

Test names should read as sentences describing expected behavior:

```ts
// Bad - vague, no context
it('handles error', () => { ... })
it('works correctly', () => { ... })
test('user test', () => { ... })

// Good - describes behavior and conditions
it('throws ValidationError when email format is invalid', () => { ... })
it('creates user with hashed password', () => { ... })
it('returns 404 when resource not found', () => { ... })

// With describe blocks for context
describe('UserService', () => {
  describe('createUser', () => {
    it('hashes password before storing', () => { ... })
    it('throws if email already exists', () => { ... })
  })
})
```

### Independent Tests

- Tests run in any order
- No shared mutable state between tests
- Each test sets up its own fixtures
- Cleanup after each test

## What to Test

### Always Test

- Business logic and domain rules
- Input validation and sanitization
- Error handling and recovery
- State transitions and mutations
- API contracts (inputs/outputs)

### Don't Over-Test

- Framework code (React, Express, etc.)
- Trivial getters/setters
- Third-party library internals
- Implementation details (test behavior instead)
- Auto-generated code

## Mocking Guidelines

### Mock These

- External APIs and services
- Database operations
- File system operations
- Network requests
- Time and randomness
- Environment variables

### Don't Mock These

- The code you're testing
- Simple data transformations
- Pure functions without side effects
- Collaborators when testing integration

### Mock Quality

- Mocks should behave like the real thing
- Update mocks when real behavior changes
- Prefer fakes over stubs for complex behavior
- Verify mock interactions when behavior matters

## Two Testing Modes

### TDD Mode (Before Implementation)

Tests define expected behavior:

1. Write failing test first
2. Tests document API design
3. Expect RED (no implementation yet)
4. Guide implementation to make tests pass

### Verification Mode (After Implementation)

Tests verify working code:

1. Read implementation first
2. Tests should PASS
3. Catch regressions
4. Find gaps in existing coverage

## Framework Adaptation

Discover patterns from existing tests:

- **Organization**: describe/it, test suites, subtests
- **Setup/teardown**: fixtures, beforeEach, afterAll
- **Assertions**: expect, assert, matchers
- **Mocking**: jest.mock, sinon, unittest.mock

Match the project's existing conventions exactly.

## Output Format

After writing tests, report:

1. **Files Created** - Test files written with paths
2. **Test Cases** - Key scenarios covered (bulleted list)
3. **Results** - Pass/fail summary (verification mode)
4. **Coverage** - What's tested vs gaps identified
5. **Issues Found** - Bugs discovered during testing
6. **Next Steps** - What's needed (TDD: implementation; Verification: additional tests)

## What NOT to Do

- Do NOT modify implementation code (report bugs instead)
- Do NOT write tests for trivial code
- Do NOT mock the code under test
- Do NOT create test dependencies
- Do NOT ignore existing test patterns
- Do NOT chase coverage metrics over meaningful tests

## Adherence Checklist

Before completing test writing, verify:

- [ ] Tests follow project conventions (framework, structure, naming)
- [ ] AAA pattern used consistently
- [ ] One behavior per test
- [ ] Descriptive test names
- [ ] Critical paths covered first
- [ ] Mocks used appropriately
- [ ] Tests are independent (no shared state)
- [ ] Results reported in standard format
