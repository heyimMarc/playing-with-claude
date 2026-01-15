# Claude MD Generator

Generates an intelligent CLAUDE.md file by analyzing the codebase structure, tech stack, and fetching current best practices from the internet.

## Usage

```
/init-claude-md
/init-claude-md --minimal "Generate minimal CLAUDE.md"
/init-claude-md --focus "frontend" "Focus on frontend best practices"
/init-claude-md --update "Update existing CLAUDE.md with new best practices"
```

## Flags

| Flag | Description |
|------|-------------|
| `--minimal` | Generate a concise CLAUDE.md without extensive details |
| `--focus` | Focus on specific area (frontend, backend, testing, etc.) |
| `--update` | Update existing CLAUDE.md rather than replace |
| `--no-web` | Skip web research, use only codebase analysis |

---

## Internal Implementation

When invoked with: $ARGUMENTS

### Step 1: Check for Existing CLAUDE.md

```
Check if CLAUDE.md already exists at project root.

If exists and not --update:
  Ask user: "CLAUDE.md already exists. Overwrite, update, or cancel?"

If --update flag set:
  Read existing content for preservation/enhancement.
```

### Step 2: Analyze Codebase Structure

```
Launch codebase-analyzer agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Analyze the codebase to understand its structure and characteristics.

Investigation:
1. Identify project type (web app, API, CLI, library, monorepo)
2. Detect tech stack:
   - Languages (Java, TypeScript, Python, Go, etc.)
   - Frameworks (Spring Boot, React, Next.js, FastAPI, etc.)
   - Build tools (Gradle, Maven, npm, cargo, etc.)
   - Test frameworks (JUnit, Jest, pytest, etc.)
3. Identify architecture patterns (Clean Architecture, MVC, microservices)
4. Find existing code conventions:
   - Naming patterns
   - File organization
   - Import ordering
5. Detect CI/CD configuration
6. Find existing linting/formatting rules

Use Glob to find config files:
- package.json, build.gradle, pom.xml, Cargo.toml, go.mod
- .eslintrc, .prettierrc, checkstyle.xml
- .github/workflows/*, Jenkinsfile, .gitlab-ci.yml
- tsconfig.json, .editorconfig

Return:
- Project type and description
- Tech stack summary
- Architecture patterns detected
- Code conventions found
- Build and test commands
- File structure overview"
```

### Step 2b: Detect Code Patterns and Architecture

```
Launch pattern-detector agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Analyze the codebase for design patterns and architectural styles.

Pattern Detection:

1. **Architectural Patterns**:
   - Clean Architecture / Hexagonal / Ports & Adapters
   - MVC / MVP / MVVM
   - Layered Architecture (controller/service/repository)
   - Microservices / Modular Monolith
   - Event-Driven / CQRS / Event Sourcing
   - Domain-Driven Design (DDD)

2. **Design Patterns in Use**:
   - Creational: Factory, Builder, Singleton
   - Structural: Adapter, Decorator, Facade, Proxy
   - Behavioral: Strategy, Observer, Command, Chain of Responsibility
   - Repository Pattern
   - Dependency Injection patterns

3. **Code Organization Patterns**:
   - Feature-based vs Layer-based structure
   - Barrel exports (index files)
   - Module boundaries
   - Shared/common code organization

Search for indicators:
- Directory names: domain/, application/, infrastructure/, adapters/, ports/
- Class suffixes: *Factory, *Builder, *Strategy, *Handler, *Repository, *Service
- Interface patterns: I* prefix, *able suffix
- Decorator usage: @annotations, decorators
- DI containers: @Inject, @Autowired, constructor injection

Use Grep to find pattern indicators:
- 'implements.*Repository'
- 'extends.*Factory'
- '@Injectable|@Service|@Component'
- 'createBuilder|newBuilder'

Return:
- Detected architectural pattern with confidence level
- Design patterns found with examples
- Code organization style
- Dependency injection approach
- Module/package structure philosophy"
```

### Step 2c: Analyze Code Style and Conventions

