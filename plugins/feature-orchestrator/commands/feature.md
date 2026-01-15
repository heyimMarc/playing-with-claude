# Feature Orchestrator

Intelligent feature development with multi-agent orchestration. Describe your feature and watch intelligent agents collaborate to design, implement, and test it.

## Usage

```
/feature "Your feature description"
/feature --fast "Simple feature"
/feature --parallel "Complex feature with multiple independent parts"
```

## Flags

| Flag | Description |
|------|-------------|
| `--fast` | Skip requirements analysis, go straight to implementation |
| `--parallel` | Run compatible agents simultaneously |
| `--no-tests` | Skip test generation (not recommended) |
| `--secure` | Force security review even for simple features |
| `--yolo` | Chaos mode: fast + no-tests + no-review |

---

## Internal Implementation

When invoked with: $ARGUMENTS

### Step 1: Parse Arguments and Analyze Complexity

```
Parse flags: --fast, --parallel, --no-tests, --secure, --yolo

Analyze the feature description to determine complexity:
- SIMPLE: Single file change, straightforward logic
- MEDIUM: Multiple files, some business logic
- COMPLEX: Multiple components, architectural decisions
- EPIC: Cross-service, major architectural changes

Based on complexity, select which agents to use:
- SIMPLE: senior-developer only (with tests unless --no-tests)
- MEDIUM: requirements-engineer → senior-developer → test-wizard
- COMPLEX: Full orchestra
- EPIC: Full orchestra with architect using Opus
```

### Step 2: Create Plan File

```
Create plan file at: <project>/cc-plans/<feature-slug>.md

Initialize with header:
# Feature: <Feature Name>
Generated: <timestamp>
Complexity: <SIMPLE|MEDIUM|COMPLEX|EPIC>
Status: In Progress

## Progress
- [ ] Requirements Analysis
- [ ] Architecture Design
- [ ] Test Strategy
- [ ] Implementation
- [ ] Security Review
- [ ] Code Quality Review
```

### Step 3: Update Todo List

```
Use TodoWrite to create tracking:
1. Analyze requirements (unless --fast)
2. Design architecture (if COMPLEX+)
3. Create test strategy (unless --no-tests)
4. Implement feature
5. Security review (if auth/data/API or --secure)
6. Code quality review
7. Run tests
8. Update plan with results
```

### Step 4: Execute Agent Orchestra

#### Phase 1: Requirements (unless --fast)
```
Launch requirements-engineer agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Analyze and document requirements for: $ARGUMENTS

Create comprehensive requirements including:
- User stories with acceptance criteria
- Domain concepts
- Edge cases
- Non-functional requirements

IMPORTANT: If you need to understand existing code patterns, use the Task tool
with subagent_type 'Explore' to search the codebase first.

Return structured output with:
- Feature summary (2-3 sentences)
- Acceptance criteria (testable)
- Edge cases identified
- Dependencies on existing code
- Complexity assessment: SIMPLE, MEDIUM, COMPLEX, or EPIC"
```

#### Phase 2: Architecture (if COMPLEX or EPIC)
```
Launch architect agent:

Model: claude-opus-4.5 (for EPIC) or claude-sonnet-4.5 (for COMPLEX)
Context: isolated
Input: Requirements from Phase 1

Prompt:
"Design architecture based on these requirements:
$REQUIREMENTS_SUMMARY

Follow Clean Architecture principles.
Identify all files to create and modify.
Document key architectural decisions.

IMPORTANT: Search existing codebase for patterns to follow.
Use Task tool with 'Explore' subagent to understand current architecture.

Return:
- Component design
- Layer structure
- Files to create/modify
- Integration points
- Key decisions with rationale"
```

#### Phase 3: Test Strategy (unless --no-tests or --yolo)
```
Can run in parallel with Phase 2 if --parallel flag is set.

Launch test-wizard agent:

Model: claude-haiku-4.5
Context: isolated
Input: Requirements (or Architecture if available)

Prompt:
"Create test strategy for: $FEATURE_SUMMARY

Include:
- Unit tests for domain logic
- Integration tests for persistence/APIs
- Edge case tests

IMPORTANT: Search codebase for existing test patterns.
Match existing test style (Spock, JUnit, etc.).

Return:
- Test list with descriptions
- Test data builders needed
- Mocking strategy
- Coverage targets"
```

