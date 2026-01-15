# Shared MCP Servers

Provides 8 MCP servers for use across all marketplace plugins. Once enabled, Claude automatically discovers and can use these tools.

## MCP Servers Included

| MCP | Purpose | Key Tools |
|-----|---------|-----------|
| **Git** | Version control | `git_status`, `git_commit`, `git_diff`, `git_log`, `git_blame`, `git_branch` |
| **Semgrep** | Security scanning | `semgrep_scan`, `security_check`, `get_abstract_syntax_tree` |
| **DBHub** | Multi-database access | `execute_sql`, `search_objects` (PostgreSQL, MySQL, SQLite, SQL Server) |
| **Maven** | Dependency versions | `check_version` - lookup latest versions from Maven Central |
| **Gradle** | Build automation | `list_tasks`, `run_task`, `dependencies` |
| **Kubernetes** | Cluster management | 22 tools: pod exec/logs, resource CRUD, helm, port-forward |
| **Docker** | Container management | `create_container`, `list_containers`, `deploy_compose`, `get_logs` |
| **Code Context** | Semantic code search | Codebase indexing, ~40% token reduction, 12+ language support |

## Prerequisites

Install these before using the MCPs:

| Requirement | For MCP | Install |
|-------------|---------|---------|
| Node.js 18+ | Git, DBHub, Maven, Gradle, K8s, Code Context | `brew install node` |
| Python 3.12+ & uv | Semgrep, Docker | `brew install uv` |
| Docker Desktop | Docker MCP | [docker.com](https://docker.com) |
| kubectl + kubeconfig | Kubernetes MCP | `brew install kubectl` |
| JDK 17+ | Gradle MCP | `brew install openjdk` |

## Environment Variables

Some MCPs require configuration:

```bash
# Database (DBHub)
export DB_URL="postgresql://user:pass@localhost:5432/db"

# Code Context (for semantic search)
export EMBEDDING_PROVIDER="openai"  # or "ollama" for local
export OPENAI_API_KEY="sk-..."
export MILVUS_URI="http://localhost:19530"  # optional, for Milvus vector DB
```

## Usage

After enabling this plugin, restart Claude Code. The MCP tools become available automatically.

Check status:
```
/mcp
```

## MCP Tool Examples

### Git
```
Use git_status to check repository state
Use git_commit to create commits
Use git_diff to see changes
```

### Semgrep
```
Use semgrep_scan to scan code for security vulnerabilities
Use security_check for quick OWASP Top 10 detection
```

### Database
```
Use execute_sql to run queries
Use search_objects to explore schema
```

### Kubernetes
```
Use kubernetes tools to list pods, get logs, exec into containers
```

### Docker
```
Use list_containers to see running containers
Use deploy_compose to deploy docker-compose stacks
```

### Code Context
```
Use semantic_search to find code by meaning, not just keywords
Reduces token usage by ~40% compared to reading full files
```

## JetBrains Integration

JetBrains MCP is built into IntelliJ IDEA 2025.2+. Enable via:
`Settings > Tools > MCP Server > Enable`

No need to bundle - it's native to the IDE.