```
Launch style-analyzer agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Deep analysis of code style and conventions in this codebase.

Style Detection:

1. **Naming Conventions** (sample 10+ files):
   - Classes: PascalCase, camelCase, snake_case
   - Functions/Methods: camelCase, snake_case, kebab-case
   - Variables: camelCase, snake_case, SCREAMING_SNAKE_CASE for constants
   - Files: kebab-case, camelCase, PascalCase, snake_case
   - Test files: *.test.ts, *.spec.ts, *Test.java, test_*.py
   - Private members: _prefix, #private, no convention

2. **Import/Package Organization**:
   - Absolute vs relative imports
   - Import grouping (stdlib, external, internal)
   - Import ordering (alphabetical, by type)
   - Barrel imports usage

3. **Code Formatting** (from config or inference):
   - Indentation: tabs vs spaces, size
   - Line length limits
   - Brace style: same line, new line
   - Semicolons: always, never (JS/TS)
   - Quotes: single, double
   - Trailing commas

4. **Documentation Style**:
   - JSDoc / JavaDoc / docstrings / rustdoc
   - Comment style (when used)
   - README conventions

5. **Type Conventions** (if applicable):
   - Strict typing vs loose
   - Interface vs Type (TypeScript)
   - Explicit vs inferred types
   - Null handling: Optional, nullable, null-safe operators

Read configuration files:
- .editorconfig
- .prettierrc, prettier.config.js
- .eslintrc, eslint.config.js
- checkstyle.xml, spotless config
- .scalafmt.conf
- rustfmt.toml
- pyproject.toml [tool.black], [tool.ruff]
- .clang-format

Sample actual code files to verify conventions match reality.

Return:
- Naming convention summary with examples
- Import organization rules
- Formatting rules (explicit and inferred)
- Documentation conventions
- Type usage patterns
- Any inconsistencies detected"
```

### Step 2d: Discover Build Tool Tasks

```
Launch build-tool-analyzer agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Discover all available build tasks and commands for this project.

For each detected build tool, extract available tasks:

**Gradle** (build.gradle, build.gradle.kts, settings.gradle):
Run: ./gradlew tasks --all
Parse output for:
- Build tasks: build, clean, assemble
- Test tasks: test, integrationTest, check
- Application tasks: bootRun, run
- Publishing tasks: publish, publishToMavenLocal
- Custom tasks defined in build files
- Subproject tasks: :module:task

**Maven** (pom.xml):
Run: ./mvnw help:describe -Dcmd=compile (for each phase)
Or parse pom.xml for:
- Lifecycle phases: clean, compile, test, package, verify, install, deploy
- Plugin goals: spring-boot:run, flyway:migrate
- Profiles: -P profile-name
- Custom executions

**npm/yarn/pnpm** (package.json):
Read scripts section:
- dev, start, build, test, lint
- Custom scripts
- Pre/post hooks
- Workspace commands (if monorepo)

**Cargo** (Cargo.toml):
Standard commands:
- cargo build, cargo run, cargo test
- cargo clippy, cargo fmt
- cargo doc
- Custom aliases in .cargo/config.toml

**Go** (go.mod, Makefile):
- go build, go test, go run
- Makefile targets
- Task runner commands (task, mage)

**Python** (pyproject.toml, setup.py, Makefile):
- pytest, python -m pytest
- Poetry: poetry run, poetry build
- Hatch: hatch run
- Makefile targets
- tox environments

**Bazel/Buck/Pants** (BUILD, WORKSPACE):
- Build targets
- Test targets
- Query commands

Return structured data:
- Build tool name and version (if detectable)
- All available tasks/commands grouped by category:
  - Development (run, watch, dev server)
  - Build (compile, assemble, package)
  - Test (unit, integration, e2e)
  - Quality (lint, format, check)
  - Deploy (publish, release)
- Most commonly used commands (based on CI config or scripts)
- Task dependencies
- Environment-specific tasks"
```

### Step 3: Web Research for Best Practices

```
Unless --no-web flag:

Use WebSearch tool to find current best practices:

Queries to execute:
1. "<detected-framework> CLAUDE.md best practices 2025"
2. "<detected-language> project conventions Claude AI"
3. "CLAUDE.md examples <project-type>"
4. "<framework> coding standards documentation"

For each relevant result:
Use WebFetch to extract key recommendations.

Compile:
- Framework-specific best practices
- Common CLAUDE.md patterns
- Industry coding standards
- Testing recommendations
```