#### Phase 4: Implementation
```
Launch senior-developer agent:

Model: claude-sonnet-4.5
Context: isolated
Input: Architecture + Test Strategy

Prompt:
"Implement the feature based on:

Architecture: $ARCHITECTURE_SUMMARY
Test Strategy: $TEST_STRATEGY_SUMMARY

Follow TDD if tests are defined:
1. Write failing tests first
2. Implement code to pass tests
3. Refactor for quality

IMPORTANT:
- Search codebase for patterns before implementing
- Follow existing conventions
- Run tests after implementation
- Run code formatter (spotlessApply, prettier, etc.)

Return:
- Files created
- Files modified
- Test results
- Commands executed"
```

#### Phase 5: Security Review (if auth/data/API or --secure)
```
Automatically triggered if feature involves:
- Authentication/Authorization
- User data handling
- API endpoints
- File operations
- Database queries with user input

Launch security-expert agent:

Model: claude-sonnet-4.5
Context: isolated
Input: Files modified

Prompt:
"Security review for: $FILES_MODIFIED

Check for:
- OWASP Top 10 vulnerabilities
- Input validation
- Authorization enforcement
- Sensitive data exposure
- Injection risks

Return:
- Critical issues (must fix)
- Warnings (should fix)
- Passed checks
- Recommendations"
```

#### Phase 6: Code Quality Review
```
Launch clean-code-nerd agent:

Model: claude-haiku-4.5
Context: isolated
Input: Files modified

Prompt:
"Review code quality for: $FILES_MODIFIED

Check for:
- Naming clarity
- Function size and complexity
- Code duplication
- Self-documenting patterns

Return:
- Issues found
- Naming improvements
- Refactoring suggestions
- Overall assessment"
```

### Step 5: Run Tests

```
After implementation, run test suite:

For Java/Gradle: ./gradlew test
For Java/Maven: ./mvnw test
For Node: npm test
For Python: pytest

Report results in plan file.
```

### Step 6: Update Plan with Results

```
Update cc-plans/<feature-slug>.md with:

## Results

### Files Changed
- Created: [list]
- Modified: [list]

### Test Results
- Passing: X/Y
- Coverage: Z%

### Security Review
- Issues: [list or "None"]

### Code Quality
- Grade: [A-F]
- Suggestions: [list]

### Next Steps
- [ ] Manual testing needed
- [ ] Documentation updates
- [ ] PR ready for review
```

### Step 7: Summary Output

```
Provide user with summary:

✅ Feature: <name> implemented

📁 Files: X created, Y modified
🧪 Tests: X passing, Y% coverage
🔒 Security: [Clean | X issues found]
📝 Quality: Grade [A-F]

📄 Full plan: cc-plans/<feature-slug>.md

🚨 Action Items:
- [Any manual steps needed]
- [Issues to address]
```

---

## Error Handling

- **Agent Failure**: Retry once, then report error with context
- **Test Failure**: Run in debug mode, provide failure analysis
- **Build Failure**: Attempt auto-fix, then escalate
- **Security Issues**: Block completion until critical issues resolved

## Cost Optimization

- Use Haiku for implementation and review (90% capability, 3x cheaper)
- Use Sonnet for requirements and security (needs deeper reasoning)
- Use Opus only for EPIC complexity architecture
- Return artifacts, not reasoning chains
- Recommend /clear after completion

---

## Preferred MCP Tools

When available, use these MCP tools instead of Bash equivalents:

| Task | Use MCP Tool | Instead of |
|------|--------------|------------|
| Git operations | `git_status`, `git_commit`, `git_branch`, `git_diff` | `git` CLI |
| Security scan | `semgrep_scan`, `security_check` | Manual review |
| Database queries | `execute_sql` | CLI clients |
| Build tasks | Gradle MCP tools | `./gradlew` CLI |
| Code search | `semantic_search` (Code Context MCP) | `grep`/`Grep` tool |
| Container ops | `list_containers`, `deploy_compose` | `docker` CLI |
