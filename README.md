# Claude Code Productivity Suite

Enterprise-grade Claude Code plugin marketplace with intelligent multi-agent orchestration for feature development, bug fixing, refactoring, idea exploration, and dependency management.

## 🚀 Quick Start

### Installation

```bash
# Add this marketplace to Claude Code
/plugin marketplace add mnuetzel/playing-with-claude

# Install all plugins
/plugin install feature-orchestrator@mnuetzel-productivity
/plugin install bug-fixer@mnuetzel-productivity
/plugin install refactor-pro@mnuetzel-productivity
/plugin install idea-lab@mnuetzel-productivity
/plugin install upgrade-deps@mnuetzel-productivity
```

### Usage

```bash
# Develop a new feature with intelligent agent orchestration
/feature "Add user authentication with JWT tokens"

# Fix a bug with systematic investigation
/fix-bug "Users report login failing intermittently"

# Refactor code safely with metrics tracking
/refactor --simplify "src/services/OrderService.java"

# Explore an idea before implementation
/idea "Real-time notifications system"

# Upgrade dependencies safely
/upgrade-deps --dry-run
```

## 📦 Plugins

### Feature Orchestrator (`/feature`)

Intelligent feature development with multi-agent orchestration. Claude automatically selects which agents to use based on feature complexity.

**Agents Used:**
- `architect` - System design and Clean Architecture
- `senior-developer` - Production-ready implementation
- `test-wizard` - TDD and test strategy
- `security-expert` - OWASP compliance and vulnerability detection
- `clean-code-nerd` - Code readability and maintainability
- `requirements-engineer` - Domain analysis and acceptance criteria

**Flags:**
- `--fast` - Skip requirements, go straight to implementation
- `--parallel` - Run compatible agents simultaneously
- `--secure` - Force security review
- `--yolo` - Chaos mode (not recommended 😅)

### Bug Fixer (`/fix-bug`)

Systematic bug investigation with root cause analysis and regression test generation.

**Features:**
- Automatic reproduction steps
- Git bisect integration
- Similar bug detection
- Regression test creation

**Flags:**
- `--hotfix` - Minimal fix, skip analysis
- `--investigate-only` - Find cause without fixing
- `--security` - Treat as security issue

### Refactor Pro (`/refactor`)

Safe refactoring with before/after metrics, architectural analysis, and automatic test verification.

**Features:**
- Git checkpoint for safe rollback
- Complexity metrics tracking
- Incremental changes with verification
- Code smell detection

**Flags:**
- `--extract` - Extract method/class/module
- `--simplify` - Reduce complexity
- `--pattern` - Apply design pattern
- `--clean-arch` - Enforce Clean Architecture
- `--dry-run` - Preview without executing

### Idea Lab (`/idea`)

Design research phase for exploring ideas before technical implementation.

**Agents Used:**
- `product-visionary` - Business value and user needs
- `ux-researcher` - User experience and workflows

**Features:**
- Web research for competitive analysis
- User journey mapping
- Technical feasibility assessment
- Handoff to `/feature` when ready

**Flags:**
- `--quick` - Fast concept sketch
- `--deep` - Comprehensive research
- `--competitive` - Include competitive analysis

### Upgrade Deps (`/upgrade-deps`)

Safe dependency upgrades with breaking change detection and security scanning.

**Supported Build Systems:**
- Gradle (Kotlin DSL, Groovy)
- Maven
- npm/pnpm/yarn
- Python (pip, poetry)

**Features:**
- CVE and security advisory scanning
- Breaking change detection via web research
- Incremental upgrades with test verification
- Automatic rollback on failure

**Flags:**
- `--dry-run` - Show plan without executing
- `--security-only` - Only security patches
- `--major` - Include major version upgrades
- `--lib <name>` - Upgrade specific library

## 🏗️ Architecture

All plugins generate plans in `<project>/cc-plans/` directory:

```
cc-plans/
├── features/
│   └── user-auth-jwt.md
├── bugs/
│   └── login-intermittent.md
├── refactors/
│   └── order-service-simplify.md
├── ideas/
│   └── realtime-notifications.md
└── upgrades/
    └── 2026-01-15.md
```

## 🔧 Agent Philosophy

All agents follow a **professional, technical style**:
- Clear, structured output
- Actionable recommendations
- Metrics and evidence-based decisions
- No personality quirks - pure expertise

## 💰 Cost Optimization

The plugins use intelligent model selection:
- **Opus** - Complex architecture, novel problems (rare)
- **Sonnet** - Requirements, security review, analysis
- **Haiku** - Implementation, code review, tests (90% of work)

Expected cost reduction: **70-85%** compared to using Opus for everything.

## 🛡️ Safety Features

1. **Git checkpoints** before changes
2. **Test verification** after each step
3. **Automatic rollback** on failure
4. **Breaking change detection**
5. **Security scanning** for vulnerabilities

## 📚 Documentation

- [Getting Started](docs/getting-started.md)
- [Agent Guide](docs/agent-guide.md)

## 🤝 Contributing

Contributions welcome! Please submit issues and pull requests.

## 📄 License

MIT License - See LICENSE file for details.

---

Made with ❤️ for the Claude Code community
