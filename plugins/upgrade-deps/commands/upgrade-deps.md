# Upgrade Dependencies

Safe dependency upgrades with breaking change detection, security scanning, and test verification for Gradle, Maven, npm, and more.

## Usage

```
/upgrade-deps
/upgrade-deps --dry-run
/upgrade-deps --security-only
/upgrade-deps --major
/upgrade-deps --lib spring-boot
```

## Flags

| Flag | Description |
|------|-------------|
| `--dry-run` | Show what would be upgraded without making changes |
| `--security-only` | Only apply security patches |
| `--major` | Include major version upgrades (careful!) |
| `--lib <name>` | Upgrade specific library only |
| `--skip-tests` | Skip test verification (not recommended) |

---

## Internal Implementation

When invoked with: $ARGUMENTS

### Step 1: Detect Build System

```
Detect project type:
- build.gradle / build.gradle.kts → Gradle
- pom.xml → Maven
- package.json → npm/pnpm/yarn
- requirements.txt / pyproject.toml → Python
- Cargo.toml → Rust
- go.mod → Go

Set appropriate commands for each system.
```

### Step 2: Create Git Checkpoint

```
git stash push -m "upgrade-deps-checkpoint-$(date +%s)"

This allows safe rollback if upgrades cause issues.
```

### Step 3: Scan Current Dependencies

```
For Gradle:
./gradlew dependencies --configuration compileClasspath

For Maven:
./mvnw dependency:tree

For npm:
npm outdated --json

Parse output to get current versions.
```

### Step 4: Check for Updates

```
For Gradle (with versions plugin):
./gradlew dependencyUpdates

For Maven:
./mvnw versions:display-dependency-updates

For npm:
npm outdated

Categorize updates:
- Patch (x.x.PATCH) - Safe
- Minor (x.MINOR.x) - Usually safe
- Major (MAJOR.x.x) - Breaking changes possible
```

### Step 5: Security Scan

```
Launch security-expert agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Check security advisories for these dependencies:
$DEPENDENCY_LIST

Use WebSearch to check:
- CVE databases
- GitHub security advisories
- Snyk vulnerability database

Return:
- Known vulnerabilities in current versions
- Security fixes in available updates
- Critical updates that should be prioritized"
```

### Step 6: Breaking Change Analysis

```
For each upgrade (especially minor/major):

1. WebSearch for "[library] [version] changelog"
2. WebSearch for "[library] [version] migration guide"
3. WebSearch for "[library] [version] breaking changes"

Compile:
| Library | From | To | Breaking Changes |
|---------|------|----|--------------------|
| [lib] | [v1] | [v2] | [changes or "None"] |
```

### Step 7: Database Impact Analysis (if DB libs updated)

```
If database-related libraries are being upgraded (JPA, Hibernate, Flyway, etc.):

Launch database-expert agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Analyze database impact of these upgrades:
$DB_LIBRARY_UPGRADES

Check:
1. Schema compatibility changes
2. Migration format changes
3. Query behavior changes
4. Transaction handling changes

Return:
- Required migration updates
- Potential data issues
- Recommended upgrade order"
```

### Step 8: Create Upgrade Plan

```
Create: cc-plans/upgrades/<timestamp>.md

# Dependency Upgrade Plan
Date: <timestamp>
Mode: <standard|security-only|major>

## Current State
[List of outdated dependencies]

## Planned Upgrades

### Security Updates (Priority)
| Library | From | To | CVE |
|---------|------|----|----|

### Patch Updates (Safe)
| Library | From | To |
|---------|------|-----|

### Minor Updates
| Library | From | To | Breaking |
|---------|------|-------|----------|

### Major Updates (if --major)
| Library | From | To | Migration Required |
|---------|------|-------|-------------------|

## Risks
[Identified risks and mitigations]
```

### Step 9: Apply Upgrades (unless --dry-run)

```
Apply upgrades in order:
1. Security patches first
2. Patch updates
3. Minor updates
4. Major updates (if --major)

For each update:
1. Update version in build file
2. Run tests
3. If tests pass, continue
4. If tests fail, analyze and fix or rollback
```

### Step 10: Run Full Test Suite

```
./gradlew test  # or appropriate command

If tests fail:
1. Analyze failures
2. Check if related to upgrades
3. Attempt fixes
4. If cannot fix, partial rollback
```

### Step 11: Generate Report

```
## Upgrade Report

### Successfully Upgraded
| Library | From | To | Type |
|---------|------|-------|------|
| spring-boot | 3.1.0 | 3.2.0 | minor |

### Skipped (breaking changes)
| Library | From | To | Reason |
|---------|------|-------|--------|

### Security Fixes Applied
| Library | CVE | Fixed In |
|---------|-----|----------|

### Test Results
- Total: X tests
- Passing: Y
- Failing: Z

### Manual Actions Required
- [ ] Review [specific change]
- [ ] Update [configuration]
```

### Step 12: Summary Output

```
📦 Dependency Upgrade Complete

✅ Upgraded: X libraries
⏭️ Skipped: Y libraries
🔒 Security fixes: Z CVEs addressed

📊 Summary:
| Type | Count |
|------|-------|
| Patch | X |
| Minor | Y |
| Major | Z |

🧪 Tests: All passing

📄 Full report: cc-plans/upgrades/<timestamp>.md

⚠️ Manual Review Needed:
- [Item requiring attention]

🔄 Rollback: git stash pop (if needed)
```

---

## Build System Commands

### Gradle
```bash
# Check updates
./gradlew dependencyUpdates

# Update with versions plugin
./gradlew useLatestVersions

# Specific library
./gradlew dependencyUpdates --dependency-filter spring
```

### Maven
```bash
# Check updates
./mvnw versions:display-dependency-updates

# Update properties
./mvnw versions:update-properties

# Update specific
./mvnw versions:use-latest-versions -Dincludes=org.springframework:*
```

### npm
```bash
# Check outdated
npm outdated

# Update all
npm update

# Update specific
npm update lodash

# Major updates
npx npm-check-updates -u
```

---

## Safety Features

1. **Git checkpoint** before any changes
2. **Incremental updates** with test verification
3. **Breaking change detection** via web research
4. **Security prioritization** for CVE fixes
5. **Automatic rollback** on test failure
6. **Detailed reporting** for review