### Step 4: Generate CLAUDE.md Content

```
Launch content-generator agent:

Model: claude-sonnet-4.5
Context: isolated

Prompt:
"Generate a comprehensive CLAUDE.md file based on:

Codebase Analysis:
$CODEBASE_ANALYSIS

Best Practices Research:
$WEB_RESEARCH_RESULTS

Create CLAUDE.md with these sections:

# Project Overview
- Brief description of what this project does
- Main technologies and frameworks

# Development Setup
- Prerequisites
- Installation commands
- Environment setup

# Architecture
- Project structure explanation
- Key directories and their purposes
- Important patterns used

# Code Conventions
- Naming conventions (files, classes, functions, variables)
- Import ordering
- Code formatting rules

# Testing
- How to run tests
- Test file naming conventions
- Test organization

# Common Tasks
- Build commands
- Lint commands
- Deployment steps

# Claude-Specific Guidelines
- How Claude should approach this codebase
- Important files to read first
- Patterns to follow
- Patterns to avoid

Make it:
1. Specific to this actual codebase
2. Actionable with concrete examples
3. Concise but comprehensive
4. Easy to scan with clear headers"
```

### Step 5: Validate Generated Content

```
Verify the generated CLAUDE.md:

1. Check all referenced files/paths exist
2. Verify commands are correct for detected build system
3. Ensure conventions match actual codebase patterns
4. Remove any placeholder content

If issues found:
  Fix references and paths to match reality.
```

### Step 6: Write CLAUDE.md

```
If --update and existing CLAUDE.md:
  Merge new content with existing, preserving custom sections.
  Mark updated sections with timestamp.
Else:
  Write new CLAUDE.md to project root.

Include at bottom:
---
Generated by Claude MD Generator
Last updated: <timestamp>
```

### Step 7: Summary Output

```
Provide summary:

✨ CLAUDE.md Generated Successfully

📊 Analysis Summary:
   - Project Type: <type>
   - Tech Stack: <stack>
   - Architecture: <pattern>

📝 Sections Created:
   - Project Overview
   - Development Setup
   - Architecture
   - Code Conventions
   - Testing
   - Common Tasks
   - Claude-Specific Guidelines

🌐 Best Practices Sources:
   - <source 1>
   - <source 2>
   - <source 3>

📄 File: ./CLAUDE.md

💡 Tip: Review and customize the generated file for your specific workflow.
```

---

## Section Templates

### Minimal Template (--minimal)

```markdown
# Project: <name>

<one-line description>

## Quick Start
<setup commands>

## Commands
<common commands>

## For Claude
<key guidelines>
```

### Full Template

