# Refactor Pro

Safe code refactoring with architectural analysis, before/after comparisons, metrics tracking, and automatic regression testing.

## Usage

```
/refactor "path/to/file.java"
/refactor --extract "Extract validation logic from UserService"
/refactor --simplify "Reduce complexity in OrderProcessor"
/refactor --pattern "Apply Strategy pattern to payment handling"
```

## Flags

| Flag | Description |
|------|-------------|
| `--extract` | Extract method, class, or module |
| `--rename` | Rename with all references |
| `--simplify` | Reduce complexity |
| `--pattern` | Apply design pattern |
| `--clean-arch` | Enforce Clean Architecture layers |
| `--dry-run` | Show plan without executing |

---

## Internal Implementation

When invoked with: $ARGUMENTS

### Step 1: Create Git Checkpoint

```
Before any refactoring:

git stash push -m "refactor-checkpoint-$(date +%s)"

This allows safe rollback if refactoring fails.
```

### Step 2: Analyze Current State

```
Launch architect agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Analyze code for refactoring: $ARGUMENTS

Assess:
1. Current structure and dependencies
2. Complexity metrics (cyclomatic, cognitive)
3. Code smells present
4. Test coverage status
5. Coupling and cohesion

IMPORTANT: Use Task tool with 'Explore' to find all usages and dependencies.

Return:
- Current metrics (lines, complexity, coupling)
- Identified issues
- Dependencies that will be affected
- Risks of refactoring"
```

### Step 3: Create Refactoring Plan

```
Launch clean-code-nerd agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Create refactoring plan for: $ARGUMENTS

Based on analysis:
$ANALYSIS_RESULTS

Design refactoring approach that:
1. Improves readability
2. Reduces complexity
3. Maintains behavior
4. Preserves tests

Return:
- Step-by-step refactoring plan
- Expected improvements (metrics)
- Files to modify
- Breaking changes (if any)"
```

### Step 4: Update Plan File

```
Create: cc-plans/refactors/<refactor-slug>.md

# Refactoring: <Target>
Date: <timestamp>
Status: Planning

## Before Metrics
- Lines: X
- Complexity: Y
- Coupling: Z

## Refactoring Plan
[Steps from planning phase]

## Expected After Metrics
- Lines: X'
- Complexity: Y'
- Coupling: Z'
```

### Step 5: Execute Refactoring (unless --dry-run)

```
Launch senior-developer agent:

Model: claude-haiku-4.5
Context: isolated

Prompt:
"Execute refactoring plan:

$REFACTORING_PLAN

Steps:
1. Make changes incrementally
2. Run tests after each step
3. Commit each successful step
4. Track what changed

IMPORTANT: Stop if tests fail. Do not proceed with broken tests.

Return:
- Changes made
- Tests status after each step
- Any issues encountered"
```

### Step 6: Run Full Test Suite

```
After refactoring complete:

./gradlew test  # or appropriate test command

If tests fail:
1. Analyze failures
2. Attempt to fix
3. If cannot fix, rollback:
   git stash pop
```

### Step 7: Measure After Metrics

```
Calculate new metrics:
- Lines of code
- Cyclomatic complexity
- Cognitive complexity
- Coupling metrics
- Test coverage

Compare before vs after.
```

### Step 8: Update Plan and Report

```
## After Metrics
- Lines: X' (Δ -15%)
- Complexity: Y' (Δ -30%)
- Coupling: Z' (Δ -20%)

## Changes Made
| File | Change | Impact |
|------|--------|--------|
| ... | ... | ... |

## Verification
- All tests passing: ✅
- No breaking changes: ✅
- Metrics improved: ✅
```

### Step 9: Summary Output

```
✨ Refactoring Complete

📊 Metrics Improvement:
   - Lines: X → X' (-15%)
   - Complexity: Y → Y' (-30%)

📁 Files Modified: N files

✅ Tests: All passing

📄 Full report: cc-plans/refactors/<slug>.md

💡 Rollback: git stash pop (if needed)
```

---

## Refactoring Types

### Extract (--extract)
- Extract Method: Pull out reusable logic
- Extract Class: Separate responsibilities
- Extract Interface: Define contract

### Simplify (--simplify)
- Remove dead code
- Flatten nested conditionals
- Replace conditionals with polymorphism
- Consolidate duplicated code

### Pattern (--pattern)
- Strategy: Replace switch with objects
- Factory: Centralize object creation
- Builder: Simplify complex construction
- Decorator: Add behavior without inheritance

### Clean Architecture (--clean-arch)
- Move domain logic to domain layer
- Extract use cases from controllers
- Separate infrastructure concerns
- Define proper boundaries

---

## Safety Measures

1. **Git checkpoint before start**
2. **Tests must pass before proceeding**
3. **Incremental changes with verification**
4. **Automatic rollback on failure**
5. **Metrics comparison to verify improvement**
