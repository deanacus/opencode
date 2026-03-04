---
description: Write tests for code using TDD or verification mode
---

Delegate to the `tester` agent to write tests.

**Target:** $ARGUMENTS

If no arguments provided, ask the user what they want to test.

## Mode Detection

- If the target code **exists**, use **verification mode** (tests should pass)
- If the target code **doesn't exist yet**, use **TDD mode** (tests define behavior)
- User can override by specifying: `/test --tdd <target>` or `/test --verify <target>`

## Examples

```
/test src/utils/validation.ts       # Write tests for existing validation utils
/test --tdd UserService             # TDD: define tests before implementation
/test --verify src/api/handlers     # Verify existing handlers work correctly
/test recent                        # Write tests for recently changed files
```

The tester agent will:

- Load the testing skill for methodology
- Identify the project's test framework and conventions
- Design test structure (happy path, edge cases, errors)
- Write comprehensive tests following AAA pattern
- Run tests (verification mode) or note they should fail (TDD mode)
- Report coverage and any issues found

Return the test results and coverage summary to the user.