```markdown
# <Project Name>

## Overview
<description>

## Tech Stack
- **Language**:
- **Framework**:
- **Build Tool**:
- **Test Framework**:

## Getting Started

### Prerequisites
<list>

### Installation
<commands>

### Environment Setup
<env vars, config files>

## Project Structure
<tree or explanation>

## Architecture

### Pattern
<detected architectural pattern: Clean Architecture / MVC / etc.>

### Key Layers
- **Domain/Core**: <purpose and location>
- **Application**: <purpose and location>
- **Infrastructure**: <purpose and location>
- **Presentation**: <purpose and location>

### Design Patterns in Use
| Pattern | Usage | Example |
|---------|-------|---------|
| Repository | Data access abstraction | `UserRepository.java` |
| Factory | Object creation | `PaymentProcessorFactory.ts` |
| Strategy | Interchangeable algorithms | `PricingStrategy` |

## Code Style

### Naming Conventions
| Element | Convention | Example |
|---------|------------|---------|
| Classes | PascalCase | `UserService` |
| Functions | camelCase | `getUserById` |
| Variables | camelCase | `isActive` |
| Constants | SCREAMING_SNAKE | `MAX_RETRY_COUNT` |
| Files | kebab-case | `user-service.ts` |
| Test Files | *.test.ts | `user-service.test.ts` |

### Import Organization
1. Standard library imports
2. External dependencies
3. Internal absolute imports
4. Relative imports

### Formatting Rules
- Indentation: <spaces/tabs> (<size>)
- Line length: <max chars>
- Quotes: <single/double>
- Semicolons: <always/never>
- Trailing commas: <always/never/es5>

## Build System

### Build Tool: <Gradle/Maven/npm/etc.>

### Available Tasks

#### Development
| Command | Description |
|---------|-------------|
| `<cmd>` | Start dev server |
| `<cmd>` | Watch mode |

#### Build
| Command | Description |
|---------|-------------|
| `<cmd>` | Clean build |
| `<cmd>` | Production build |

#### Testing
| Command | Description |
|---------|-------------|
| `<cmd>` | Run all tests |
| `<cmd>` | Run unit tests |
| `<cmd>` | Run integration tests |
| `<cmd>` | Run with coverage |

#### Quality
| Command | Description |
|---------|-------------|
| `<cmd>` | Run linter |
| `<cmd>` | Auto-format code |
| `<cmd>` | Type checking |

#### Deployment
| Command | Description |
|---------|-------------|
| `<cmd>` | Build artifact |
| `<cmd>` | Publish/deploy |

## Testing

### Test Organization
<where tests live, naming patterns>

### Running Tests
<primary test command>

### Test Patterns
- Unit tests: <pattern>
- Integration tests: <pattern>
- E2E tests: <pattern>

## Claude Guidelines

### When Working on This Codebase
1. Always run <lint command> before committing
2. Follow the <architecture> pattern for new features
3. Place new code in appropriate layer

### Important Files to Read First
| File | Purpose |
|------|---------|
| `<file>` | <what it does> |

### Patterns to Follow
- Use dependency injection via <method>
- Create interfaces for external dependencies
- Follow <pattern> for error handling

### Anti-patterns to Avoid
- Direct database calls outside repositories
- Business logic in controllers
- Circular dependencies between modules

### Code Examples

#### Creating a New Service
\`\`\`<lang>
// Example of correct service structure
\`\`\`

#### Adding a New Endpoint
\`\`\`<lang>
// Example of correct endpoint structure
\`\`\`
```

---

## Tech Stack Detection

### JavaScript/TypeScript

**Detection Files**: package.json, tsconfig.json, .nvmrc, .node-version

**Frameworks**:
- React, Vue, Angular, Svelte, Solid
- Next.js, Nuxt, Remix, Astro
- Express, Fastify, NestJS, Hono

**Build Tools & Package Managers**:
| Tool | Config File | Task Discovery |
|------|-------------|----------------|
| npm | package.json | Read `scripts` section |
| yarn | yarn.lock, .yarnrc.yml | Read `scripts` section |
| pnpm | pnpm-lock.yaml, pnpm-workspace.yaml | Read `scripts` section |
| bun | bun.lockb | Read `scripts` section |
| Vite | vite.config.ts | `vite`, `vite build`, `vite preview` |
| Webpack | webpack.config.js | `webpack`, `webpack serve` |
| esbuild | esbuild.config.js | Custom scripts |
| Turbo | turbo.json | `turbo run <task>` |
| Nx | nx.json | `nx run <project>:<target>` |

**Style Config**:
- .eslintrc.* / eslint.config.js
- .prettierrc.* / prettier.config.js
- .stylelintrc.*

**Common Tasks**:
```
npm run dev          # Development server
npm run build        # Production build
npm run test         # Run tests
npm run lint         # Lint code
npm run format       # Format code
npm run typecheck    # Type checking
```

---

### Java

**Detection Files**: build.gradle, build.gradle.kts, pom.xml, settings.gradle

**Frameworks**:
- Spring Boot, Spring Cloud
- Quarkus, Micronaut, Helidon
- Jakarta EE, Vert.x

**Build Tools**:

#### Gradle
| Config File | Wrapper | Task Discovery |
|-------------|---------|----------------|
| build.gradle | ./gradlew | `./gradlew tasks --all` |
| build.gradle.kts | ./gradlew | `./gradlew tasks --all` |
| settings.gradle | | Lists subprojects |

