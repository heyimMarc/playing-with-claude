# Bug Fixer

Intelligent bug hunting and fixing workflow with systematic investigation, root cause analysis, and regression test generation.

## Usage

```
/fix-bug "Description of the bug or error message"
/fix-bug --hotfix "Critical issue needing quick fix"
/fix-bug --investigate-only "Just find the cause, don't fix"
```

## Flags

| Flag | Description |
|------|-------------|
| `--hotfix` | Minimal fix, skip extensive analysis |
| `--investigate-only` | Find root cause but don't implement fix |
| `--security` | Treat as potential security vulnerability |

---

## Internal Implementation

When invoked with: $ARGUMENTS

### Step 1: Create Bug Report File

```
Create: cc-plans/bugs/<bug-slug>.md

# Bug: <Summary>
Reported: <timestamp>
Status: Investigating

## Description
$ARGUMENTS

## Investigation Log
[Will be populated during investigation]
```

### Step 2: Update Todo List

```
Use TodoWrite:
1. Gather error information
2. Reproduce the issue
3. Identify root cause
4. Implement fix (unless --investigate-only)
5. Create regression test
6. Verify fix
```

### Step 3: Investigation Phase

```
Launch bug-hunter agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Investigate this bug: $ARGUMENTS

Investigation steps:
1. Search for error messages in logs/code
2. Find related code paths
3. Identify potential failure points
4. Create reproduction scenario

Use Task tool with 'Explore' subagent to search codebase.
Use Grep to find error messages.
Use Read to examine suspicious code.

Return:
- Likely file(s) containing the bug
- Reproduction steps
- Root cause hypothesis
- Evidence supporting hypothesis
- Similar patterns in codebase"
```

### Step 4: Git Bisect (if applicable)

```
If the bug is a regression:

1. Find the last known working commit
2. Use git bisect to identify breaking commit
3. Analyze the breaking changes

Commands:
git bisect start
git bisect bad HEAD
git bisect good <last-known-good>
# Continue until culprit found
```

### Step 5: Implement Fix (unless --investigate-only)

```
Launch senior-developer agent (from feature-orchestrator):

Model: claude-haiku-4.5
Context: isolated
Input: Investigation results

Prompt:
"Fix this bug based on investigation:

Root Cause: $ROOT_CAUSE
Location: $FILE_LOCATION
Evidence: $EVIDENCE

Implement the minimal fix that:
1. Addresses the root cause
2. Doesn't introduce side effects
3. Follows existing code patterns

Run tests after fixing.

Return:
- Files modified
- Changes made
- Test results"
```

### Step 6: Create Regression Test

```
Launch test-wizard agent:

Model: claude-haiku-4.5
Context: isolated

Prompt:
"Create regression test for bug:

Bug: $BUG_DESCRIPTION
Root Cause: $ROOT_CAUSE
Fix Applied: $FIX_SUMMARY

Create a test that:
1. Would have caught this bug
2. Verifies the fix works
3. Prevents recurrence

Match existing test patterns in codebase.

Return:
- Test file location
- Test implementation
- Test verification"
```

### Step 7: Verify and Report

```
Run full test suite.
Update bug report with results.

## Resolution

### Root Cause
[Technical explanation]

### Fix Applied
- File: [path]
- Change: [description]

### Regression Test
- Test: [name]
- Location: [path]

### Verification
- Tests passing: X/Y
- Manual verification: [if needed]

### Similar Issues Found
[List any related bugs that should be checked]
```

### Step 8: Summary Output

```
Provide summary:

🐛 Bug Fixed: <summary>

📍 Root Cause: <location>:<line>
   [Brief explanation]

🔧 Fix Applied:
   - Modified: [files]
   - Added: [regression test]

✅ Verification:
   - Tests: Passing
   - Build: Success

📄 Full report: cc-plans/bugs/<bug-slug>.md
```

---

## Security Bug Handling

If --security flag or security-related bug detected:

1. Mark as security issue in report
2. Check for similar vulnerabilities
3. Do NOT commit detailed vulnerability info
4. Recommend security review
5. Consider disclosure process

---

## Error Handling

- **Cannot Reproduce**: Document attempts, ask for more info
- **Multiple Causes**: Investigate each, prioritize
- **Breaking Fix**: Revert, investigate further
- **Related Bugs Found**: Create separate tracking

---

## Preferred MCP Tools

When available, use these MCP tools instead of Bash equivalents:

| Task | Use MCP Tool | Instead of |
|------|--------------|------------|
| Git blame/bisect | `git_blame`, `git_bisect` | `git` CLI |
| Security check | `semgrep_scan` | Manual review |
| Code search | `semantic_search` | `grep`/`Grep` tool |
