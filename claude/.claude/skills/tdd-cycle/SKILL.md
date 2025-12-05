---
name: tdd-cycle
description: Use when implementing any feature or bugfix, before writing implementation code. Execute strict Red-Green-Refactor TDD cycle following industry testing standards. Write the test first, watch it fail, write minimal code to pass; ensures tests actually verify behavior by requiring failure first.
---

# TDD Cycle Skill

Execute ONE Red-Green-Refactor cycle following testing best practices.

## Prerequisites

**Reference**: See `testing-reference.md` (in this folder) for complete testing patterns and guidelines.

## The Cycle

### RED Phase (Write Failing Test)

1. **Write ONE failing test**:
   - Use Given-When-Then structure (see `testing-reference.md`)
   - Format: `it('should [behavior] when [condition]', () => { ... })`
   - **Check for test utilities** FIRST - use existing factories/builders when available
   - Avoid manually constructing complex test data

2. **Run the test** - verify it fails for the RIGHT reason:
   ```bash
   # Run your project's test command
   npm test path/to/test
   pytest path/to/test.py
   ```
   (Adjust to your project's test framework)

3. **Verify failure message** makes sense - if not, improve the test

### GREEN Phase (Make It Pass)

4. **Write minimum code** to make the test pass:
   - Follow your project's architecture and conventions
   - Write only what's needed to pass the test
   - Don't add extra features "while you're there"

5. **Run the test again** - verify it passes

6. **Run full test suite** - verify no regressions:
   ```bash
   # Run your full test suite
   npm test
   pytest
   ```

### REFACTOR Phase (Improve Structure)

7. **Look for improvements** (only if needed):
   - Remove duplication
   - Improve naming for clarity
   - Extract methods/functions
   - Simplify conditionals
   - Improve type definitions

8. **Run tests after EACH refactoring** - must stay green

9. **Commit the change**:
   ```bash
   # Use feat: for new features
   git commit -m "feat: [description]"

   # Use fix: for bug fixes
   git commit -m "fix: [description]"

   # Use test: for test additions
   git commit -m "test: [description]"
   ```

## Critical Rules

- ✅ Write ONE test at a time (never batch multiple tests)
- ✅ Test MUST fail before implementation (verify RED phase)
- ✅ Write MINIMUM code to pass (resist gold-plating)
- ✅ Run tests after EVERY change
- ✅ Use test utilities (factories/builders) when available
- ✅ Follow Given-When-Then with blank lines, NO comments

## Example Cycle

```typescript
// RED: Write failing test
describe('ShoppingCart', () => {
  it('should calculate total when items are added', () => {
    const cart = new ShoppingCart();
    const item1 = { id: '1', price: 10.00 };
    const item2 = { id: '2', price: 15.50 };

    cart.addItem(item1);
    cart.addItem(item2);

    expect(cart.getTotal()).toBe(25.50);
  });
});

// GREEN: Implement minimum code to pass
// REFACTOR: Improve code quality while tests stay green
```

## When to Repeat

After completing one RED-GREEN-REFACTOR cycle:
- If there's more functionality to add, start a new cycle
- If the feature is complete, mark the todo as completed
- Each cycle should be small and focused on one behavior