**Common Gradle Tasks**:
```
./gradlew build              # Full build
./gradlew clean              # Clean build outputs
./gradlew test               # Run tests
./gradlew check              # Run all checks (tests + quality)
./gradlew bootRun            # Run Spring Boot app
./gradlew assemble           # Build without tests
./gradlew dependencies       # Show dependencies
./gradlew dependencyUpdates  # Check for updates (if plugin installed)

# Multi-module
./gradlew :module:build      # Build specific module
./gradlew -p module test     # Test specific module

# Quality
./gradlew checkstyleMain     # Run Checkstyle
./gradlew spotbugsMain       # Run SpotBugs
./gradlew spotlessCheck      # Check formatting
./gradlew spotlessApply      # Apply formatting
./gradlew jacocoTestReport   # Generate coverage report
```

#### Maven
| Config File | Wrapper | Task Discovery |
|-------------|---------|----------------|
| pom.xml | ./mvnw | Parse pom.xml phases/plugins |

**Common Maven Commands**:
```
./mvnw clean install         # Clean build + install to local repo
./mvnw package               # Build JAR/WAR
./mvnw test                  # Run tests
./mvnw verify                # Run integration tests
./mvnw spring-boot:run       # Run Spring Boot app
./mvnw dependency:tree       # Show dependencies
./mvnw versions:display-dependency-updates  # Check updates

# Profiles
./mvnw -P production package # Build with profile
./mvnw -DskipTests package   # Skip tests

# Quality
./mvnw checkstyle:check      # Run Checkstyle
./mvnw spotbugs:check        # Run SpotBugs
./mvnw jacoco:report         # Generate coverage
```

**Style Config**:
- checkstyle.xml, checkstyle-suppressions.xml
- spotbugs-exclude.xml
- .editorconfig
- google-java-format (via plugin)

---

### Kotlin

**Detection Files**: build.gradle.kts, *.kt files

**Additional Tools**:
```
./gradlew ktlintCheck        # Check Kotlin style
./gradlew ktlintFormat       # Format Kotlin code
./gradlew detekt             # Static analysis
```

**Style Config**: .editorconfig, .ktlint, detekt.yml

---

### Python

**Detection Files**: pyproject.toml, setup.py, requirements.txt, Pipfile, poetry.lock

**Frameworks**:
- Django, FastAPI, Flask, Starlette
- SQLAlchemy, Pydantic, Celery

**Build Tools & Package Managers**:

#### Poetry
```
poetry install               # Install dependencies
poetry run python app.py     # Run with venv
poetry run pytest            # Run tests
poetry build                 # Build package
poetry publish               # Publish to PyPI
poetry update                # Update dependencies
poetry show --outdated       # Check outdated deps
```

#### pip + venv
```
python -m venv .venv         # Create venv
source .venv/bin/activate    # Activate (Unix)
pip install -r requirements.txt
pip install -e .             # Editable install
python -m pytest             # Run tests
```

#### uv (fast Python package installer)
```
uv venv                      # Create venv
uv pip install -r requirements.txt
uv run pytest                # Run tests
```

#### Hatch
```
hatch env create             # Create environment
hatch run test               # Run tests
hatch build                  # Build package
hatch fmt                    # Format code
```

**Common Tasks**:
```
pytest                       # Run tests
pytest --cov                 # With coverage
python -m mypy .             # Type checking
ruff check .                 # Lint (fast)
ruff format .                # Format (fast)
black .                      # Format
flake8                       # Lint
isort .                      # Sort imports
```

**Style Config**:
- pyproject.toml [tool.black], [tool.ruff], [tool.mypy], [tool.pytest]
- .flake8, setup.cfg
- .pre-commit-config.yaml

---

### Go

**Detection Files**: go.mod, go.sum

**Frameworks**:
- Gin, Echo, Fiber, Chi
- GORM, sqlx
- Standard library net/http

**Build & Tasks**:
```
go build ./...               # Build all
go run .                     # Run main
go test ./...                # Run all tests
go test -v ./...             # Verbose tests
go test -cover ./...         # With coverage
go test -race ./...          # Race detection
go mod tidy                  # Clean dependencies
go mod download              # Download deps
go vet ./...                 # Static analysis
golangci-lint run            # Comprehensive linting
go generate ./...            # Run generators
```

