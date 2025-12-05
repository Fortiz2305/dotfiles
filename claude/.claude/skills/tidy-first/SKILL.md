---
name: tidy-first
description: Execute structural refactoring with test verification before behavioral changes - use when tidying code before features, cleaning up duplication, or improving clarity
---

# Tidy First Skill

Execute structural changes BEFORE behavioral changes, following Kent Beck's Tidy First approach.

## What Qualifies as "Structural"?

Structural changes rearrange code **WITHOUT changing behavior**:

✅ **Structural (Tidy First)**:
- Rename variables, functions, classes for clarity
- Extract methods/functions to reduce duplication
- Move code to different files/modules for better organization
- Reorder code for readability
- Add type annotations
- Split large functions into smaller ones
- Consolidate duplicate code

❌ **NOT Structural (Behavioral)**:
- Add new features
- Fix bugs
- Change logic or algorithms
- Modify API contracts
- Add/remove parameters
- Change return types

## The Tidy First Process

### 1. Capture Baseline

Before making ANY structural change, run tests to capture baseline:

```bash
# Run all tests
npm test

# Verify TypeScript compilation
tsc -b --noEmit
```

**All tests MUST pass** before proceeding. If tests fail, fix them first.

### 2. Make ONE Structural Change

Make exactly ONE type of structural change at a time:
- Rename **OR** extract **OR** move (not multiple types at once)
- Keep the change focused and small

**Examples of ONE change**:
- Rename `processData` to `validateUserInput` across all files
- Extract duplicate validation logic into `validateInput()` function
- Move all repository interfaces to `domain/repositories/` folder

### 3. Verify No Behavior Change

After the structural change, run tests again:

```bash
# Run all tests
npm test

# Verify TypeScript compilation
tsc -b --noEmit
```

**Tests MUST still pass** - this proves you didn't change behavior.

If tests fail:
- ❌ You accidentally changed behavior
- ⚠️ Revert the change and try again more carefully

### 4. Commit Separately

Commit the structural change with `refactor:` type:

```bash
git commit -m "refactor: rename processData to validateUserInput for clarity"
git commit -m "refactor: extract duplicate validation into validateInput"
git commit -m "refactor: move repository interfaces to domain layer"
```

## Critical Rules

- ✅ ALL structural changes BEFORE behavioral changes
- ✅ ONE structural change type per commit
- ✅ Tests must pass BEFORE the change
- ✅ Tests must pass AFTER the change
- ✅ If tests fail after, you changed behavior - REVERT
- ✅ Use `refactor:` commit type for structural changes
- ✅ Never mix structural and behavioral in same commit

## Why Separate Structural from Behavioral?

1. **Easier to review**: Reviewers can see structure changes vs logic changes separately
2. **Safer**: If behavior breaks, you know it's from behavioral commits, not structural
3. **Easier to revert**: Can revert behavioral changes without losing structural improvements
4. **Better history**: Git history shows clear progression of work
5. **Less risky**: Structural changes with passing tests are provably safe

## Example Workflow

```bash
# WRONG: Mixed structural + behavioral
git commit -m "Add user validation and rename variables"  # ❌

# RIGHT: Separate commits
git commit -m "refactor: rename userData to userInput for clarity"  # ✅
git commit -m "feat: add user validation logic"  # ✅
```