**Style Config**:
- .golangci.yml
- .editorconfig

**Task Runners** (if present):
- Makefile: `make build`, `make test`, `make lint`
- Taskfile.yml: `task build`, `task test`
- Mage: `mage build`, `mage test`

---

### Rust

**Detection Files**: Cargo.toml, Cargo.lock

**Frameworks**:
- Actix-web, Axum, Rocket, Warp
- Tokio, async-std
- Diesel, SQLx, SeaORM

**Cargo Commands**:
```
cargo build                  # Debug build
cargo build --release        # Release build
cargo run                    # Build and run
cargo test                   # Run tests
cargo test -- --nocapture    # With output
cargo check                  # Fast type check
cargo clippy                 # Lint
cargo fmt                    # Format
cargo doc --open             # Generate docs
cargo update                 # Update deps
cargo outdated               # Check outdated (cargo-outdated)
cargo audit                  # Security audit (cargo-audit)
cargo bench                  # Run benchmarks
```

**Style Config**:
- rustfmt.toml
- clippy.toml
- .cargo/config.toml

---

### .NET (C#/F#)

**Detection Files**: *.csproj, *.fsproj, *.sln, Directory.Build.props

**Frameworks**:
- ASP.NET Core, Blazor
- Entity Framework Core
- MediatR, AutoMapper

**.NET CLI Commands**:
```
dotnet build                 # Build solution
dotnet run                   # Run project
dotnet test                  # Run tests
dotnet watch run             # Hot reload
dotnet publish               # Publish for deployment
dotnet restore               # Restore packages
dotnet format                # Format code
dotnet tool restore          # Restore tools
```

**Style Config**:
- .editorconfig
- Directory.Build.props
- stylecop.json

---

### PHP

**Detection Files**: composer.json, composer.lock

**Frameworks**:
- Laravel, Symfony, Slim
- Doctrine, Eloquent

**Composer Commands**:
```
composer install             # Install deps
composer update              # Update deps
composer require <pkg>       # Add dependency
php artisan serve            # Laravel dev server
php artisan test             # Laravel tests
./vendor/bin/phpunit         # Run tests
./vendor/bin/phpstan         # Static analysis
./vendor/bin/php-cs-fixer fix  # Format
```

---

### Ruby

**Detection Files**: Gemfile, Gemfile.lock, Rakefile

**Frameworks**:
- Rails, Sinatra, Hanami
- ActiveRecord, Sequel

**Commands**:
```
bundle install               # Install gems
rails server                 # Dev server
rails test                   # Run tests
bundle exec rspec            # RSpec tests
bundle exec rubocop          # Lint
bundle exec rubocop -a       # Auto-fix
rake db:migrate              # Database migrations
```

---

## Build Tool Task Discovery Commands

Quick reference for discovering available tasks:

| Build Tool | Discovery Command |
|------------|-------------------|
| Gradle | `./gradlew tasks --all` |
| Maven | `./mvnw help:describe -Dcmd=compile` |
| npm | `npm run` (lists scripts) |
| yarn | `yarn run` (lists scripts) |
| pnpm | `pnpm run` (lists scripts) |
| Cargo | `cargo --list` |
| Go + Make | `make help` or read Makefile |
| Poetry | `poetry run --help` |
| .NET | `dotnet --help` |
| Composer | `composer list` |
| Rake | `rake -T` |
| Nx | `nx show projects` |
| Turbo | Read turbo.json |

---

## Error Handling

- **No recognizable project**: Generate minimal template with placeholders
- **Web search fails**: Continue with codebase analysis only
- **Conflicting conventions**: Prioritize explicit config over inference
- **Monorepo detected**: Generate overview with links to subproject docs

---

## Preferred MCP Tools

When available, use these MCP tools instead of Bash equivalents:

| Task | Use MCP Tool | Instead of |
|------|--------------|------------|
| Gradle tasks | Gradle MCP `list_tasks` | `./gradlew tasks` |
| Code analysis | `semantic_search` | Manual exploration |
| Docker detection | `list_containers` | `docker` CLI |
